---
date:
  created: 2024-09-04
  updated: 2024-09-04
categories:
  - IT
#draft: true
readtime: 5
# pin: false

tags:
  - service design

search:
  boost: 2 

authors:
  - team
---

# Introducing the orders API specification
Using the orders API, we can place orders, update them, retrieve their details, or cancel them.


In the orders API, we have these two resource URLs:

- `/orders`: Represents a list of orders. 
- `/orders/{orders_id}`: Represents a single order. 
The curly braces around {order_id} indicates that this is a URL path parameter and must be replaced by the ID of an order.

we use the singleton URL /orders/{order_id} to perform actions on an order, such as updating it, and the collections URL /orders to place and to list past orders. HTTP methods help us model these operations:

- POST /orders to place orders since we use POST to create new resources. 
- GET /orders to retrieve a list of orders since we use GET to obtain information. 
- GET /orders/{order_id} to retrieve the details of a particular order. 
- PUT /orders/{order_id} to update an order since we use PUT to update a resource. 
- DELETE /orders/{order_id} to delete an order since we use DELETE for deletes. 
- POST /orders/{order_id}/cancel to cancel an order. We use POST to create a cancellation. 
- POST /orders/{order_id}/pay to pay for an order. We use POST to create a payment.

![order_service_api-endpoints.png](..%2Fsite%2Fassets%2Fimages%2Forder_service_api-endpoints.png)

https://github.com/sponsors/squidfunk?sp=bfium