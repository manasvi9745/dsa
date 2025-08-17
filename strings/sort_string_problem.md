# Sort Characters in String

## Problem Statement
Given a string consisting of lowercase letters, arrange all its letters in ascending order.

### Example 1:
```
Input:  S = "edcab"
Output: "abcde"
Explanation: characters are in ascending order in "abcde".
```

### Example 2:
```
Input:  S = "xzy"
Output: "xyz"
Explanation: characters are in ascending order in "xyz".
```

---

## Your Task
You don't need to read input or print anything.  
Your task is to complete the function **sort()** which takes the string as input and returns the modified string.

### Expected Time Complexity
O(|S| * log |S|)

### Expected Auxiliary Space
O(1)

### Constraints
- 1 ≤ |S| ≤ 10^5  
- S contains only lowercase alphabets.

---

## ✅ Solution

### Approach
- Convert the string into a list of characters.
- Use the built-in `sort()` function or `sorted()` in C++/Python which works in O(n log n).
- Join the characters back to form the sorted string.

### C++ Code
```cpp
class Solution {
  public:
    string sort(string S) {
        sort(S.begin(), S.end());
        return S;
    }
};
```

### Python Code
```python
class Solution:
    def sort(self, S: str) -> str:
        return ''.join(sorted(S))
```
