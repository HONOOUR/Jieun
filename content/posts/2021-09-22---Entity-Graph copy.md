---
title: "Entity Graph"
date: "2021-09-22T15:10:10.284Z"
template: "post"
draft: false
slug: "entity-graph"
category: "Development"
tags:
  - "JAVA"
  - "Entity Graph"
  - "JPA"
description: "To define fetching plans programmatically using springboot-starter-data-jpa"
socialImage: ""
---

[https://medium.com/swlh/jpa-entity-graphs-with-spring-boot-30cb110ba4f8](https://medium.com/swlh/jpa-entity-graphs-with-spring-boot-30cb110ba4f8)

To define fetching plans programmatically using springboot-starter-data-jpa

## N+1 query problem

executes N additional query statements (to fetch the same data that could have been retrieved)

when executing the primary query

primary query : all studies

N additional query: tags, zones, managers, members

## Benefits

- Improve the performance when loading entities
- Query all the information from the database at once with JOIN strategy

## Basics

### Default Entity Graph - Lazy

unless the fetch attribute is set to javax.persistence.FetchType.EAGER

### Eager, Lazy

- attributes that are specified by attribute nodes of the entity graph are treated as FetchType.EAGER

  e.g. `@EntityGraph`로 정의한 findByPath를 사용하여 조회한 결과는 **EAGER** 모드가 적용

- attributes that are not specified are treated as FetchType.LAZY

  e.g. **스프링 데이터 JPA**가 제공해주는 findByPath

- 경우에 따라서 원하는 방식으로 **Fetching** 전략을 선택해서 가져올 수 있게 됨

- FETCH: 설정한 엔티티 애트리뷰트는 EAGER 패치 나머지는 LAZY 패치
- LOAD: 설정한 엔티티 애트리뷰트는 EAGER 패치 나머지는 기본 패치 전략 따름

### @NamedEntityGraph

override any annotation-based graphs with the same name

### Entity

- graph name: Study.withAll
- nodes to be loaded: tags, zones, managers, members
- One Study, N tags, zones, managers, ,members

```java
@NamedEntityGraph(name = "Study.withAll", attributeNodes = {
        @NamedAttributeNode("tags"),
        @NamedAttributeNode("zones"),
        @NamedAttributeNode("managers"),
        @NamedAttributeNode("members")})
@NamedEntityGraph(name = "Study.withTagsAndManagers", attributeNodes = {
        @NamedAttributeNode("tags"),
        @NamedAttributeNode("managers")})
@NamedEntityGraph(name = "Study.withZonesAndManagers", attributeNodes = {
        @NamedAttributeNode("zones"),
        @NamedAttributeNode("managers")})

@Entity
@Getter @Setter @EqualsAndHashCode(of = "id")
@Builder @AllArgsConstructor @NoArgsConstructor
public class Study {
		@Id @GeneratedValue
    private Long id;

		@ManyToMany
    private Set<Tag> tags = new HashSet<>();

    @ManyToMany
    private Set<Zone> zones = new HashSet<>();

		@ManyToMany
    private Set<Account> managers = new HashSet<>();

    @ManyToMany
    private Set<Account> members = new HashSet<>();
}
```

### Repository

Declare Interface

depending on the method

```java
@Transactional(readOnly = true)
public interface StudyRepository extends JpaRepository<Study, Long> {

		Study findByPath(String path)

		@EntityGraph(attributePaths = {"tags", "zones", "managers", "members"},type = EntityGraph.EntityGraphType.LOAD)
    Study findByPath(String path);

    @EntityGraph(attributePaths = { "tags", "managers" }, type = EntityGraph.EntityGraphType.LOAD)
    Study findStudyWithTagsByPath(String path);

		@EntityGraph(attributePaths = { "zones", "managers"}, type = EntityGraph.EntityGraphType.LOAD)
    Study findStudyWithZonesByPath(String path);

    @EntityGraph(attributePaths = "managers")
    Study findStudyWithManagersByPath(String path);

    @EntityGraph(attributePaths = "members")
    Study findStudyWithMembersByPath(String path);
}
```

### Service

```java
@Service
public clasee StudyService {
		public Study getStudyToUpdateTag(Account account, String path) {
        Study study = studyRepository.findStudyWithTagsByPath(path);
        checkIfExistingStudy(path, study);
        checkIfManager(account, study);
        return study;
    }

    public Study getStudyToUpdateZone(Account account, String path) {
        Study study = studyRepository.findStudyWithZonesByPath(path);
        checkIfExistingStudy(path, study);
        checkIfManager(account, study);
        return study;
    }

    public Study getStudyToUpdateStatus(Account account, String path) {
        Study study = studyRepository.findStudyWithManagersByPath(path);
        checkIfExistingStudy(path, study);
        checkIfManager(account, study);
        return study;
    }
}
```

### Controller

```java
public EventController {
		@DeleteMapping("/events/{id}")
    public String cancelEvent(@CurrentAccount Account account, @PathVariable String path, @PathVariable Long id) {
        Study study = studyService.getStudyToUpdateStatus(account, path);
        eventService.deleteEvent(eventRepository.findById(id).orElseThrow());
        return "redirect:/study/" + study.getEncodedPath() + "/events";
    }
}
```
