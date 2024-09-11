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
  - hogmanay
---

# Designing API payloads
Payloads represent the data exchanged between a client and a server through an HTTP request. 
We send payloads to the server when we want to cre- ate or update a resource, and the server sends us payloads when we request data. 
The usability of an API is very much dependent on good payload design.
<!-- more -->

## RESPONSE PAYLOADS FOR POST REQUESTS
We use POST requests to create resources. 

We place orders through the `POST/orders` endpoint.  with the following payload:

```json
 {
  "order": [
     {
      "product": "cappuccino",
      "size": "big",
      "quantity": 1
    }, 
    {
      "product": "croissant",
      "size": "medium",
      "quantity": 2
    }
  ]
}
```
To place an order, we send the list of items we want to buy to the server, which takes responsibility for:

- assigning a unique ID to the order, 
- sets the time when the order was taken and its initial status. 

- therefore the order’s ID must be returned in the response payload: 

```json
{
  "id": "96247264-7d42-4a95-b073-44cedf5fc07d", 
  "created": "2024-02-02",
  "status": "created",
  "order": [
     {
      "product": "cappuccino",
      "size": "big",
      "quantity": 1
    }, 
    {
      "product": "croissant",
      "size": "medium",
      "quantity": 2
    }
  ] 
}
```

We call the properties set by the server server-side or read-only properties, and we must include them in the response payload.

## RESPONSE PAYLOADS FOR PUT AND PATCH REQUESTS
To update a resource, we use a PUT or a PATCH request.

we make use of the PUT /orders/ {order_id}  (`like PUT /orders/cf979f89-effd-4996-ae3d-8d42c79e0831`) endpoint of the orders API with the following payload:

```json
{
  "id": "cf979f89-effd-4996-ae3d-8d42c79e0831", 
  "created": "2024-02-02",
  "status": "created",
  "order": [
    {
      "product": "cappuccino",
      "size": "big",
      "quantity": 1
    }, 
    {
      "product": "croissant",
      "size": "medium",
      "quantity": 2
    }
  ] 
}
```
the service the return a full representation of the resource, which the client can use to validate that the update was correctly processed.

```json
 {
  "id": "cf979f89-effd-4996-ae3d-8d42c79e0831",
  "created": "2024-02-02",
  "status": "created",
  "order": [
    {
      "product": "cappuccino",
      "size": "big",
      "quantity": 1
    }, 
    {
      "product": "croissant",
      "size": "medium",
      "quantity": 2
    }
  ]
}
```

## RESPONSE PAYLOADS FOR GET REQUESTS
We retrieve resources from the server using GET requests.
The orders API exposes two GET endpoints:

- GET /orders 
The GET /orders returns a list of orders. 

`Strategie1`: include a full representation of each order 

```json
{
  "orders": [
  {
    "id": "96247264-7d42-4a95-b073-44cedf5fc07d", 
    "created": "2025-02-02",
    "status": "created",
    "order": [
      {
      "product": "cappuccino",
      "size": "big",
      "quantity": 1
    }, 
    {
      "product": "croissant",
      "size": "medium",
      "quantity": 2
    }
    ]
  },
    {
     "id": "96877264-7d42-4a95-b073-44cedjf5fc07d", 
    "created": "2024-02-02",
    "status": "created",
    "order": [
    {
      "product": "expresso",
      "size": "small",
      "quantity": 2
    }, 
    {
      "product": "burger",
      "size": "big",
      "quantity": 3
    }
  ]
}
  ]
}
```

`Strategie2`: include a partial representation of each order.

```json
{
  "orders": [
  {
    "id": "cf979f89-effd-4996-ae3d-8d42c79e0831"
  }, 
    {
      "id": "f005db16-fa8c-4e29-82a6-6210dddda554"
  }
  ]
}
```

when using the GET /orders endpoint, we may want to limit the results to only the five most recent orders or to list only cancelled orders. 
URL query parameters allow us to accomplish those goals and should always be optional, and, when appropriate, the server may assign default values for them.
GET /orders?cancelled=true
GET /orders?cancelled=true&Limit=5


- GET /orders/{orders_id} with order_id= `cf979f89-effd-4996-ae3d-8d42c79e0831`
The payload of the response will be: 

```json
 {
  "id": "cf979f89-effd-4996-ae3d-8d42c79e0831",
  "created": "2024-02-02",
  "status": "created",
  "order": [
    {
      "product": "cappuccino",
      "size": "big",
      "quantity": 1
    }, 
    {
      "product": "croissant",
      "size": "medium",
      "quantity": 2
    }
  ]
}
```






