# 📌 Next Greater Element I

**LeetCode:** Next Greater Element I

---

## 1. Problem Idea

For every element `x` in `nums1`:

1. Find `x` inside `nums2`.
2. Look to its **right** in `nums2`.
3. Find the **first element greater than `x`**.
4. If no greater element exists → `-1`.

### Example

```text
nums2 = [1, 3, 4, 2]

For 1 → 3
For 3 → 4
For 4 → -1
For 2 → -1
```

So:

```text
Next Greater:
1 → 3
3 → 4
4 → -1
2 → -1
```

---

# 2. Brute Force Approach

### 🧠 Intuition

For every element of `nums1`:

> "Pehle mujhe `nums2` mein ye element find karna hai, phir uske right side mein jaakar pehla greater element search karna hai."

So we use **3 loops**:

```text
nums1 element
      ↓
find it in nums2
      ↓
search right side
      ↓
find first greater
```

### Code

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {

        int n1 = nums1.size();
        int n2 = nums2.size();

        vector<int> result(n1, -1);

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

        return result;
    }
};
```

---

# 3. Brute Force Complexity

There are potentially 3 nested loops:

```text
i → nums1
j → nums2
k → right side of nums2
```

### Time

```text
O(n1 × n2²)
```

### Space

```text
O(n1)
```

for the result array.

---

# 4. 🔥 Better Approach — Monotonic Stack

The important question is:

> **Can we find the Next Greater Element for ALL elements of `nums2` in one traversal?**

Yes.

Instead of solving:

```text
1 → find answer
3 → find answer
4 → find answer
2 → find answer
```

individually, we process `nums2` **from left to right** and calculate answers together.

---

# 5. 🧠 Main Intuition

Suppose:

```text
nums2 = [1, 3, 4, 2]
```

We maintain a stack of elements whose **Next Greater Element is still unknown**.

When a new element `x` comes:

```cpp
while (!st.empty() && x > st.top())
```

If:

```text
x > st.top()
```

then we have discovered the answer for `st.top()`.

### Example

```text
Stack:
[1]

Current x = 3

3 > 1
```

Therefore:

```text
NGE(1) = 3
```

So:

```cpp
nextGreater[1] = 3;
st.pop();
```

---

# 6. 🔥 The Most Important Observation

When processing from **left → right**:

> If the current element `x` is greater than the stack top, then `x` is the **first greater element** for that stack top.

Why?

Because everything before `x` has already been processed.

If some earlier element was greater than `st.top()`, we would have already removed `st.top()`.

Therefore, when `x` finally becomes greater:

```text
x = FIRST greater element
```

This is the core idea behind **Monotonic Stack**.

---

# 7. Why Do We Keep a Stack?

Suppose:

```text
nums2 = [5, 2, 1]
```

Process:

```text
5 → push
2 → 2 is not greater than 5 → push
1 → 1 is not greater than 2 → push
```

Stack:

```text
TOP
 ↓
1
2
5
```

These elements are waiting for their Next Greater Element.

At the end:

```text
5 → -1
2 → -1
1 → -1
```

So:

```cpp
while (!st.empty()) {
    nextGreater[st.top()] = -1;
    st.pop();
}
```

---

# 8. 🔍 Why `while`, Not `if`?

This is extremely important.

Suppose:

```text
nums2 = [1, 2, 3]
```

After processing:

```text
1
2
```

Stack:

```text
TOP → 2
      1
```

Now:

```text
x = 3
```

`3` is greater than **both**:

```text
3 > 2
3 > 1
```

Therefore we need:

```cpp
while (!st.empty() && x > st.top())
```

not:

```cpp
if (x > st.top())
```

The `while` allows one new element to resolve **multiple waiting elements**.

---

# 9. Complete Better Approach

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {

        unordered_map<int, int> nextGreater;
        stack<int> st;

        // Find NGE for every element of nums2
        for (int x : nums2) {

            while (!st.empty() && x > st.top()) {

                nextGreater[st.top()] = x;
                st.pop();
            }

            st.push(x);
        }

        // Remaining elements have no greater element
        while (!st.empty()) {

            nextGreater[st.top()] = -1;
            st.pop();
        }

        // Build answer for nums1
        vector<int> ans;

        for (int x : nums1) {
            ans.push_back(nextGreater[x]);
        }

        return ans;
    }
};
```

