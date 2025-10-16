# CoffeeMesh API Documentation

## 🎯 Overview

The CoffeeMesh platform provides a comprehensive set of RESTful and GraphQL APIs for managing orders, kitchen operations, product catalog, and payment processing. All APIs are secured with JWT authentication and include rate limiting and comprehensive documentation.

## 🔐 Authentication

### JWT Token Authentication
All APIs require JWT authentication using Bearer tokens:

```bash
curl -H "Authorization: Bearer <jwt_token>" http://localhost:8000/orders
```

### Environment Control
- **Development**: `AUTH_ON=False` (authentication disabled)
- **Production**: `AUTH_ON=True` (authentication required)

## 📊 Service Overview

| Service | Base URL | Port | Technology | Documentation |
|---------|----------|------|------------|---------------|
| **Orders API** | http://localhost:8000 | 8000 | FastAPI | http://localhost:8000/docs/orders |
| **Kitchen API** | http://localhost:8001 | 8001 | Flask | http://localhost:8001/docs |
| **Products API** | http://localhost:8002 | 8002 | FastAPI + GraphQL | http://localhost:8002/graphql |
| **Payments API** | http://localhost:8003 | 8003 | FastAPI | http://localhost:8003/docs/payments |

## 🛒 Orders API

### Base URL: `http://localhost:8000`

#### Endpoints

| Method | Endpoint | Description | Rate Limit |
|--------|----------|-------------|------------|
| `GET` | `/orders` | List user orders | 100/min |
| `POST` | `/orders` | Create new order | 10/min |
| `GET` | `/orders/{order_id}` | Get order details | 100/min |
| `PUT` | `/orders/{order_id}/cancel` | Cancel order | 10/min |

#### Create Order
```bash
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <jwt_token>" \
  -d '{
    "order": [
      {
        "product": "Espresso",
        "size": "small",
        "quantity": 1
      }
    ]
  }'
```

**Response:**
```json
{
  "id": "order-123",
  "created": "2025-10-16T22:59:35.684826",
  "status": "created",
  "items": [
    {
      "product": "Espresso",
      "size": "small",
      "quantity": 1
    }
  ],
  "schedule_id": null,
  "delivery_id": null
}
```

#### List Orders
```bash
curl -H "Authorization: Bearer <jwt_token>" \
  http://localhost:8000/orders?limit=10&cancelled=false
```

**Response:**
```json
{
  "orders": [
    {
      "id": "order-123",
      "created": "2025-10-16T22:59:35.684826",
      "status": "created",
      "items": [...],
      "schedule_id": null,
      "delivery_id": null
    }
  ]
}
```

## 🍳 Kitchen API

### Base URL: `http://localhost:8001`

#### Endpoints

| Method | Endpoint | Description | Rate Limit |
|--------|----------|-------------|------------|
| `GET` | `/schedules` | List production schedules | No limit |
| `POST` | `/schedules` | Create production schedule | No limit |
| `GET` | `/schedules/{schedule_id}` | Get schedule details | No limit |
| `PUT` | `/schedules/{schedule_id}/status` | Update schedule status | No limit |

#### Create Schedule
```bash
curl -X POST http://localhost:8001/schedules \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <jwt_token>" \
  -d '{
    "order_id": "order-123",
    "scheduled_time": "2025-10-17T10:00:00Z",
    "estimated_duration": 15,
    "priority": "normal"
  }'
```

**Response:**
```json
{
  "id": "schedule-456",
  "order_id": "order-123",
  "scheduled_time": "2025-10-17T10:00:00Z",
  "estimated_duration": 15,
  "status": "pending",
  "priority": "normal",
  "created": "2025-10-16T23:00:00Z"
}
```

## 🛍️ Products API (GraphQL)

### Base URL: `http://localhost:8002`

#### GraphQL Endpoint: `http://localhost:8002/graphql`

#### Queries

##### Get All Products
```graphql
query {
  allProducts {
    id
    name
    price
    category
    sizes {
      size
      price
    }
  }
}
```

**Request:**
```bash
curl -X POST http://localhost:8002/graphql \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <jwt_token>" \
  -d '{"query": "{ allProducts { id name price category } }"}'
```

**Response:**
```json
{
  "data": {
    "allProducts": [
      {
        "id": "1",
        "name": "Espresso",
        "price": 2.50,
        "category": "Beverage"
      }
    ]
  }
}
```

##### Get Single Product
```graphql
query {
  product(id: "1") {
    name
    price
    category
    ingredients {
      name
      quantity
    }
  }
}
```

##### Get All Ingredients
```graphql
query {
  allIngredients {
    name
    supplier {
      name
      contact
    }
  }
}
```

#### Mutations

##### Create Product
```graphql
mutation {
  createProduct(input: {
    name: "New Coffee"
    price: 4.50
    category: "Beverage"
  }) {
    id
    name
    price
  }
}
```

##### Update Product
```graphql
mutation {
  updateProduct(id: "1", input: {
    price: 3.75
  }) {
    id
    name
    price
  }
}
```

## 💳 Payments API

### Base URL: `http://localhost:8003`

#### Endpoints

