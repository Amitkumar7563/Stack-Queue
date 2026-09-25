# Next Greater Element II — LeetCode 503

🔗 LeetCode: https://leetcode.com/problems/next-greater-element-ii/

## 🧠 Problem

For every element, find the **first greater element on its right**.

The array is **circular**, so after `nums[n-1]`, we can continue from `nums[0]`.

Example:

```text
nums = [1, 2, 1]

answer = [2, -1, 2]
```

For the last `1`:

```text
1 → [back to beginning] → 2
```

---

# 1️⃣ Brute Force — My Approach

### 💡 Intuition

For every `nums[i]`:

1. Start checking from the next element.
2. Move forward.
3. Because the array is circular, use:

```cpp
idx = (i + j) % n;
```

4. The **first** element greater than `nums[i]` is the answer.
5. If we complete one full circle → `-1`.

### Code

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {

        int n = nums.size();
        vector<int> result(n, -1);

        for(int i = 0; i < n; i++) {

            for(int j = 1; j < n; j++) {

                int idx = (i + j) % n;

                if(nums[idx] > nums[i]) {
                    result[i] = nums[idx];
                    break;
                }
            }
        }

        return result;
    }
};
```

### 🔑 Important observation

```cpp
(i + j) % n
```

handles the **circular array**.

Example:

```text
n = 5
i = 3

j = 1 → (3+1)%5 = 4
j = 2 → (3+2)%5 = 0
j = 3 → (3+3)%5 = 1
j = 4 → (3+4)%5 = 2
```

So:

```text
3 → 4 → 0 → 1 → 2
```

### Complexity

```text
Time  : O(n²)
Space : O(1) extra
```

The problem's circular scan naturally leads to a quadratic worst case.

---

# 2️⃣ Better / Optimal — Monotonic Stack

## 💡 Why do we need optimization?

In brute force, for every element we repeatedly search:

```text
"Where is my next greater?"
```

Many elements are checked again and again.

Instead, use a **monotonic stack** to remember elements that are still waiting for their next greater element.

---

# 🧠 Main Intuition

Think:

```text
Element is waiting for a greater element
                ↓
       keep it in the stack
                ↓
A bigger element arrives
                ↓
   it becomes the answer
```

For example:

```text
nums = [2, 1, 3]

1 is waiting
2 is waiting

3 arrives

3 > 1 → answer of 1 = 3
3 > 2 → answer of 2 = 3
```

So we don't search again for every element.

---

# 🔄 Circular Array Trick

The main problem is:

```text
[1, 2, 1]
       ↑
       next can be from beginning
```

Instead of actually copying the array:

```text
[1,2,1,1,2,1]
```

we **pretend** the array exists twice.

Use:

```cpp
i % n
```

So:

```text
i = 0 → 0
i = 1 → 1
i = 2 → 2
i = 3 → 0
i = 4 → 1
i = 5 → 2
```

This gives us the circular behaviour without creating another array. Traversing twice is sufficient because an element only needs to inspect at most one complete wrap.

---

# 🔥 Why Traverse From Right to Left?

For a normal **Next Greater Element**, we want:

```text
current → next greater on right
```

If we traverse from **right to left**, the elements on the right are already inside the stack.

Example:

```text
[2, 1, 3]

        current
           ↓
[2, 1, 3]
       ← ← ←
```

So stack contains possible greater candidates.

---

# 📚 Monotonic Stack Rule

Before finding the answer:

```cpp
while(!st.empty() && st.top() <= nums[index])
    st.pop();
```

### Why pop `<=`?

If:

```text
stack top = 5
current   = 5
```

5 is **not greater than 5**.

So it cannot be the answer.

If:

```text
stack top = 3
current   = 5
```

3 also cannot be the answer.

Therefore remove both smaller **and equal** elements.

After popping:

```text
stack top > current
```

So the top is the next greater candidate.

---

# 3️⃣ Optimal Code

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {

        int n = nums.size();
        vector<int> answer(n, -1);
        stack<int> candidates;

        for(int i = 2*n - 1; i >= 0; i--) {

            int index = i % n;

            while(!candidates.empty() &&
                  candidates.top() <= nums[index]) {
                candidates.pop();
            }

            if(i < n && !candidates.empty()) {
                answer[index] = candidates.top();
            }

            candidates.push(nums[index]);
        }

        return answer;
    }
};
```

---

# 🔍 Understand These 3 Lines

### 1. Simulate circular array

```cpp
int index = i % n;
```

Instead of creating:

```text
[1,2,1,1,2,1]
```

we access the original array using modulo.

---

### 2. Remove useless candidates

```cpp
while(!candidates.empty() &&
      candidates.top() <= nums[index])
    candidates.pop();
```

After this:

```text
stack.top() > nums[index]
```

Therefore stack top can be the next greater element.

---

### 3. Why `i < n`?

```cpp
if(i < n)
    answer[index] = candidates.top();
```

We traverse `2n` times only to **give every element access to the circular part**.

But we only need to write the final answer during the **real/original pass**.

Think:

```text
First half  → calculate answers
Second half → provide circular candidates
```

---

# 🧪 Dry Run

```text
nums = [1, 2, 1]
```

Conceptually:

```text
[1, 2, 1 | 1, 2, 1]
```

We traverse from right → left.

For the last `1` in the original array:

```text
1 → wrap → 2
```

Stack finds:

```text
2 > 1
```

Therefore:

```text
answer[2] = 2
```

Final:

```text
[2, -1, 2]
```

---

# 🧠 Brute → Optimal Thinking

Remember this progression:

```text
Brute Force
   ↓
For every element search forward
   ↓
Circular → (i+j)%n
   ↓
O(n²)
```

Then ask:

```text
"Am I checking the same elements again?"
                ↓
              YES
                ↓
"Can I remember useful candidates?"
                ↓
              STACK
                ↓
Monotonic Stack
                ↓
Traverse 2n positions
                ↓
i % n → circular
                ↓
O(n)
```

---

# ⭐ Pattern to Remember

### Circular Array

```cpp
index = i % n;
```

### Next Greater

```cpp
while(!st.empty() && st.top() <= current)
    st.pop();
```

### Remaining top

```text
st.top() > current
```

→ possible next greater element.

### Complexity

```text
Time  : O(n)
Space : O(n)
```

Each element is pushed and popped at most once, so the stack operations are linear overall.

---

## 🔑 One-Line Revision

> **Circular NGE = traverse 2n times + modulo + monotonic decreasing stack.**