---

# 10. 🔎 Dry Run

```text
nums2 = [1, 3, 4, 2]
```

### x = 1

Stack empty:

```text
push(1)
```

```text
Stack: [1]
Map: {}
```

---

### x = 3

```text
3 > 1
```

So:

```text
NGE(1) = 3
```

Pop `1`.

Then push `3`.

```text
Stack: [3]
Map:
1 → 3
```

---

### x = 4

```text
4 > 3
```

So:

```text
NGE(3) = 4
```

Pop `3`.

Push `4`.

```text
Stack: [4]

Map:
1 → 3
3 → 4
```

---

### x = 2

```text
2 > 4 ❌
```

So `4` is still waiting.

Push `2`.

```text
Stack:
TOP → 2
      4
```

---

### End

Stack contains:

```text
[4, 2]
```

Nobody to the right is greater because there is no element left.

Therefore:

```text
NGE(4) = -1
NGE(2) = -1
```

Final map:

```text
1 → 3
3 → 4
4 → -1
2 → -1
```

---

# 11. Why `unordered_map`?

We calculate NGE for every element of `nums2`.

But the final answer is required only for `nums1`.

So we store:

```text
element → next greater element
```

Example:

```text
1 → 3
3 → 4
4 → -1
2 → -1
```

Then for every `nums1[i]`:

```cpp
nextGreater[nums1[i]]
```

gives the answer in **O(1) average time**.

---

# 12. Why Does This Become O(n)?

At first, the `while` loop may look like another nested loop.

But every element:

```text
Pushed → once
Popped → at most once
```

An element cannot be popped again after being removed.

Therefore total stack operations are approximately:

```text
n pushes + n pops
```

So:

```text
Time = O(n)
```

for processing `nums2`.

Then:

```text
O(n1)
```

for building the answer.

Overall:

```text
O(n + n1)
```

---

# 13. Complexity

Let:

```text
n = nums2.size()
m = nums1.size()
```

### Brute Force

```text
Time  → O(m × n²)
Space → O(m)
```

### Monotonic Stack

```text
Time  → O(n + m)
Space → O(n)
```

The `O(n)` space comes from:

```text
stack + unordered_map
```

---

# 14. 🧠 Reusable Monotonic Stack Pattern

Whenever you see:

* Next Greater Element
* Next Smaller Element
* Previous Greater Element
* Previous Smaller Element
* First greater/smaller element
* Nearest greater/smaller element

Think:

```text
🔥 MONOTONIC STACK
```

Typical pattern:

```cpp
for (int x : nums) {

    while (!st.empty() && CONDITION) {

        // Current x is the answer
        // for st.top()

        st.pop();
    }

    st.push(x);
}
```

For **Next Greater Element**:

```cpp
while (!st.empty() && x > st.top())
```

---

# 15. ⭐ The Most Important Mental Model

Don't memorize the code.

Remember this:

> **Stack = elements waiting for their answer.**

When a new element comes:

```text
Current element
      ↓
Can it solve the top?
      ↓
   YES → assign answer + pop
   NO  → keep waiting
```

And because one current element can solve multiple waiting elements:

```cpp
while (...)
```

is used.

---

# 16. 📝 One-Line Revision

```text
Next Greater Element
        ↓
Process left → right
        ↓
Keep unresolved elements in stack
        ↓
Current x > stack.top()
        ↓
x is the NGE of stack.top()
        ↓
store answer + pop
        ↓
push x
        ↓
remaining stack → -1
```

---

# 🎯 Future Question Trigger

When you see:

> **"Find the first/next greater element on the right."**

Immediately think:

```text
NEXT GREATER
     ↓
MONOTONIC STACK
     ↓
while (!st.empty() && current > st.top())
```

This is the pattern you should remember, not just the LeetCode solution.
