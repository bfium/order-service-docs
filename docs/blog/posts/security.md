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
---

# Securing the API
If our API is protected, the API specification must describe how users need to authenticate and authorize their requests.
<!-- more -->

The security definitions of the API go within the components section of the specification, under the securitySchemes header.

With OpenAPI, we can describe different security schemes, such as:

- HTTP-based authentication, 
- key-based authentication, 
- Open Authorization 2 (OAuth2),  
- OpenID Connect.

```yaml
components:
  responses:
    #...
  schemas:
    #...
  securitySchemes:  # The security schemes under the securitySchemes header of the API’s components section
    openId:   # We provide a name for the security scheme (it can be any name).
      type: openIdConnect   # The type of security scheme
      openIdConnectUrl: https://order-service-dev.eu.auth0.com/.well-known/openid-configuration # The URL that describes the OpenID Connect configuration in our backend
    oauth2:   # The name of another security scheme
      type: oauth2   # The type of the security scheme
      flows:      # The authorization flows available under this security scheme
        clientCredentials: # A description of the client credentials flow
          tokenUrl: https://order-service-dev.eu.auth0.com/oauth/token # The URL where users can request authorization tokens
            scopes:{}     # The available scopes when requesting an authorization token
    bearerAuth:
      type: http
      sheme: bearer
      bearerFormat: JWT  # The bearer token has a JSON Web Token (JWT) format.
              
  security:
  - oauth2:
      - getOrders
      - createOrder
      - getOrder
      - updateOrder
      - deleteOrder
      - payOrder
      - cancelOrder
  - bearerAuth:
      - getOrders
      - createOrder
      - getOrder
      - updateOrder
      - deleteOrder
      - payOrder
      - cancelOrder
  
```