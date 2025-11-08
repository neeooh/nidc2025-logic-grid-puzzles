# Data Model

## Core Entities

### Puzzle
Represents a complete logic grid puzzle definition.

```typescript
interface Puzzle {
  id: string;
  name: string;
  description?: string;
  createdAt: string;
  updatedAt: string;
  gridSize: {
    rows: number;
    columns: number;
  };
  categories: Category[];
  constraints: Constraint[];
}

interface Category {
  id: string;
  name: string;
  values: string[];
}
```

### PuzzleState
Represents the current state of a puzzle being solved.

```typescript
interface PuzzleState {
  puzzleId: string;
  lastModified: string;
  cells: CellState[];
  derivedConstraints: DerivedConstraint[];
}

interface CellState {
  position: {
    row: number;
    column: number;
  };
  value: string | null;
  possibleValues: string[];
  isUserMarked: boolean;
  derivedFrom?: string[]; // IDs of constraints that led to this state
}
```

### Constraint
Represents a logical rule in the puzzle.

```typescript
interface Constraint {
  id: string;
  type: ConstraintType;
  description: string;
  categories: string[]; // Category IDs involved
  condition: {
    operator: 'equals' | 'notEquals' | 'implies';
    values: string[];
  };
}

enum ConstraintType {
  DIRECT = 'direct',       // Direct relationship between categories
  NEGATIVE = 'negative',   // Categories that cannot be related
  POSITION = 'position',   // Relative position in grid
  CUSTOM = 'custom'        // Custom logic (with function reference)
}
```

### DerivedConstraint
Represents a constraint inferred from existing ones.

```typescript
interface DerivedConstraint {
  id: string;
  sourceConstraints: string[]; // IDs of constraints that led to this
  affectedCells: {
    position: { row: number; column: number };
    effect: 'exclude' | 'require';
    values: string[];
  }[];
}
```

## File Structure

```text
data/
├── puzzles/
│   ├── [puzzle-id].json        # Puzzle definitions
│   └── index.json             # Puzzle metadata/index
├── solutions/
│   └── [puzzle-id]/
│       └── [timestamp].json   # Solution snapshots
└── state/
    └── current.json          # Active puzzle state
```

## Validation Rules

### Puzzle
- ID must be unique
- Grid size must be positive integers
- Categories must have at least 2 values
- Must have at least one constraint
- Category values must be unique within category

### PuzzleState
- Must reference valid puzzle ID
- Cell positions must be within grid bounds
- Cell values must be from valid categories
- Derived constraints must reference valid constraints

### Constraint
- Must reference valid categories
- Condition values must exist in referenced categories
- Custom constraints must have valid function references

## State Transitions

### Cell Marking
1. User marks cell
2. System validates against constraints
3. Propagate changes to related cells
4. Update derived constraints
5. Save state to file

### Puzzle Loading
1. Load puzzle definition
2. Load latest state if exists
3. Initialize grid
4. Apply all constraints
5. Calculate derived constraints

### State Persistence
1. Throttle saves (max 1/second)
2. Save complete state snapshot
3. Maintain history of last 10 states
4. Auto-cleanup old states > 24h

## Notes
- All dates in ISO 8601 format
- All IDs are UUIDv4
- Files are UTF-8 encoded
- JSON schema validation on read/write