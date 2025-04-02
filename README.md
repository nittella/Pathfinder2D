# Pathfinder2D Documentation

## Overview
The `Pathfinder2D` system provides a pathfinding algorithm for a 2D grid-based environment using a weighted graph approach. It determines the shortest path between two points, considering movement costs and obstacles.

---

## Enumerations
### `Pathfinder2DResultStatus`
Defines the possible results of a pathfinding attempt:
- `SUCCESS`: A valid path was found.
- `NOT_FOUND`: No valid path exists.

### `NodeConnectionType`
Specifies how nodes connect within the grid:
- `RectangleWithDiagonals`: Allows diagonal movement.
- `RectangleNoDiagonals`: Restricts movement to vertical and horizontal directions.
- `Hexagone`: Uses a hexagonal grid connection model.

---

## Structs
### `Pathfinder2DResult`
Represents the outcome of a pathfinding request.

**Fields:**
- `List<Vector2Int> Path`: Stores the sequence of grid positions forming the computed path.
- `Pathfinder2DResultStatus Status`: Indicates success or failure.
- `float weightedDistance`: The total weighted cost of the path.
- `float distance`: The number of steps in the path.

---

## Classes

### `Node`
Represents a grid node used in the pathfinding process.

**Fields:**
- `Vector2Int Vector`: The position of the node.
- `float FactDistanceFromStart`: The actual distance from the starting node.
- `float EmpiricalDistanceToEnd`: The estimated distance to the target node.
- `Node Parent`: Reference to the previous node in the path.
- `int Index`: Heap index for priority queue operations.

**Methods:**
- `float GetTotalDistance()`: Returns the sum of actual and estimated distances.
- `int CompareTo(Node other)`: Compares nodes based on their total distances.

---

### `Pathfinder2D`
Handles pathfinding operations within a 2D grid.

**Fields:**
- `Dictionary<Vector2Int, float> _weightedMap`: Stores movement costs per grid position.
- `List<Vector2Int> _movementBlockers`: Contains positions that block movement.
- `NodeConnectionType _nodeConnectionType`: Determines how nodes connect.

**Constructors:**
- `Pathfinder2D(Dictionary<Vector2Int, float> weightedMap, List<Vector2Int> movementBlockers, NodeConnectionType nodeConnectionType)`: Initializes pathfinding with movement costs, obstacles, and connection type.
- `Pathfinder2D(Dictionary<Vector2Int, float> weightedMap, NodeConnectionType nodeConnectionType)`: Initializes without movement blockers.

**Methods:**
- `Pathfinder2DResult FindPath(Vector2Int start, Vector2Int end)`: Finds the shortest path between two points.
- `private float GetEmpiricalDistanceToEndPoint(Vector2Int node, Vector2Int end)`: Estimates the distance from a node to the endpoint.
- `private bool IsEligibleNode(Vector2Int node)`: Checks if a node is traversable.
- `private List<Vector2Int> GetConnectedVectors(Vector2Int vector)`: Returns a list of adjacent valid positions based on the connection type.

---

## Usage
```csharp
void Start()
{
    path = new();
    Dictionary<Vector2Int, float> weightedTilemap = GetWeightedTilemap();
    NodeConnectionType connectionType = NodeConnectionType.RectangleWithDiagonals;
    pathfinder = new Pathfinder2D(weightedTilemap, connectionType);
}

void Update()
{
    Vector3 cursorPosition = camera.ScreenToWorldPoint(Input.mousePosition);
    Vector2Int cursorCell = (Vector2Int)pathTilemap.WorldToCell(cursorPosition);
    Vector2Int playerCell = (Vector2Int)pathTilemap.WorldToCell(player.gameObject.transform.position);
    Pathfinder2DResult pathResult = pathfinder.FindPath(playerCell, cursorCell);
    path = pathResult.Path;
    pathTilemap.ClearAllTiles();
    ShowPath();
    UpdateDistance(pathResult.distance);
    UpdateWeightedDistance(pathResult.weightedDistance);

}
```

---

## Conclusion
`Pathfinder2D` provides a flexible and efficient way to compute paths in a grid-based 2D environment, considering movement costs and obstacles. It is suitable for game AI and simulation applications that require pathfinding solutions.

