# API Contract

## Overview
RESTful API for managing logic grid puzzles and their states. All endpoints are local HTTP calls.

## Base URL
`http://localhost:3000/api/v1`

## Endpoints

### Puzzles

#### List Puzzles
```http
GET /puzzles
```

**Response** (200 OK)
```json
{
  "puzzles": [
    {
      "id": "string",
      "name": "string",
      "description": "string",
      "gridSize": {
        "rows": "number",
        "columns": "number"
      },
      "createdAt": "string",
      "updatedAt": "string"
    }
  ]
}
```

#### Get Puzzle
```http
GET /puzzles/{puzzleId}
```

**Response** (200 OK)
```json
{
  "id": "string",
  "name": "string",
  "description": "string",
  "gridSize": {
    "rows": "number",
    "columns": "number"
  },
  "categories": [
    {
      "id": "string",
      "name": "string",
      "values": ["string"]
    }
  ],
  "constraints": [
    {
      "id": "string",
      "type": "string",
      "description": "string",
      "categories": ["string"],
      "condition": {
        "operator": "string",
        "values": ["string"]
      }
    }
  ],
  "createdAt": "string",
  "updatedAt": "string"
}
```

### Puzzle State

#### Get Current State
```http
GET /puzzles/{puzzleId}/state
```

**Response** (200 OK)
```json
{
  "puzzleId": "string",
  "lastModified": "string",
  "cells": [
    {
      "position": {
        "row": "number",
        "column": "number"
      },
      "value": "string",
      "possibleValues": ["string"],
      "isUserMarked": "boolean",
      "derivedFrom": ["string"]
    }
  ],
  "derivedConstraints": [
    {
      "id": "string",
      "sourceConstraints": ["string"],
      "affectedCells": [
        {
          "position": {
            "row": "number",
            "column": "number"
          },
          "effect": "string",
          "values": ["string"]
        }
      ]
    }
  ]
}
```

#### Mark Cell
```http
POST /puzzles/{puzzleId}/cells
```

**Request**
```json
{
  "position": {
    "row": "number",
    "column": "number"
  },
  "value": "string"
}
```

**Response** (200 OK)
```json
{
  "cell": {
    "position": {
      "row": "number",
      "column": "number"
    },
    "value": "string",
    "possibleValues": ["string"],
    "isUserMarked": true
  },
  "propagatedChanges": [
    {
      "position": {
        "row": "number",
        "column": "number"
      },
      "value": "string",
      "possibleValues": ["string"],
      "derivedFrom": ["string"]
    }
  ]
}
```

#### Save State
```http
PUT /puzzles/{puzzleId}/state
```

**Request**
```json
{
  "cells": [
    {
      "position": {
        "row": "number",
        "column": "number"
      },
      "value": "string",
      "possibleValues": ["string"],
      "isUserMarked": "boolean",
      "derivedFrom": ["string"]
    }
  ]
}
```

**Response** (200 OK)
```json
{
  "savedAt": "string",
  "stateId": "string"
}
```

### Error Responses

#### 400 Bad Request
```json
{
  "error": "string",
  "details": "string"
}
```

#### 404 Not Found
```json
{
  "error": "Resource not found",
  "resource": "string"
}
```

#### 409 Conflict
```json
{
  "error": "Constraint violation",
  "details": "string",
  "constraints": ["string"]
}
```

#### 500 Internal Server Error
```json
{
  "error": "Internal server error",
  "requestId": "string"
}
```

## Authentication
No authentication required (local-only API)

## Rate Limiting
Basic rate limiting to prevent file system overload:
- Max 10 requests/second
- Max 100 requests/minute for state saves

## Notes
1. All timestamps in ISO 8601 format
2. All IDs are UUIDv4
3. Content-Type: application/json for all requests/responses
4. CORS enabled for localhost only