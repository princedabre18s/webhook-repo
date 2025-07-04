# Developer Assessment Task Prompt

You are tasked with building a system that integrates GitHub webhooks with a Flask-based application to track and display repository actions in real-time. The system involves two GitHub repositories: one to trigger webhook events (`action-repo`) and another to process these events and display them via a UI (`webhook-repo`). The data from the webhooks will be stored in MongoDB, and the UI will poll this database to reflect updates. This assignment assesses your ability to work with APIs, databases, and real-time UI updates.

## Project Overview

The goal is to create a system that:

1. Monitors GitHub actions (Push, Pull Request, and optionally Merge) in `action-repo` via webhooks.
2. Receives these events at an endpoint in `webhook-repo`, processes them, and stores the data in MongoDB.
3. Displays the latest changes in a UI by polling MongoDB every 15 seconds, formatted according to specific action types.

## Project Requirements

### 1. Repository Setup

- **action-repo**:
  - Create a new GitHub repository named `action-repo`.
  - This repository will trigger webhooks for the following actions:
    - Push
    - Pull Request
    - Merge (optional, but encouraged for brownie points).
  - Purpose: Simulate GitHub actions to send webhook payloads to `webhook-repo`.

- **webhook-repo**:
  - Create a new GitHub repository named `webhook-repo`.
  - This repository will contain:
    - A Flask application with a webhook endpoint to receive and process payloads from `action-repo`.
    - A UI to display the processed data.
  - Purpose: Handle webhook events, store data in MongoDB, and serve the UI.

### 2. Webhook Configuration

- Configure webhooks in `action-repo` to send payloads to an endpoint in `webhook-repo`.
- Events to configure:
  - **Push**: Triggered when a commit is pushed to a branch.
  - **Pull Request**: Triggered when a pull request is created.
  - **Merge**: Triggered when a pull request is merged (note: GitHub sends this as part of a Pull Request event; infer the merge from the payload).
- Steps:
  1. In `action-repo`, go to Settings > Webhooks > Add Webhook.
  2. Set the Payload URL to the endpoint in `webhook-repo` (e.g., `https://your-domain.com/webhook`).
  3. Set Content Type to `application/json`.
  4. Optionally, set a Secret token for security and validate it in the endpoint.
  5. Select individual events: Push, Pull Requests.
  6. Ensure the webhook is active.

### 3. Webhook Endpoint Implementation

- **Framework**: Use Flask to implement the webhook endpoint in `webhook-repo`.
- **Endpoint Details**:
  - Create a POST endpoint (e.g., `/webhook`) to receive GitHub webhook payloads.
  - Process the payload to extract:
    - `request_id`: 
      - For Push: Git commit hash (e.g., from `payload['head_commit']['id']`).
      - For Pull Request: PR ID (e.g., from `payload['pull_request']['id']`).
      - For Merge: Merge commit hash or PR ID (infer from `payload['pull_request']['merged']` being `true`).
    - `author`: GitHub username (e.g., from `payload['pusher']['name']` for Push, `payload['pull_request']['user']['login']` for PR/Merge).
    - `action`: Enum value based on the event:
      - "PUSH" for Push events.
      - "PULL_REQUEST" for Pull Request creation.
      - "MERGE" for merged Pull Requests (optional).
    - `from_branch`: Source branch (e.g., `payload['pull_request']['head']['ref']` for PR/Merge; not applicable or equal to `to_branch` for Push).
    - `to_branch`: Target branch (e.g., `payload['ref'].split('/')[-1]` for Push, `payload['pull_request']['base']['ref']` for PR/Merge).
    - `timestamp`: UTC datetime string of the action (e.g., `payload['head_commit']['timestamp']` for Push, `payload['pull_request']['created_at']` or `updated_at` for PR/Merge; ensure UTC format like "1st April 2021 - 9:30 PM UTC").
  - Store the extracted data in MongoDB using the schema below.
- **Reference Code**:
  - Use the provided reference repository as a starting point.
  - Customize and extend the base code to meet these requirements.

### 4. MongoDB Schema

