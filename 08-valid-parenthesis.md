# Valid Parentheses — LeetCode 20

## Problem

Given a string containing `()`, `{}`, `[]`, check whether the brackets are valid.

### Valid

```text
()       → true
()[]{}   → true
([])     → true
```

### Invalid

```text
(]       → false
([)]     → false
((       → false
```

---

## Intuition

> **Last opened bracket must be the first one closed → LIFO → Stack.**

* Opening bracket → **Push**
* Closing bracket → **Check `top()` → Match → Pop**
* Mismatch → **Return `false`**
* End → **Stack must be empty**

---

## Example

For:

```text
"([])"
```

```text
( → push
[ → push
] → match [ → pop
) → match ( → pop
```

Stack is empty → `true`.

---

## C++ Code

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<char> st;

        for(char ch : s) {

            // Opening bracket
            if(ch == '(' || ch == '{' || ch == '[') {
                st.push(ch);
                continue;
            }

            // Closing bracket with no opening bracket
            if(st.empty())
                return false;

            if(ch == ')' && st.top() != '(')
                return false;

            if(ch == '}' && st.top() != '{')
                return false;

            if(ch == ']' && st.top() != '[')
                return false;

            st.pop();
        }

        return st.empty();
    }
};
```

---

## Key Concept

**Universal Stack Pattern:**

```text
Most recent unresolved element
            ↓
          STACK
```

Examples: **Valid Parentheses, NGE, Daily Temperatures, Asteroid Collision, Remove K Digits**

---

## Complexity

* **Time:** `O(n)`
* **Space:** `O(n)`

### Memory Trick

> **Open → Push | Close → Match + Pop | End → Empty**
