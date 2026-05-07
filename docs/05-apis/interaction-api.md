# Interaction API

The Interaction API allows you to handle real-time messaging and approval processes during events.

## Endpoints

### Create Interaction

**URL**: `POST /interactions`
**Method**: POST
**Headers**:
  - `Authorization`: Bearer YOUR_ACCESS_TOKEN
  - `Content-Type`: application/json
**Request Body**:
  ```json
  {
    "event_id": "12345",
    "message": "Hello, everyone!"
  }
  ```
**Response**:
  ```json
  {
    "interaction_id": "67890",
    "event_id": "12345",
    "message": "Hello, everyone!",
    "timestamp": "2023-10-05T10:00:00Z"
  }
  ```

### Approve Interaction

**URL**: `PUT /interactions/{interaction_id}/approve`
**Method**: PUT
**Headers**:
  - `Authorization`: Bearer YOUR_ACCESS_TOKEN
**Response**:
  ```json
  {
    "interaction_id": "67890",
    "event_id": "12345",
    "message": "Hello, everyone!",
    "timestamp": "2023-10-05T10:00:00Z",
    "status": "approved"
  }
  ```

### Reject Interaction

**URL**: `PUT /interactions/{interaction_id}/reject`
**Method**: PUT
**Headers**:
  - `Authorization`: Bearer YOUR_ACCESS_TOKEN
**Response**:
  ```json
  {
    "interaction_id": "67890",
    "event_id": "12345",
    "message": "Hello, everyone!",
    "timestamp": "2023-10-05T10:00:00Z",
    "status": "rejected"
  }
  ```

## Examples

### Create Interaction Example

```python
import requests

url = "https://api.ai-event-platform.com/interactions"
headers = {
    "Authorization": "Bearer YOUR_ACCESS_TOKEN",
    "Content-Type": "application/json"
}
data = {
    "event_id": "12345",
    "message": "Hello, everyone!"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

### Approve Interaction Example

```python
import requests

url = "https://api.ai-event-platform.com/interactions/67890/approve"
headers = {
    "Authorization": "Bearer YOUR_ACCESS_TOKEN",
    "Content-Type": "application/json"
}

response = requests.put(url, headers=headers)
print(response.json())
```

### Reject Interaction Example

```python
import requests

url = "https://api.ai-event-platform.com/interactions/67890/reject"
headers = {
    "Authorization": "Bearer YOUR_ACCESS_TOKEN",
    "Content-Type": "application/json"
}

response = requests.put(url, headers=headers)
print(response.json())
```
