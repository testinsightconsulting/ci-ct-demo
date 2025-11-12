# 🧩 NDO Agent API Usage Guide

## 📘 Introduction
The **Network DevOps Agent (NDO Agent)** API provides a RESTful interface that allows external systems or CI/CD workflows to control automated test executions.  
You can authenticate, start tests, check their progress, and retrieve reports — all programmatically.

---

## 📚 Table of Contents
1. [Authentication: Obtain Access Token](#1-authentication-obtain-access-token)  
2. [Create a New Execution](#2-create-a-new-execution)  
3. [List Executions and Get Execution ID](#3-list-executions-and-get-execution-id)  
4. [Get Execution Report](#4-get-execution-report)  
5. [Example Workflow (GitHub Actions)](#5-example-workflow-github-actions)  
6. [Environment Variables](#6-environment-variables)  
7. [References](#7-references)

---

## 1. 🔐 Authentication: Obtain Access Token
Obtain an access token using **HTTP Basic Authentication**.

**Endpoint**
```
GET https://{{NDO-Host}}/agent/v2/auth
```

**Headers**
| Key | Value |
|------|--------|
| Accept | `application/json` |
| Authorization | `Basic <base64(username:password)>` |

**Response Example**
```json
{
  "access_token": "<token>",
  "token_type": "Bearer",
  "expires_in": 1440
}
```

**Steps**
1. Encode `username:password` in base64.
2. Include it in the `Authorization` header.
3. Send the GET request.
4. Extract the `access_token`.

---

## 2. ▶️ Create a New Execution

Start a new test execution using the token.

**Endpoint**
```
POST https://{{NDO-Host}}/agent/v1/executions
```

**Headers**
| Key | Value |
|------|--------|
| Accept | `application/json` |
| Authorization | `Bearer <access_token>` |
| Content-Type | `application/json` |

**Body Example**
```json
{
  "testPath": "project://ai_LS_5G/testcases/example/test.fftc",
  "parametersFilePath": "project://ai_LS_5G/parameters/example/test-params.ffpt",
  "reportFormat": "text",
  "archivedReportFormat": "CUSTOM HTML",
  "detailLevel": "ALL_ISSUES_ALL_STEPS"
}
```

---

## 3. 📄 List Executions and Get Execution ID

**Endpoint**
```
GET https://{{NDO-Host}}/agent/v1/executions/
```

**Headers**
| Key | Value |
|------|--------|
| Accept | `application/json` |
| Authorization | `Bearer <access_token>` |

**Response Example**
```json
[
  {
    "id": "12345",
    "status": "completed"
  }
]
```

---

## 4. 📊 Get Execution Report

**Endpoint**
```
GET https://{{NDO-Host}}/agent/v1/executions/{{exec_id}}/report
```

**Headers**
| Key | Value |
|------|--------|
| Accept | `application/json` |
| Authorization | `Bearer <access_token>` |

**Response**
Returns a detailed report for the execution.

---

## 5. 🧠 Example Workflow (GitHub Actions)

Here’s a minimal example workflow that calls the NDO Agent API using `curl`.

Save as:  
`.github/workflows/ndo-agent-demo.yml`

```yaml
name: NDO Agent Demo

on:
  workflow_dispatch:

jobs:
  run-ndo-test:
    runs-on: ubuntu-latest
    env:
      NDO_HOST: ${{ secrets.NDO_HOST }}
      BASIC_AUTH: ${{ secrets.AGENT_BASIC_AUTH }}
      TEST_PATH: "project://ai_LS_5G/testcases/example/test.fftc"

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Authenticate with NDO Agent
        id: auth
        run: |
          echo "Requesting token from $NDO_HOST..."
          TOKEN=$(curl -s -X GET "$NDO_HOST/agent/v2/auth"             -H "Accept: application/json"             -H "Authorization: Basic $BASIC_AUTH" | jq -r '.access_token')
          echo "token=$TOKEN" >> $GITHUB_OUTPUT

      - name: Start new execution
        id: execute
        run: |
          echo "Starting execution..."
          RESPONSE=$(curl -s -X POST "$NDO_HOST/agent/v1/executions"             -H "Authorization: Bearer ${{ steps.auth.outputs.token }}"             -H "Content-Type: application/json"             -d "{"testPath": "${TEST_PATH}", "reportFormat": "text"}")
          echo "$RESPONSE"
          EXEC_ID=$(echo "$RESPONSE" | jq -r '.executionID')
          echo "execution_id=$EXEC_ID" >> $GITHUB_OUTPUT

      - name: Get execution report
        run: |
          echo "Fetching report for ${{ steps.execute.outputs.execution_id }}..."
          curl -s -X GET "$NDO_HOST/agent/v1/executions/${{ steps.execute.outputs.execution_id }}/report"             -H "Authorization: Bearer ${{ steps.auth.outputs.token }}"             -H "Accept: application/json" | jq .
```

**Usage**
1. Add these repository secrets:
   - `NDO_HOST`: Base URL (e.g., `https://yourdomain.ngrok-free.app`)
   - `AGENT_BASIC_AUTH`: Base64 encoded `username:password`
2. Manually trigger the workflow under **Actions → NDO Agent Demo → Run workflow**.

This example:
- Authenticates using Basic Auth  
- Starts an execution with a test path  
- Retrieves the execution report  
- Uses `jq` to format responses cleanly  

---

## 6. ⚙️ Environment Variables

| Variable | Description |
|-----------|--------------|
| `NDO_HOST` | The base URL of your NDO Agent instance |
| `BASIC_AUTH` | Base64-encoded credentials for authentication |
| `TEST_PATH` | Path of the test to execute (e.g., `project://ai_LS_5G/testcases/example/test.fftc`) |

---

## 7. 🔗 References

- **Authentication:** `/agent/v2/auth`  
- **Create Execution:** `/agent/v1/executions`  
- **Get Executions:** `/agent/v1/executions/`  
- **Get Report:** `/agent/v1/executions/{exec_id}/report`  
- **Postman Collection:** `NDO.postman_collection.json`

---

### 🏁 Summary
This guide shows how to:
- Authenticate to NDO Agent  
- Start a test execution  
- Retrieve reports automatically in a CI workflow  

With GitHub Actions, you can easily integrate NDO test execution into your continuous testing pipelines.
