# Next Greater Element I

**Pattern:** 🔥 Monotonic Stack

## 1. Problem

For every element of `nums1`:

* Find it in `nums2`
* Look to its right
* Find the **first greater element**
* If none → `-1`

Example:

```text
nums2 = [1, 3, 4, 2]

1 → 3
3 → 4
4 → -1
2 → -1
```

---

# 2. Brute Force

### Intuition

For every `nums1[i]`:

```text
Find it in nums2
      ↓
Search right side
      ↓
First greater = answer
```

```cpp
for (int i = 0; i < n1; i++) {
    for (int j = 0; j < n2; j++) {

        if (nums1[i] == nums2[j]) {

            for (int k = j + 1; k < n2; k++) {
                if (nums2[k] > nums2[j]) {
                    result[i] = nums2[k];
                    break;
                }
            }
        }
    }
}
```

**Time:** `O(n1 × n2²)`

---

# 3. Better — Monotonic Stack

### 🧠 Core Intuition

> **Stack mein woh elements rakho jinka Next Greater Element abhi nahi mila.**

Process `nums2` from **left → right**.

For current `x`:

```cpp
while (!st.empty() && x > st.top())
```

If `x > st.top()`:

```text
x = first greater element of st.top()
```

So:

```cpp
nextGreater[st.top()] = x;
st.pop();
```

Then:

```cpp
st.push(x);
```

---

# 4. Why `while`?

Because one element can be greater than **multiple** previous elements.

Example:

```text
nums = [1, 2, 3]

When x = 3:

3 > 2 → answer of 2
3 > 1 → answer of 1
```

Therefore:

```cpp
while(...)
```

not `if`.

---

# 5. Code

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1,
                                   vector<int>& nums2) {

        unordered_map<int, int> nextGreater;
        stack<int> st;

        for (int x : nums2) {

            while (!st.empty() && x > st.top()) {
                nextGreater[st.top()] = x;
                st.pop();
            }

            st.push(x);
        }

        // Remaining elements have no NGE
        while (!st.empty()) {
            nextGreater[st.top()] = -1;
            st.pop();
        }

        vector<int> ans;

        for (int x : nums1) {
            ans.push_back(nextGreater[x]);
        }

        return ans;
    }
};
```

---

# 6. Dry Run

```text
nums2 = [1, 3, 4, 2]

x=1 → push

Stack: [1]

x=3
3 > 1
1 → 3
push 3

Stack: [3]

x=4
4 > 3
3 → 4
push 4

Stack: [4]

x=2
2 > 4 ❌
push 2

Stack: [4, 2]

End:
4 → -1
2 → -1
```

Final:

```text
1 → 3
3 → 4
4 → -1
2 → -1
```

---

# 7. Complexity

### Brute

```text
Time  → O(n1 × n2²)
Space → O(n1)
```

### Monotonic Stack

```text
Time  → O(n1 + n2)
Space → O(n2)
```

Why `O(n)`?

> Every element is **pushed once and popped at most once**.

---

# ⭐ Pattern to Remember

Whenever question says:

```text
Next/Previous
Greater/Smaller
First/Nearest
```

Think:

```text
🔥 MONOTONIC STACK
```

### Next Greater:

```cpp
while (!st.empty() && current > st.top())
```

### Mental Model:

```text
Stack = "waiting elements"

Current element
      ↓
Can it solve stack.top()?
      ↓
 YES → answer + pop
 NO  → push current
```

**Don't memorize the code. Remember:**

> **Current element greater than stack top → current is the answer for stack top.**
