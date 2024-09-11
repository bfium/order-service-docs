---
date:
  created: 2024-08-11
  updated: 2024-08-16
#draft: true
readtime: 5
# pin: false
links:
  - Homepage: index.md
  - Blog index: blog/index.md

authors:
  - bfium

tags:
  - IT
---

# Architectural constraints of REST applications
These constraints were enumerated by Fielding, and they specify how a server should process and respond to a client request.
<!-- more -->

- `Client-server architecture`: The user interface (UI) must be decoupled from the backend.
- `Statelessness`: The server must not manage states between requests.
- `Cacheability`: Requests that always return the same response must be cacheable.
- `Layered system`: The API may be architected in layers, but such complexity must be hidden from the user(Using the Coordinator pattern).
- `Code on demand`: The server can inject code into the user interface on demand.
- `Uniform interface`: The API must provide a consistent interface for accessing and manipulating resources.

### The client-server architecture principle
REST relies on the principle of separation of concerns, and consequently it requires that user interfaces are decoupled from data storage and server logic. 
This allows server-side components to evolve independently from UI elements.

### The statelessness principle
In REST, every request to the server must contain all the information necessary to pro- cess it. In particular, the server must not keep state from one request to the next.
removing state management from server components makes it easier to scale the backend horizontally. 
This allows us to deploy multiple instances of the server, and because none of those instances manages the API client’s state, the client can communicate with any of them.


### The cacheability principle
When applicable, server responses must be cached. 
Caching improves the performance of APIs because it means we don’t have to perform all the calculations required to serve a response again and again. 

`GET` requests are suitable for caching, since they return data already saved in the server.

by caching a GET request, we avoid having to fetch data from the source every time a user requests the same information. 
The longer it takes to assemble the response for a GET request, the greater the benefits of caching it.

The orders service interfaces with the kitchen service to obtain information on the order’s progress. 
To save time the next time the customer checks the order’s status, we cache its value for a short period of time.

### The layered system principle
In a REST architecture, clients must have a unique point of entry to your API and must not be able to tell whether they are connected directly to the end server or to an intermediary layer such as a load balancer. 
You can deploy different components of a server-side application in different servers, or you can deploy the same component across different servers for redundancy and scalability. 
This complexity should be hidden from the user by exposing a single endpoint that encapsulates access to your services.

### The code-on-demand principle
Servers can extend the functionality of a client application by sending executable code directly from the backend, such as JavaScript files needed to run a UI. 
This constraint is optional and only applies to applications in which the backend serves the client interface.

### The uniform interface principle
REST applications must expose a uniform and consistent interface to their consumers. 
The interface must be documented, and the API specification must be followed strictly by the server and the client. 
Individual resources are identified by a Uniform Resource Identifier (URI),4 and each URI must be unique and always return the same resource. 

For example, the URI /orders/8 represents an order with ID 8, and a GET request on this URI always returns the state of the order with ID 8. 
If the order is deleted from the system, the ID must not be reused to represent a different order.






