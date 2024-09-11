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

slug: specifications

tags:
  - IT
---

# Using OpenAPI as specifications for the Order's API
<!-- more -->

## POST /orders

```yaml
paths:
  /oders:
    post:
      summary: Create an order
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequestSchema'
      responses:
        '201':
          description: A JSON representation of the created order
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrderResponseSchema'
        '422':
          description: unprocessable entity

components:
  schemas:
    OrderItemSchema:
      description: order item schema
      additionalProperties: false
      type: object
      required:
        - product
        - size
      properties:
        product:
          type: string
        size:
          type: string
          enum:
            - small
            - medium
            - big
        quantity:
          type: integer
          format: int64
          default: 1
          minimum: 1
          maximum: 1000000

    CreateOrderRequestSchema:
      description: create order schema
      additionalProperties: false
      type: object
      required:
        - order
      properties:
        order:
          type: array
          minItems: 1
          items:
            $ref: '#/components/schemas/OrderItemSchema'

    OrderResponseSchema:
      description: the schema of the response of the order request
      additionalProperties: false
      type: object
      required:
        - id
        - created
        - status
        - order
      properties:
        id:
          type: string
          format: uuid
        created:
          type: string
          format: data-time
        status:
          type: string
          enum:
            - created
            - paid
            - progress
            - cancelled
            - dispatched
            - delivered
        order:
          type: array
          minItems: 1
          items:
            $ref: '#/components/schemas/OrderItemSchema'

```