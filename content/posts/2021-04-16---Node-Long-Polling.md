---
title: Long Polling - Node.js
date: "2021-04-16T22:50:37.121Z"
template: "post"
draft: false
slug: "long-polling"
category: "Development"
tags:
  - "Node"
  - "Long Polling"
  - "Eloquent Javascript"
description: "long polling for server-client communication"
socialImage: ""
---

Eloquent JS - CH21 Project: Skill-Sharing Website
https://eloquentjavascript.net/21_skillsharing.html

문제: Server can not open connection to Client
<br />해결: long polling for server-client communication

## Long Polling

- clients continuously ask the server for new information using regular HTTP requests, and the server stalls its answer then it has nothing new to report

## Server

### Routing

request 를 적절한 함수에 전달한다

1. router component 를 만든다.
   <br />add 함수: GET, PUT, DELETE 등 새로운 핸들러를 등록할 수 있도록 한다.
   <br />resolve 함수: request 핸들러, path를 확인해서 요청을 수행한다.

2. router component 를 사용한다.
3. create a static file server with ecstatic([https://www.npmjs.com/package/ecstatic](https://www.npmjs.com/package/ecstatic))

## Client

### HTML (index.html)

found by the static file server(by ecstatic)

### Javascript (client side application)

**state change → action**
<br />state = {user, action}

**Rendering components**
<br />function for constructing the document

- change document property (not document attribute)

```jsx
function elt(type, props, ...children) {
  let dom = document.createElement(type);
  if (props) Object.assign(dom, props);
  for (let child of children) {
    if (typeof child != "string") dom.appendChild(child);
    else dom.appendChild(document.createTextNode(child));
  }
  return dom;
```

<br />

**Polling**
<br />keeps polling the server for /talks and calls a callback function

```jsx
async function pollTalks(update) {
  let tag = undefined;
  for (;;) {
    let response;
    try {
      response = await fetchOK("/talks", {
        headers: tag && { "If-None-Match": tag, Prefer: "wait=90" },
      });
    } catch (e) {
      console.log("Request failed: " + e);
      await new Promise((resolve) => setTimeout(resolve, 500));
      continue;
    }
    if (response.status == 304) continue;
    tag = response.headers.get("ETag");
    update(await response.json());
  }
}
```

pollTalk(): infinite loop for polling
<br />pollTalk(handleAction()) → handleAction() → fetchOK("talk/a_T, headers)

**Start the application**

```jsx
function runApp() {
  let user = localStorage.getItem("userName") || "Anon";
  let state, app;
  function dispatch(action) {
    state = handleAction(state, action);
    app.syncState(state);
  }

  pollTalks((talks) => {
    // polling the server
    if (!app) {
      state = { user, talks };
      app = new SkillShareApp(state, dispatch); // create an application
      document.body.appendChild(app.dom); // construct dom
    } else {
      dispatch({ type: "setTalks", talks }); // if an app is, action is handled
    }
  }).catch(reportError);
}

runApp();
```
