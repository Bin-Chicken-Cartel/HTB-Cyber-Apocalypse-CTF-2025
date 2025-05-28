# Coding - Dragon Fury

## Challenge Information
- **Category**: Coding

## Description
In this challenge, we need to find a specific combination of numbers from multiple arrays that sum up to a target value. Think of it as simulating a fighting game combo system where each move has a specific damage value, and we need to find the right sequence to defeat an enemy with exact damage.

## Analysis

The challenge presents a recursive backtracking problem. We're given:
1. Multiple arrays (subarrays) of integers
2. A target sum
3. The constraint that we must select exactly one number from each subarray

Our goal is to find a valid combination where selecting one number from each subarray gives us the exact target sum.

## Understanding the Code

Let's break down the provided Python code:

```python
import ast 

# Input the text as a single string
subarrays_str = input() # Example: "[[13, 15, 27, 17], [24, 15, 28, 6, 15, 16], [7, 25, 10, 14, 11], [23, 30, 14, 10]]" 
target = int(input()) # Input the target as an integer 

# Convert the string to a list of lists of integers
subarrays = ast.literal_eval(subarrays_str) 

def find_combination(subarrays, target, index=0, current_sum=0, path=[]): 
    if index == len(subarrays): 
        if current_sum == target: 
            return path # Return the valid combination 
        return None 
    
    # Iterate over each number in the current subarray 
    for num in subarrays[index]: 
        result = find_combination(subarrays, target, index + 1, current_sum + num, path + [num]) 
        if result: 
            return result # If a valid combination is found, return it 
    
    return None 

# Find and output the valid combination
combination = find_combination(subarrays, target) 
print(combination)
```

### Key Components:

1. **Input Parsing**: The code takes a string representation of a list of lists and converts it to Python objects using `ast.literal_eval()`.

2. **Recursive Function**: `find_combination()` is a recursive function that:
   - Takes the list of subarrays, target sum, current index, running sum, and current path
   - Checks if we've processed all subarrays and if the current sum equals the target
   - Iterates through each number in the current subarray and recursively calls itself with updated parameters
   - Returns the path if a valid combination is found

3. **Output**: The code prints the first valid combination found.

## Solving Approach

The algorithm uses a depth-first search (DFS) with backtracking to explore all possible combinations:

1. Starting with an empty path and a sum of 0
2. For each subarray, try each number, adding it to the current path and sum
3. Recursively proceed to the next subarray
4. If all subarrays have been processed and the sum equals the target, return the path
5. If no valid combination is found after exploring all possibilities, return None

### Time Complexity

If there are `n` subarrays with an average of `m` elements each, the time complexity is O(m^n) in the worst case, as we potentially need to check all possible combinations.

## Example Walkthrough

Let's say we have the following input:
```
[[1, 2], [3, 4], [5, 6]]
12
```

Our target is 12, and we need to select one number from each subarray.

The algorithm would explore:
- Start with empty path [], sum = 0
- Try 1 from first array: path = [1], sum = 1
  - Try 3 from second array: path = [1, 3], sum = 4
    - Try 5 from third array: path = [1, 3, 5], sum = 9 ≠ 12, backtrack
    - Try 6 from third array: path = [1, 3, 6], sum = 10 ≠ 12, backtrack
  - Try 4 from second array: path = [1, 4], sum = 5
    - Try 5 from third array: path = [1, 4, 5], sum = 10 ≠ 12, backtrack
    - Try 6 from third array: path = [1, 4, 6], sum = 11 ≠ 12, backtrack
- Try 2 from first array: path = [2], sum = 2
  - Try 3 from second array: path = [2, 3], sum = 5
    - Try 5 from third array: path = [2, 3, 5], sum = 10 ≠ 12, backtrack
    - Try 6 from third array: path = [2, 3, 6], sum = 11 ≠ 12, backtrack
  - Try 4 from second array: path = [2, 4], sum = 6
    - Try 5 from third array: path = [2, 4, 5], sum = 11 ≠ 12, backtrack
    - Try 6 from third array: path = [2, 4, 6], sum = 12 = 12, found!

The output would be `[2, 4, 6]`.

## Dragon Fury Context

In the context of the challenge name "Dragon Fury," we can imagine this as a fighting game scenario:
- Each subarray represents a set of possible moves or attacks
- Each number represents the damage value of a particular move
- We need to find a combo (one move from each set) that deals exactly the target amount of damage

## Challenge Solution

When presented with the actual challenge data, we would:
1. Input the provided subarrays string and target value
2. Run the given Python code
3. Get the valid combination that sums to the target value

This gives us the specific "Dragon Fury Combo" needed to solve the challenge.

## Key Insights

1. **Backtracking**: The solution demonstrates a classic backtracking approach to exploring combinations.
2. **Early Termination**: The algorithm returns as soon as a valid combination is found, without exploring all possibilities.
3. **Path Tracking**: The algorithm keeps track of the selected numbers, not just their sum.

## Flag
```
HTB{DR4G0NS_FURY_SIM_C0MB0_3a09897d65eb9cef45b70bb9a493864f}
```

This challenge tests our understanding of:
- Recursive programming
- Backtracking algorithms
- Combination problems
- Python's handling of nested data structures

## Write-Up Credit
**Author**: [kkc123](https://ctf.hackthebox.com/user/profile/606424)