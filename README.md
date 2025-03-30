# Pathfinder2D
The Pathfinder2D class implements a pathfinding system for a 2D grid-based environment using a weighted graph approach. This class uses the A* (A-star) algorithm to find the shortest path from a start position to a target position. It supports including rectangular grids (both with and without diagonals) and hexagonal grids.
Certainly! Here's the updated Pathfinder documentation with the inclusion of the `Heap<T>` class documentation. I've integrated both the `Pathfinder2D` and `Heap<T>` documentation in one cohesive document.

#### Namespace: `KwaaktjePathfinder2D`

---

### **Pathfinder2D Class**

The `Pathfinder2D` class implements a pathfinding system for a 2D grid-based environment using a weighted graph approach. This class uses the A* (A-star) algorithm to find the shortest path from a start position to a target position. It supports different node connection types, including rectangular grids (both with and without diagonals) and hexagonal grids.

---

#### **Constructor:**

```csharp
public Pathfinder2D(Dictionary<Vector2Int, float> weightedMap, List<Vector2Int> movementBlockers, NodeConnectionType nodeConnectionType)
```

**Description:**
- Initializes the `Pathfinder2D` with a weighted grid, a list of movement blockers, and a node connection type (Rectangle with or without diagonals or Hexagonal).
- `weightedMap`: Dictionary mapping grid positions to movement costs.
- `movementBlockers`: List of grid positions that block movement (obstacles).
- `nodeConnectionType`: Defines how nodes are connected in the grid (with or without diagonals, or hexagonal).

---

```csharp
public Pathfinder2D(Dictionary<Vector2Int, float> weightedMap, NodeConnectionType nodeConnectionType)
```

**Description:**
- Initializes the `Pathfinder2D` with a weighted grid and a node connection type without any movement blockers.
- `weightedMap`: Dictionary mapping grid positions to movement costs.
- `nodeConnectionType`: Defines how nodes are connected in the grid (with or without diagonals, or hexagonal).

---

#### **Method:**

```csharp
public List<Vector2Int> FindPath(Vector2Int start, Vector2Int end)
```

**Description:**
- Finds the shortest path from the start position to the end position using the A* algorithm.
- Returns a list of grid positions representing the path from start to end, or an empty list if no valid path exists.

---

#### **Private Helper Methods:**

```csharp
private float GetEmpiricalDistanceToEndPoint(Vector2Int node, Vector2Int end)
```

**Description:**
- Calculates the empirical distance (straight-line distance) from a node to the endpoint using the Euclidean distance formula.

---

```csharp
private bool IsEligibleNode(Vector2Int node)
```

**Description:**
- Checks if a node is eligible for pathfinding (i.e., it exists in the weighted map and is not a movement blocker).

---

```csharp
private List<Vector2Int> GetConnectedVectors(Vector2Int vector)
```

**Description:**
- Gets the list of neighboring grid positions (nodes) that are connected to the current node based on the specified connection type (rectangle with or without diagonals, hexagonal).

---

### **Heap<T> Class**

The `Heap<T>` class is a generic implementation of a heap (priority queue) data structure. It is used internally in the pathfinding algorithm for efficient retrieval of the highest-priority node, allowing quick access to the node with the lowest total cost.

#### **Constructor:**

```csharp
public Heap(int size)
```

**Description:**
- Initializes a new heap with a predefined size.
- `size`: The maximum number of elements the heap can store.

---

#### **Method:**

```csharp
public int Count { get; }
```

**Description:**
- Gets the current number of elements in the heap.

---

```csharp
public void Add(T item)
```

**Description:**
- Adds an item to the heap while maintaining the heap property (highest-priority item at the root).
- The heap will reorder itself to ensure that the highest-priority item is always at index 0.

---

```csharp
public T Pop()
```

**Description:**
- Removes and returns the highest-priority item from the heap (the root).
- After removal, the heap reorders itself to maintain the heap property.

---

```csharp
public bool Contains(T item)
```

**Description:**
- Checks whether the heap contains a specific item.
- Returns `true` if the item exists in the heap, `false` otherwise.

---

```csharp
public void Update(T item)
```

**Description:**
- Updates the priority of an item in the heap, ensuring the heap property is maintained.
- Used when a node's cost changes in pathfinding (e.g., when a better path is found).

---

#### **Private Helper Methods:**

```csharp
private int GetLeftChildIndex(T item)
```

**Description:**
- Returns the index of the left child of the given item.

---

```csharp
private int GetRightChildIndex(T item)
```

**Description:**
- Returns the index of the right child of the given item.

---

```csharp
private T GetParent(T item)
```

**Description:**
- Returns the parent of the given item.

---

```csharp
private void SortDown(T item)
```

**Description:**
- Reorders the heap from top to bottom (starting at the root) to maintain the heap property.

---

```csharp
private void SortUp(T item)
```

**Description:**
- Reorders the heap from bottom to top (starting at the leaf node) to maintain the heap property.

---

```csharp
private void Swap(T item1, T item2)
```

**Description:**
- Swaps the positions of two items in the heap.

---

### **IHeapItem<T> Interface**

This interface defines items that can be stored in a heap and supports priority-based comparisons.

#### **Method:**

```csharp
int Index { get; set; }
```

**Description:**
- Gets or sets the index of the item within the heap. The heap uses this to maintain the item's position.

---

```csharp
int CompareTo(T other)
```

**Description:**
- Compares the current item to another item to determine their priority (used to maintain the heap property).

---

### Example Usage in `GameManager`

Here’s how the `Heap<T>` class is used inside the `Pathfinder2D` class for pathfinding:

```csharp
Heap<Node> opened = new Heap<Node>(_weightedMap.Count);
```

- A `Heap<Node>` is created to efficiently store nodes that need to be processed. The nodes are prioritized based on their total cost (fact distance + empirical distance).

---

### **Usage example of `Pathfinder2D` **

Here's how the `Pathfinder2D` class can be used:

```csharp
public class GameManager : MonoBehaviour
{
    public Tilemap pathTilemap; // Tilemap to show a path
    public Tilemap mazeTileMap; // Tilemap with walkable cells

    private List<Vector2Int> path;
    private Pathfinder2D pathfinder;

    
    void Start()
    {
        Dictionary<Vector2Int, float> weightedTilemap = GetWeightedTilemap();
        NodeConnectionType connectionType = NodeConnectionType.RectangleWithDiagonals;
        pathfinder = new Pathfinder2D(weightedTilemap, connectionType);
    }
    
    void Update()
    {
       Vector3 cursorPosition = camera.ScreenToWorldPoint(Input.mousePosition);
       Vector2Int cursorCell = (Vector2Int)pathTilemap.WorldToCell(cursorPosition);
       Vector2Int playerCell = (Vector2Int)pathTilemap.WorldToCell(player.gameObject.transform.position);
       path = pathfinder.FindPath(playerCell, cursorCell);
       pathTilemap.ClearAllTiles();
       ShowPath();
    }
}
```
- The `GameManager` interacts with the `Pathfinder2D` class to find paths by calling `FindPath()`. The `pathfinder` uses the `Heap<T>` to efficiently find the optimal path from the start to the end using A*.
