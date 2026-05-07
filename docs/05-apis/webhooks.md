# Webhooks

Webhooks allow external systems to receive notifications about events and interactions.

## Purpose

Webhooks are used to notify external systems when specific events occur within the AI Event Platform. This enables real-time integration with third-party services, such as CRM systems, analytics tools, or custom applications.

## Endpoints

### Register Webhook

**URL**: `POST /webhooks`
**Method**: POST
**Headers**:
  - `Authorization`: Bearer YOUR_ACCESS_TOKEN
  - `Content-Type`: application/json
**Request Body**:
  ```json
  {
    "event_id": "12345",
    "webhook_url": "https://your-webhook-url.com/events"
  }
  ```
**Response**:
  ```json
  {
    "webhook_id": "98765",
    "event_id": "12345",
    "webhook_url": "https://your-webhook-url.com/events"
  }
  ```

### Example Payload

When an event occurs, the AI Event Platform sends a POST request to the registered webhook URL with the following payload structure:

```json
{
  "event_id": "12345",
  "name": "Annual Conference",
  "date": "2023-10-05",
  "location": "Convention Center"
}
```

## Examples

### Register Webhook Example

```python
import requests

url = "https://api.ai-event-platform.com/webhooks"
headers = {
    "Authorization": "Bearer YOUR_ACCESS_TOKEN",
    "Content-Type": "application/json"
}
data = {
    "event_id": "12345",
    "webhook_url": "https://your-webhook-url.com/events"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```