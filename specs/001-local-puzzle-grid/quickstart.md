# Quick Start Guide

## Prerequisites
- Node.js 20.x or later
- npm 10.x or later
- Modern web browser (Chrome, Firefox, Safari, Edge)

## Project Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd logic-grid-puzzles
```

2. Install dependencies:
```bash
# Install backend dependencies
cd backend
npm install

# Install frontend dependencies
cd ../frontend
npm install
```

3. Create data directories:
```bash
mkdir -p data/puzzles data/solutions data/state
```

## Development

### Starting the Backend
```bash
cd backend
npm run dev
```
This starts the local API server on http://localhost:3000

### Starting the Frontend
```bash
cd frontend
npm run dev
```
This starts the development server on http://localhost:5173

### Running Tests
```bash
# Backend tests
cd backend
npm test

# Frontend tests
cd frontend
npm test

# E2E tests
npm run test:e2e
```

## Project Structure

```text
├── backend/
│   ├── src/
│   │   ├── lib/
│   │   │   └── constraint-engine/    # Core puzzle logic
│   │   ├── models/                   # Data models
│   │   ├── routes/                   # API endpoints
│   │   └── services/                 # Business logic
│   └── tests/
├── frontend/
│   ├── src/
│   │   ├── components/              # UI components
│   │   ├── services/               # API client
│   │   └── styles/                 # CSS files
│   └── tests/
└── data/                          # Puzzle storage
    ├── puzzles/
    ├── solutions/
    └── state/
```

## Creating a New Puzzle

1. Create a puzzle definition in `data/puzzles/[puzzle-id].json`:
```json
{
  "id": "example-puzzle",
  "name": "Simple Logic Grid",
  "gridSize": {
    "rows": 3,
    "columns": 3
  },
  "categories": [
    {
      "id": "category1",
      "name": "Names",
      "values": ["Alice", "Bob", "Charlie"]
    },
    {
      "id": "category2",
      "name": "Colors",
      "values": ["Red", "Blue", "Green"]
    }
  ],
  "constraints": [
    {
      "id": "constraint1",
      "type": "direct",
      "description": "Alice likes Red",
      "categories": ["category1", "category2"],
      "condition": {
        "operator": "equals",
        "values": ["Alice", "Red"]
      }
    }
  ]
}
```

2. Update the puzzle index:
```bash
npm run update-puzzle-index
```

## Development Tips

1. **Hot Reloading**
   - Backend and frontend both support hot reloading
   - File changes trigger automatic rebuilds

2. **Debug Mode**
   ```bash
   # Backend debugging
   DEBUG=app:* npm run dev

   # Frontend debugging
   npm run dev -- --debug
   ```

3. **Test Data**
   ```bash
   # Generate sample puzzles
   npm run generate-samples

   # Reset to clean state
   npm run reset-data
   ```

4. **Common Issues**

   - If the app won't start, check:
     1. Node.js version (`node --version`)
     2. Port conflicts (3000, 5173)
     3. File permissions in data directory

   - If changes don't propagate:
     1. Check browser console
     2. Verify API responses
     3. Check file system writes

## Additional Resources

- [API Documentation](./contracts/api.md)
- [Data Model](./data-model.md)
- [Research Notes](./research.md)