- Store the webhook data in a MongoDB collection with the following document structure:

  ```json
  {
      "_id": ObjectId,           // MongoDB default ID
      "request_id": string,      // Git commit hash for Push, PR ID for Pull Requests, etc.
      "author": string,          // GitHub username
      "action": string,          // Enum: ["PUSH", "PULL_REQUEST", "MERGE"]
      "from_branch": string,     // Source branch (if applicable)
      "to_branch": string,       // Target branch
      "timestamp": string        // UTC datetime string (e.g., "1st April 2021 - 9:30 PM UTC")
  }
  ```

- Notes:
  - Ensure `timestamp` is a human-readable UTC string derived from the payload.
  - Use `pymongo` or a similar library to interact with MongoDB.

### 5. UI Implementation

- **Purpose**: Display the latest changes from MongoDB in real-time.
- **Requirements**:
  - Integrate the UI within the Flask application in `webhook-repo`.
  - Poll MongoDB every 15 seconds to fetch the latest records.
  - Display records in the following formats based on `action`:
    - **PUSH**: "{author} pushed to {to_branch} on {timestamp}"
      - Example: "Travis" pushed to "staging" on 1st April 2021 - 9:30 PM UTC
    - **PULL_REQUEST**: "{author} submitted a pull request from {from_branch} to {to_branch} on {timestamp}"
      - Example: "Travis" submitted a pull request from "staging" to "master" on 1st April 2021 - 9:00 AM UTC
    - **MERGE**: "{author} merged branch {from_branch} to {to_branch} on {timestamp}"
      - Example: "Travis" merged branch "dev" to "master" on 2nd April 2021 - 12:00 PM UTC
  - Design: Keep it clean and minimal (e.g., a simple list or table).
- **Implementation**:
  - Use Flask to serve an HTML page with embedded JavaScript.
  - Use `setInterval` in JavaScript to poll a Flask route (e.g., `/data`) every 15 seconds.
  - The `/data` route should query MongoDB and return the latest records as JSON.
  - Render the JSON data in the UI using the specified formats.

### 6. Application Flow

1. A user performs an action (Push, Pull Request, Merge) in `action-repo`.
2. GitHub sends a webhook payload to the `/webhook` endpoint in `webhook-repo`.
3. The Flask endpoint processes the payload, extracts the required fields, and stores them in MongoDB.
4. The UI polls MongoDB via a Flask route every 15 seconds, fetching the latest data.
5. The UI updates to display the actions in the specified formats.

### 7. Testing and Submission

- **Testing**:
  - Test the full flow:
    1. Push a commit to `action-repo` and verify it appears in the UI.
    2. Create a Pull Request and check the UI update.
    3. Merge a Pull Request (if implemented) and confirm the UI reflects it.
  - Ensure data is correctly stored in MongoDB and displayed in the UI.
- **Submission**:
  - Share the links to both `action-repo` and `webhook-repo` via the Google Form provided in the application.

## Additional Guidance

- **Payload Processing**:
  - Refer to GitHub’s webhook documentation for payload structures:
    - Push: https://docs.github.com/en/webhooks-and-events/webhooks/webhook-events-and-payloads#push
    - Pull Request: https://docs.github.com/en/webhooks-and-events/webhooks/webhook-events-and-payloads#pull_request
  - For Merge, detect `payload['pull_request']['merged'] == true` in the Pull Request event.
- **Timestamp Formatting**:
  - Convert payload timestamps (ISO format) to a readable UTC string (e.g., using Python’s `datetime` module).
- **Dependencies**:
  - Flask: For the web application.
  - `pymongo`: For MongoDB interaction.
  - JavaScript libraries (optional) for UI enhancements.
- **Security**:
  - Optionally, validate webhook payloads using the Secret token and HMAC signature (see GitHub docs).
- **Brownie Points**:
  - Implementing the Merge action earns extra credit; infer it from Pull Request payloads.

## Deliverables

- **action-repo**: A GitHub repository with configured webhooks.
- **webhook-repo**: A GitHub repository containing:
  - Flask application with webhook endpoint.
  - MongoDB integration.
  - UI with polling mechanism.
- Both repositories should be public or accessible for review, with well-documented code.

## Notes

- The PDF mentions a potential diagram under "Application Flow," but no image is provided. Assume it illustrates the flow from GitHub → Webhook → MongoDB → UI.
- This task evaluates your skills in API integration, data handling, and real-time UI updates. Focus on clean, efficient, and maintainable code.

Good luck! This is an opportunity to showcase your abilities and potentially secure a role with an awesome team.