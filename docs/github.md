## GitHub

### Remove Ghost Notifications

1. Get token with authorize to access notifications
2. Request Api with token to delete all notifications

```python
import requests

TOKEN = '<TOKEN>'

headers = {
    'Accept': 'application/vnd.github+json',
    'Authorization': f'Bearer {TOKEN}',
    'X-Github-Api-Version': '2022-11-28',
}

notifications = requests.get('https://api.github.com/notifications', headers=headers)

for notification in notifications.json():
    print(f"{notification['id']}: {notification['subject']['title']}")
    requests.delete(f'https://api.github.com/notifications/threads/{notification["id"]}', headers=headers)
```