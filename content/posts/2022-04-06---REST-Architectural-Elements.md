---
title: "REST Architectural Elements"
date: "2022-04-017T22:20:10.284Z"
template: "post"
draft: false
slug: "rest"
category: "Development"
tags:
  - "REST"
  - "Elements"
  - "Architecture"
description: "Processing, Data, Connecting elements."
socialImage: ""
---

### Processing elements(Components)

→ focus on the roles of components, constrains on their interaction and their interpretation of significant data elements

### Data elements

hypermedia architecture features

→ often move processing entity instead of moving data to processor.

options 

1. render the data where it is located and send a fixed-format image to the recipient
    
    → the traditional client/server style (client implementation easier, but restrict)
    
2. encapsulate the data with a rendering engine and send both to the recipient
    
    → the mobile object style (while enabling processing of the data, but limiting the functionality of the recipient to what is anticipated within the unique engine)
    
3. send the raw data to the recipient along with metadata that describes the data type, so that the recipient can choose their own rendering engine
    
    → while minimizing the bytes transferred, but not hiding information and requiring both sender and recipient to understand the same data types
    

REST architecture - hybrid of the hypermedia architecture features

- client/server and mobile object style advantages
- focus on a shared understanding of data types with metadata
    
    → communicate by transferring a **representation of the data in a format matching one** of evolving **set of standard data types**, selected dynamically based on the capabilities or desires of the recipient and the nature of the data. 
    
- limit the scope of what is revealed (the separation of concerns)
    
    → through an interface hiding implementation
    

Table 1: REST Data Elements

| Data Element | Modern Web Examples |
| --- | --- |
| resource  | the intended conceptual target of a
hypertext reference |
| resource identifier | identifier URL, URN |
| representation  | HTML document, JPEG image |
| representation
metadata | media type, last-modified time |
| resource 
metadata  | source link, alternates, vary |
| control data  | if-modified-since, cache-control |

**Resources and Resource Identifiers**

- A resource: a document, a image, a temporal service
- resource identifier: identify the particular resource

**Representations and Representation metadata**

- a representation: sequence of bytes
- a representation metadata: describe those bytes
- includes document, file, and HTTP message entity, instance or variant
- consists of data, metadata describing the data (name-value pairs)
- media type: data format for a representation  → incrementally render a large HTML document
- 

**Control Data**

- defines the purpose of a message

### Connecting elements(Connectors)

encapsulate the activities of accessing resources and transferring resource representations

**STATELESS Features**

Each request contains all of the information necessary for a connector to understand the request.

1. removes any need for the connectors to retain application state between requests
    
    → reduces consumption of physical resources and improves scalability
    
2. allows interactions to be processed in parallel without requiring that the processing mechanism understand the interaction semantics
3. allows an intermediary to view and understand a request in isolation 
4. forces all of the information that might factor into the reusability of a cached response to be present in each request
application-level protocol

generic, stateless, protocol for hypertext

***References***

*Principled Design of the Modern Web Architecture*

https://dl.acm.org/doi/pdf/10.1145/337180.337228