| Method | Endpoint | Description | Rate Limit |
|--------|----------|-------------|------------|
| `GET` | `/` | Service health check | No limit |
| `POST` | `/payments` | Create payment | 20/min |
| `GET` | `/payments/{payment_id}` | Get payment details | 100/min |
| `POST` | `/payments/{payment_id}/process` | Process payment | 10/min |

#### Create Payment
```bash
curl -X POST http://localhost:8003/payments \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <jwt_token>" \
  -d '{
    "order_id": "order-123",
    "amount": "10.50",
    "currency": "USD",
    "payment_method": "credit_card"
  }'
```

**Response:**
```json
{
  "payment_id": "fee6e260-f6f9-449a-a13f-b31f30d4f655",
  "order_id": "order-123",
  "status": "pending",
  "amount": "10.50",
  "currency": "USD",
  "payment_method": "credit_card",
  "created": "2025-10-16T22:59:35.684826",
  "updated": null
}
```

#### Process Payment
```bash
curl -X POST http://localhost:8003/payments/{payment_id}/process \
  -H "Authorization: Bearer <jwt_token>"
```

**Response:**
```json
{
  "payment_id": "fee6e260-f6f9-449a-a13f-b31f30d4f655",
  "order_id": "order-123",
  "status": "paid",
  "amount": "10.50",
  "currency": "USD",
  "payment_method": "credit_card",
  "created": "2025-10-16T22:59:35.684826",
  "updated": "2025-10-16T23:00:05.065251"
}
```

## 🔒 Security Features

### Rate Limiting
- **Orders API**: 100/min (GET), 10/min (POST)
- **Payments API**: 100/min (GET), 20/min (POST), 10/min (process)
- **Kitchen API**: No rate limiting (internal service)
- **Products API**: No rate limiting (catalog service)

### Security Headers
All APIs include comprehensive security headers:
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `X-XSS-Protection: 1; mode=block`
- `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Content-Security-Policy: default-src 'self'`

### HTTPS/SSL
- **Production**: HTTPS required via Nginx proxy
- **Development**: HTTP allowed for local development
- **SSL Termination**: Handled by Nginx reverse proxy

## 📊 Error Handling

### Standard Error Response
```json
{
  "detail": "Error message",
  "status_code": 400,
  "timestamp": "2025-10-16T23:00:00Z"
}
```

### Common HTTP Status Codes
- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `422` - Validation Error
- `429` - Rate Limit Exceeded
- `500` - Internal Server Error

### Rate Limit Exceeded
```json
{
  "detail": "Rate limit exceeded: 100 per 1 minute",
  "status_code": 429
}
```

## 🧪 Testing

### Health Checks
```bash
# Orders API
curl http://localhost:8000/health

# Kitchen API
curl http://localhost:8001/health

# Products API
curl http://localhost:8002/health

# Payments API
curl http://localhost:8003/
```

### API Testing Examples
```bash
# Test authentication
curl -H "Authorization: Bearer invalid_token" http://localhost:8000/orders
# Expected: 401 Unauthorized

# Test rate limiting
for i in {1..101}; do curl http://localhost:8000/orders; done
# Expected: 429 Rate Limit Exceeded after 100 requests

# Test validation
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"invalid": "data"}'
# Expected: 422 Validation Error
```

## 📚 SDKs and Client Libraries

### JavaScript/TypeScript
```typescript
// Orders API Client
import axios from 'axios';

const ordersAPI = axios.create({
  baseURL: 'http://localhost:8000',
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

// Create order
const createOrder = async (orderData: any) => {
  const response = await ordersAPI.post('/orders', orderData);
  return response.data;
};
```

### Python
```python
import requests

# Orders API Client
class OrdersAPIClient:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {'Authorization': f'Bearer {token}'}
    
    def create_order(self, order_data):
        response = requests.post(
            f'{self.base_url}/orders',
            json=order_data,
            headers=self.headers
        )
        return response.json()

# Usage
client = OrdersAPIClient('http://localhost:8000', 'your-jwt-token')
order = client.create_order({'order': [{'product': 'Espresso', 'size': 'small', 'quantity': 1}]})
```

## 🔧 Configuration

### Environment Variables
```bash
# Database
DB_URL=postgresql://postgres:postgres@localhost:5432/postgres

# Authentication
AUTH_ON=True

# Auth0 Configuration
AUTH0_DOMAIN=your-domain.auth0.com
AUTH0_AUDIENCE=your-api-identifier
```

### CORS Configuration
All APIs support CORS for cross-origin requests:
- **Allowed Origins**: `*` (development), specific domains (production)
- **Allowed Methods**: `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`
- **Allowed Headers**: `Authorization`, `Content-Type`

## 📞 Support

### API Documentation
- **Swagger UI**: Available at `/docs` endpoint for each service
- **ReDoc**: Available at `/redoc` endpoint for each service
- **OpenAPI Spec**: Available at `/openapi.json` endpoint for each service

### Troubleshooting
1. **Check service status**: `docker-compose ps`
2. **View logs**: `docker-compose logs <service-name>`
3. **Test connectivity**: `curl http://localhost:<port>/health`
4. **Verify authentication**: Check JWT token validity

---

**CoffeeMesh API Documentation - Comprehensive API reference for the CoffeeMesh platform**