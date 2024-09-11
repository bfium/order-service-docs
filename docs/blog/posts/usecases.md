---
date:
  created: 2024-08-11
  updated: 2024-08-16
categories:
  - IT
#draft: true
readtime: 5
# pin: false

links: #  further reading section
  - blog/index.md#specifications
  - blog/index.md#requirements

tags:
  - IT
slug: use-case

authors:
  - bfium
---

# Structured resource URLs with HTTP methods
We use HTTP methods in combination with URLs.
<!-- more -->

## How do we use HTTP methods to define the endpoints of the orders API? 

HTTP methods are special keywords used in HTTP requests to indicate the type of action we want to perform in the server. 
Proper use of HTTP methods makes our APIs more structured and elegant, and since they’re part of the HTTP protocol, they also make the API more understandable and easier to use.

The most relevant HTTP methods in REST APIs are GET, POST, PUT, PATCH, and DELETE:

- GET—Returns information about the requested resource 
- POST—Creates a new resource
- PUT—Performs a full update by replacing a resource
- DELETE—Deletes a resource

## Using HTTP status codes to create expressive HTTP responses
We use status codes to signal the result of processing a request in the server.
Status codes fall into the following five groups:

- 1xx group—Signals that an operation is in progress 
- 2xx group—Signals that a request was successfully processed 
- 3xx group—Signals that a resource has been moved to a new location 
- 4xx group—Signals that something was wrong with the request 
- 5xx group—Signals that there was an error while processing the request

### Successful HTTP status codes for the Order Service

- POST /orders: 201 (Created)—Signals that a resource has been created. 
- GET /orders: 200 (OK)—Signals that the request was successfully processed. 
- GET /orders/{order_id}: 200 (OK)—Signals that the request was successfully processed. 
- PUT /orders/{order_id}: 200 (OK)—Signals that the resource was successfully updated. 
- DELETE /orders/{order_id}: 204 (No Content)—Signals that the request was
successfully processed but no content is delivered in the response. 
Contrary to all other methods, a DELETE request doesn’t require a response with payload
- POST /orders/{order_id}/cancel: 200 (OK)—Although this is a POST endpoint, we use the 200 (OK) status code since we’re not really creating a resource, and all the client wants to know is that the cancellation was successfully processed. 
- POST /orders/{order_id}/pay: 200 (OK)—Although this is a POST endpoint, we use the 200 (OK) status code since we’re not really creating a resource, and all the client wants to know is that the payment was successfully processed.

### Unsuccessful HTTP status codes for the Order Service
We distinguish two groups of errors:

- Errors made by the user when sending the request, for example, due to a malformed payload, or due to the request being sent to a nonexistent endpoint. We address this type of error with an HTTP status code in the 4xx group. 
- Errors unexpectedly raised in the server while processing the request, typically due to a bug in our code. We address this type of error with an HTTP status code in the 5xx group.

#### Using HTTP status codes to report client errors in the request
Payloads with invalid syntax are payloads that the server can neither parse nor under- stand. A typical example of a payload with invalid syntax is malformed JSON.

- we address this type of error with a 400 (Bad Request) status code.

Unprocessable entities are syntactically valid payloads that miss a required parameter, contain invalid parameters, or assign the wrong value or type to a parameter.

- We address this type of error with the 422 (Unprocessable Entity) status code, which signals that something was wrong with the request and it couldn’t be processed.

If a client uses that endpoint with a nonexistent order ID, we should respond with an HTTP status code signaling that the order doesn’t exist.

- we address this error with the 404 (Not Found) status code, which signals that the requested resource is not available or couldn’t be found.

if a user sent a PUT request on the /orders endpoint, we must tell them that the PUT method is not supported on that URL path. There are two HTTP status codes we can use to address this situation. 

- we can return a 501 (Not Implemented) if the method hasn’t been implemented but will be available in the future (i.e., we have a plan to implement it).
- If the requested HTTP method is not available, and we don’t have a plan to implement it, we respond with the 405 (Method Not Allowed) status code

Two common errors in API requests have to do with authentication and authorization. The first happens when a client sends an unauthenticated request to a protected endpoint. In that case, we must tell them that they should first authenticate.

- we address this situation with the 401 (Unauthorized) status code, which signals that the user hasn’t been authenticated.

The second error happens when a user is correctly authenticated and tries to use an endpoint or a resource they are not authorized to access. An example is a user trying to access the details of an order that doesn’t belong to them. 

- we address this scenario with the 403 (Forbidden) status code, which signals that the user doesn’t have permissions to access the requested resource or to perform the requested operation.

#### Using HTTP status codes to report errors in the server
when our application crashes unexpectedly due to a bug. 

- we respond with a 500 (Internal Server Error) status code

when the server is unable to take on new connections, 

- we must respond with a 503 (Service Unavailable) status code, which signals that the server is overloaded or down for maintenance and therefore cannot service additional requests.









