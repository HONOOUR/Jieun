---
title: "Algorithm Solution - Simulation"
date: "2021-08-18T22:10:10.284Z"
template: "post"
draft: false
slug: "solution-simulation"
category: "Algorithm"
tags:
  - "Algorithm"
  - "Simulation"
  - "Python"
  - "Programmers"
description: "모든 과정 제시, 그 과정을 거쳐 나온 결과를 추론하는 문제"
socialImage: ""
---

### 셔틀버스

[https://programmers.co.kr/learn/courses/30/lessons/17678?language=python3](https://programmers.co.kr/learn/courses/30/lessons/17678?language=python3)

1. All of crews is in the line later than last shuttle
2. Some or All of crews is in the line earlier than last shuttle

   2-1. More crews than limited seats

   2-1. Less crews than limited seats

**SOLUTION 1**

1. Sort timetable(crew's)
2. Check if the first crew arrives earlier than the last shuttle

   1. False → All of crews is in the line later than last shuttle (CASE 1)

      answer = last shuttle time

   2. True → Some or All of crews is in the line earlier than last shuttle (CASE 2)

3. If it's True, Check if a seat is left

   Take a seat for a crews in order if the crew is earlier or exactly arrive at the interval time (if not, go to the next interval)

   1. if there is More crews than limited seats (CASE 2-1)

      arrive 1 min earlier than the current crew when it is a last shuttle and last seat (seat left = 1, last interval)

   2. if there Less crews than limited seats (CASE 2-2)

      answer = last shuttle time

```python
def getTimeStamp(hh, mm):
    if mm < 0:
        mm = 60 + mm
        hh -= 1

    if hh < 10:
        answer = "0" + str(hh) + ":"
    else:
        answer = str(hh) + ":"
    if mm < 10:
        answer += "0" + str(mm)
    else:
        answer += str(mm)
    return answer

def getLastShuttleTime(number, interval):
    last_hh = 0
    last_mm = 0
    if interval*(number-1) >= 60:
        last_hh = 9 + (interval*(number-1)//60)
        last_mm = interval*(number-1)% 60
    else:
        last_hh = 9
        last_mm = interval*(number-1)
    return last_hh, last_mm

def getShuttleWaitingTime(n, t, m, timetable):
    number = n
    interval = t
    seats_num = m
    # sort crew's timetalbe
    timetable.sort()
    answer = ""
    last_hh, last_mm = getLastShuttleTime(number, interval)
    first_crew_hh, first_crew_mm = map(int, timetable[0].split(":"))
    # Check if the first crew arrives earlier than the last shuttle
    # True → Some or All of crews is in the line earlier than last shuttle (CASE 2)
    if first_crew_hh < last_hh or (first_crew_hh == last_hh and first_crew_mm <= last_mm):
        start_hh = 9
        start_mm = 0
        for c in range(number):
            seat_taken = 0
            # calculate interval time
            if interval*c >= 60:
                start_hh = 9 + (interval*c//60)
                start_mm = interval*c % 60
            else:
                start_hh = 9
                start_mm = interval*c

            seat_taken = 0
            while seat_taken < seats_num:
                # take a seat for a crew
                if len(timetable) > 0: # if there is More crews than limited seats (CASE 2-1)
                    crew_hh, crew_mm = map(int, timetable[0].split(":"))
                    if crew_hh < start_hh or (crew_hh == start_hh and crew_mm <= start_mm):
                        if c == number-1 and seat_taken == seats_num-1:
                            crew_mm -= 1
                            answer = getTimeStamp(crew_hh, crew_mm)
                            break
                        else:
                            seat_taken += 1
                            timetable.pop(0)
                else: # if there is More crews than limited seats (CASE 2-1)
                    answer = getTimeStamp(last_hh, last_mm)
                    break
    else: # False → All of crews is in the line later than last shuttle (CASE 1)
        answer = getTimeStamp(last_hh, last_mm)

    return answer
```

**SOLUTION 2**

1. Sort timetable
2. Create shuttle bus array with number and seats

   shuttle_timetable[number][seats]

   shuttle_timetable[2][2] = [[[shuttle_time, crew_time], [shuttle_time, crew_time]], [[shuttle_time, crew_time], [shuttle_time, crew_time]]]

3. From index 0 of crews' timetable and shuttle, input crew's time to shuttle array

   crews' time is less than or equal to shuttle time

```python
def getTimeStamp(hh, mm):
    if hh < 10:
        answer = "0" + str(hh) + ":"
    else:
        answer = str(hh) + ":"
    if mm < 10:
        answer += "0" + str(mm)
    else:
        answer += str(mm)
    return answer

def getShuttleTime(n, t, m, timetable):
    answer = ""
    shuttle_num = n
    interval = t
    seats_num = m
    start_time = 540 # 9:00

    shuttle_timetable = []
    for n in range(shuttle_num):
        for _ in range(seats_num):
            shuttle_timetable.append([start_time+(interval*n), -1]) # none in the bus seat

    crew_timetable = []
    for c_t in timetable:
        hh, mm = map(int, c_t.split(":"))
        crew_timetable.append(hh*60+mm)
    crew_timetable.sort()

    shuttle_index = 0
    crew_index = 0
    while shuttle_index < len(shuttle_timetable) and crew_index < len(crew_timetable):
        # crews are earlier than shuttle take seats (next crew, next seat)
				if crew_timetable[crew_index] <= shuttle_timetable[shuttle_index][0]:
            shuttle_timetable[shuttle_index][1] = crew_timetable[crew_index]
            shuttle_index += 1
            crew_index += 1
        else:
            shuttle_index += 1

    if shuttle_timetable[-1][1] == -1: # the last shuttle's seat is empty -> answer
        hh = shuttle_timetable[-1][0]//60
        mm = shuttle_timetable[-1][0]%60
        answer = getTimeStamp(hh, mm)
    else: # the time of the crew in the last shuttle's seat -1
        hh = (shuttle_timetable[-1][1]-1)//60
        mm = (shuttle_timetable[-1][1]-1)%60
        answer = getTimeStamp(hh, mm)

    return answer
```

</br>

### 기둥과 보 설치

[https://programmers.co.kr/learn/courses/30/lessons/60061](https://programmers.co.kr/learn/courses/30/lessons/60061)

1. item is [x, y, pillar or roof, remove or add]

   pillar = 0, roof 1

   remove = 0, add = 1

2. Initialize the structure of n by n array (but, 0 ~ n size if n+1)

   n+1 ← pillar is upward **structure[n][n] structure[n+1][n]**

   n+1 ← roof is left to right **structure[n][n] structure[n][n+1]**

3. Check the item from buil_frame array

   if it's add or remove

   - add: validate if it can be added.
   - remove: validate if it can be removed (answer should be all validated when it assume that the item is removed)

4. Create isRoofOrPillarValid()

   pillar conditions: floor (y == 0) or left or right roof (x-=1, x+=1)

   roof conditions: under the roof there is a pillar or roof on either left or right

5. Sort with lambda

   conditions in order

```python
pillar = 0
roof = 1

def isRoofOrPillarVaild(structure, x, y, a):
    if a == 1:
        if structure[y-1][x][pillar] == 0 or structure[y-1][x+1][pillar] == 0 or ((x >=1 and structure[y][x-1][roof] == 1) and structure[y][x+1][roof] == 1):
            return True
        return False
    else:
        if y == 0 or structure[y-1][x][pillar] == 0 or structure[y][x][roof] == 1 or (x >=1 and structure[y][x-1][roof] == 1):
            return True
        return False


def getStructureBuilt(n, build_frame):
    answer = []
    structure = []

    # initialize structure to -1
    for i in range(n+1):
        structure.append([])
        for _ in range(n+1):
            structure[i].append([-1,-1])

    for item in build_frame:
        x, y, a, b = item
        # add (1) or remove (0)
        if b == 1:
            # roof a == 1 -> left to right
            # pillar a == 0  -> updward
            if isRoofOrPillarVaild(structure, x, y, a):
                structure[y][x][a] = a
                answer.append([x, y, a])
        elif b == 0:
            structure[y][x][a] = -1
            removable = True
            for t_x, t_y, t_a in answer:
                if not isRoofOrPillarVaild(structure, t_x, t_y, t_a):
                    structure[y][x][a] = a
                    removable = False
                    break
            if removable:
                answer.remove([x, y, a])


    answer.sort(key=lambda i:(i[0], i[1], i[2]))
    return answer
```

</br>

### 삼각달팽이

[https://programmers.co.kr/learn/courses/30/lessons/68645](https://programmers.co.kr/learn/courses/30/lessons/68645)

```python
def getTriangleSnail(size):
    # 1. create array for answer with triangle shape
    # e.g. size=5
    #[0]
    #[0][0]
    #[0][0][0]
    #[0][0][0][0]
    #[0][0][0][0][0]


    # triangle = []
    # for s in range(1, size+1):
    #     triangle.append([0]*s)
    triangle = [[0] * s for s in range(1, size+1)]

    # 2. create start point
    x = 0
    y = -1
    num = 1
    answer = []

    # 3. repeat with times of size
    # 4. separate with direction down, right, up order
    for s in range(size):
        for _ in range(size-s):
            if s % 3 == 0:
            # down [++y][x]
                y += 1
            elif s % 3 == 1:
            # left to right [y][++x]
                x += 1
            else:
            # up [--y][--x]
                y -= 1
                x -= 1
            triangle[y][x] = num
            num += 1

    for i in range(len(triangle)):
        for num in triangle[i]:
            answer.append(num)
```
