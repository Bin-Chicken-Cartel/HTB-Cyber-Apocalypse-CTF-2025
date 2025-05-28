# Coding - ClockWork Guardian

## Challenge Information
- **Category**: Coding

## Description
Navigate through a maze to find the shortest path to the guardian marked 'E'. The journey won't be straightforward - you'll need to carefully choose your steps, traversing only through the safe passages marked with '0' while avoiding obstacles marked with '1'.

## Analysis

The challenge presents a pathfinding problem in a binary maze. We need to:
1. Find the shortest path from a starting position (0,0) to a destination marked with 'E'
2. Only traverse through cells containing '0' (free paths)
3. Avoid cells containing '1' (obstacles)

## Solution Approach

The main algorithm is based on a recursive backtracking approach to find the shortest path in a binary maze. The solution uses a depth-first search (DFS) with some optimizations.

### Analyzing the Code

Let's break down the provided Python code:

```python
import ast
import sys

subarrays_str = input() 
mat = ast.literal_eval(subarrays_str)

# User defined Pair class
class Pair:
    def __init__(self, x, y):
        self.first = x
        self.second = y
```

The code starts by taking input as a string representation of a 2D array and converts it to a Python object using `ast.literal_eval()`. It also defines a `Pair` class to store coordinates.

```python
# Check if it is possible to go to (x, y) from the current position
def isSafe(mat, visited, x, y):
    return (x >= 0 and x < len(mat) and y >= 0 and y < len(mat[0]) and mat[x][y] != 1 and (not visited[x][y]))
```

The `isSafe()` function checks if a cell is valid for traversal:
- Within the maze boundaries
- Not an obstacle (not '1')
- Not already visited

```python
def shortPath(mat, visited, i, j, x, y, min_dist, dist):
    if (i == x and j == y):
        # If we reach the destination marked by "E", return the current distance
        min_dist = min(dist, min_dist)
        return min_dist

    # set (i, j) cell as visited
    visited[i][j] = True
    
    # Check all four possible directions: bottom, right, top, left
    if (isSafe(mat, visited, i + 1, j)):
        min_dist = shortPath(mat, visited, i + 1, j, x, y, min_dist, dist + 1)
    if (isSafe(mat, visited, i, j + 1)):
        min_dist = shortPath(mat, visited, i, j + 1, x, y, min_dist, dist + 1)
    if (isSafe(mat, visited, i - 1, j)):
        min_dist = shortPath(mat, visited, i - 1, j, x, y, min_dist, dist + 1)
    if (isSafe(mat, visited, i, j - 1)):
        min_dist = shortPath(mat, visited, i, j - 1, x, y, min_dist, dist + 1)

    # backtrack: remove (i, j) from the visited matrix
    visited[i][j] = False
    return min_dist
```

The `shortPath()` function is the recursive backtracking algorithm that:
1. Checks if we've reached the destination
2. Marks the current cell as visited
3. Recursively tries all four possible directions
4. Backtracks by unmarking the cell
5. Returns the minimum distance found

```python
# Wrapper over shortPath() function
def shortPathLength(mat, src, dest_value):
    if (len(mat) == 0 or mat[src.first][src.second] == 1):
        return -1

    row = len(mat)
    col = len(mat[0])

    # construct an `M × N` matrix to keep track of visited cells
    visited = []
    for i in range(row):
        visited.append([None for _ in range(col)])

    # Find the destination coordinates by looking for the "E" in the matrix
    dest = None
    for i in range(row):
        for j in range(col):
            if mat[i][j] == dest_value:
                dest = Pair(i, j)
                break
        if dest:
            break

    if not dest:
        print("Destination not found!")
        return -1

    dist = sys.maxsize
    dist = shortPath(mat, visited, src.first,
                            src.second, dest.first, dest.second, dist, 0)

    if (dist != sys.maxsize):
        return dist
    return -1
```

The `shortPathLength()` function:
1. Creates a matrix to track visited cells
2. Finds the coordinates of the destination 'E'
3. Initializes the minimum distance to the maximum integer value
4. Calls the recursive `shortPath()` function
5. Returns the minimum distance or -1 if no path is found

```python
src = Pair(0, 0)
dest_value = 'E'  # The destination is marked with 'E'

dist = shortPathLength(mat, src, dest_value)

print(dist)
```

Finally, the code sets the starting position to (0,0), defines the destination marker as 'E', and prints the shortest path length.

## Key Modifications from Original Reference

The original algorithm from the GeeksForGeeks reference was designed to find paths in a binary maze where '1' represents valid paths and '0' represents obstacles. In this challenge, the logic was inverted:

1. Changed the `isSafe()` function to allow traversal through '0' cells instead of '1' cells
2. Modified the destination detection to look for 'E' instead of specific coordinates
3. Adapted the input parsing to handle the challenge-specific format

## Execution Strategy

To solve this challenge:

1. Run the provided script
2. Input the maze data as a 2D array in string format 
3. The script will calculate and output the shortest path length from (0,0) to 'E'

## Example

Consider a simple maze:
```
[
  [0, 0, 1, 0],
  [1, 0, 0, 1],
  [0, 0, 'E', 0]
]
```

Starting at position (0,0), the algorithm would find the shortest path to 'E' at position (2,2), which would be 4 steps:
- (0,0) → (0,1) → (1,1) → (1,2) → (2,2)

## Conclusion

This challenge tested our understanding of:
1. Pathfinding algorithms
2. Recursive backtracking
3. Binary maze traversal
4. Code adaptation from reference implementations

The key insight was recognizing the need to invert the traversal logic from the original algorithm to match the challenge requirements. Instead of traversing through '1' cells, we needed to traverse through '0' cells and find 'E'.

## Flag
```
HTB{CL0CKW0RK_GU4RD14N_OF_SKYW4TCH_6e9041cf508d1e5d11a42dfdb0b79ee6}
```

## Write-Up Credit
**Author**: [kkc123](https://ctf.hackthebox.com/user/profile/606424)