# Triggering GitHub Actions via HTTP Requests

This document shows different ways to trigger the "Hello World" GitHub Actions workflow using HTTP requests.

## Prerequisites

1. **GitHub Personal Access Token**: Create a token with `repo` scope
   - Go to GitHub Settings → Developer settings → Personal access tokens
   - Generate new token with `repo` permissions

2. **Repository Information**:
   - Replace `YOUR_USERNAME` with your GitHub username
   - Replace `YOUR_REPO_NAME` with your repository name
   - Replace `YOUR_TOKEN` with your personal access token

## Method 1: Using `repository_dispatch` (Recommended for API triggers)

This method uses the `repository_dispatch` event that we added to the workflow.

### cURL Example:
```bash
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/YOUR_USERNAME/YOUR_REPO_NAME/dispatches \
  -d '{"event_type": "hello-world-trigger"}'
```

### JavaScript/Node.js Example:
```javascript
const fetch = require('node-fetch'); // or use built-in fetch in Node 18+

async function triggerWorkflow() {
  const response = await fetch('https://api.github.com/repos/YOUR_USERNAME/YOUR_REPO_NAME/dispatches', {
    method: 'POST',
    headers: {
      'Accept': 'application/vnd.github.v3+json',
      'Authorization': 'token YOUR_TOKEN',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      event_type: 'hello-world-trigger'
    })
  });

  if (response.ok) {
    console.log('Workflow triggered successfully!');
  } else {
    console.error('Failed to trigger workflow:', response.statusText);
  }
}

triggerWorkflow();
```

### Python Example:
```python
import requests

def trigger_workflow():
    url = "https://api.github.com/repos/YOUR_USERNAME/YOUR_REPO_NAME/dispatches"
    headers = {
        "Accept": "application/vnd.github.v3+json",
        "Authorization": "token YOUR_TOKEN",
        "Content-Type": "application/json"
    }
    data = {
        "event_type": "hello-world-trigger"
    }
    
    response = requests.post(url, headers=headers, json=data)
    
    if response.status_code == 204:
        print("Workflow triggered successfully!")
    else:
        print(f"Failed to trigger workflow: {response.status_code} - {response.text}")

trigger_workflow()
```

## Method 2: Using `workflow_dispatch` 

This method triggers the manual workflow dispatch.

### cURL Example:
```bash
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/YOUR_USERNAME/YOUR_REPO_NAME/actions/workflows/hello-world.yml/dispatches \
  -d '{"ref": "main"}'
```

### JavaScript Example:
```javascript
async function triggerWorkflowDispatch() {
  const response = await fetch('https://api.github.com/repos/YOUR_USERNAME/YOUR_REPO_NAME/actions/workflows/hello-world.yml/dispatches', {
    method: 'POST',
    headers: {
      'Accept': 'application/vnd.github.v3+json',
      'Authorization': 'token YOUR_TOKEN',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      ref: 'main'
    })
  });

  if (response.ok) {
    console.log('Workflow dispatch triggered successfully!');
  } else {
    console.error('Failed to trigger workflow dispatch:', response.statusText);
  }
}
```

## Response Information

- **Success Response**: HTTP 204 (No Content) for both methods
- **Authentication Error**: HTTP 401 if token is invalid
- **Not Found**: HTTP 404 if repository doesn't exist or token lacks permissions
- **Rate Limiting**: GitHub API has rate limits (5000 requests/hour for authenticated requests)

## Checking Workflow Status

After triggering, you can check the workflow runs:

```bash
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/YOUR_USERNAME/YOUR_REPO_NAME/actions/runs
```

## Security Notes

1. **Never commit your personal access token** to version control
2. **Use environment variables** to store tokens securely
3. **Consider using GitHub Apps** for production integrations
4. **Rotate tokens regularly** for security

## Testing

1. Replace the placeholders with your actual values
2. Run one of the examples above
3. Check the "Actions" tab in your GitHub repository
4. You should see a new workflow run with "Hello World" in the logs
