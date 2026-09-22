# GitHub API Health Check POC

## Overview

This project implements an automated endpoint health-check solution using
GitHub Actions.

The workflow monitors the public GitHub API endpoint:

`https://api.github.com`

It runs automatically every hour and can also be executed manually from the
GitHub Actions interface.

## Features

- Runs automatically every hour
- Supports manual execution using `workflow_dispatch`
- Calls the public GitHub API endpoint
- Captures the HTTP status code
- Captures the response time
- Captures a UTC timestamp
- Classifies the endpoint as Healthy or Unhealthy
- Generates a Markdown health-check report
- Adds the report to the GitHub Actions job summary
- Uploads the report as a GitHub Actions artifact
- Sends the health summary to a Microsoft Teams channel
- Stores the Teams webhook URL securely using GitHub Secrets
- Includes timeouts, retries, logging and error handling

## Health Rule

The endpoint is considered:

- **Healthy** when the HTTP status code is `200`
- **Unhealthy** for all other status codes, request failures or timeouts

## Repository Structure

```text
.
├── .github
│   └── workflows
│       └── health-check.yml
├── .gitignore
└── README.md
```

## Workflow Triggers

### Scheduled Trigger

The following cron expression runs the workflow at minute zero of every hour:

```yaml
schedule:
  - cron: "0 * * * *"
```

### Manual Trigger

The following event enables the Run workflow button:

```yaml
workflow_dispatch:
```

## Prerequisites

- A GitHub repository with GitHub Actions enabled
- Access to a Microsoft Teams channel
- Permission to create a Microsoft Teams Workflow
- Permission to configure GitHub repository secrets

## Microsoft Teams Setup

1. Open the required Microsoft Teams channel.
2. Select the channel's **More options** menu.
3. Select **Workflows**.
4. Find the **Send webhook alerts to a channel** template.
5. Configure the destination Team and Channel.
6. Save the workflow.
7. Copy the generated webhook URL.

Treat the webhook URL as sensitive information.

## GitHub Secret Setup

1. Open the GitHub repository.
2. Select **Settings**.
3. Select **Secrets and variables**.
4. Select **Actions**.
5. Select **New repository secret**.
6. Enter `TEAMS_WEBHOOK_URL` as the name.
7. Paste the Microsoft Teams Workflow webhook URL.
8. Save the secret.

## Manual Execution

1. Open the GitHub repository.
2. Select the **Actions** tab.
3. Select **GitHub API Health Check**.
4. Select **Run workflow**.
5. Choose the appropriate branch.
6. Select **Run workflow**.

## Viewing the Results

Open the completed workflow run to view:

- Step logs
- Endpoint health status
- HTTP response code
- Response time
- UTC timestamp
- GitHub Actions job summary

## Downloading the Report

1. Open the completed workflow run.
2. Scroll to the **Artifacts** section.
3. Download the artifact named:

   `health-check-report-<run-number>`

4. Extract the downloaded ZIP file.
5. Open `health-check-report.md`.

## Error Handling

The workflow handles:

- Connection failures
- Request timeouts
- Non-200 HTTP responses
- Missing Teams webhook secret
- Teams webhook failures
- Missing report files
- Transient endpoint and webhook failures through retries

The workflow attempts to generate the report, upload the artifact and notify
Microsoft Teams before setting the final workflow result.

## Security

- The Teams webhook URL is stored in GitHub Secrets.
- The webhook URL is not committed to source control.
- The workflow receives only read permission for repository contents.
- Report files and webhook payload files are generated during workflow
  execution and are not committed.

## Expected Output

Example successful result:

```text
Status: Healthy
HTTP Status: 200
Response Time: 245 ms
Timestamp: 2026-09-21T12:30:00Z
```

Example failed result:

```text
Status: Unhealthy
HTTP Status: 500
Response Time: 310 ms
Timestamp: 2026-09-21T12:30:00Z
```
