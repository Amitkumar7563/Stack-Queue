# Sum of Subarray Minimums — Monotonic Stack

🔗 [LeetCode: Sum of Subarray Minimums](https://leetcode.com/problems/sum-of-subarray-minimums/)

## 💡 Problem

Given an array `arr`, find the **sum of minimum elements of every contiguous subarray**.

Example:

```text
arr = [3,1,2,4]
```

All subarrays:

```text
[3]       → 3
[1]       → 1
[2]       → 2
[4]       → 4
[3,1]     → 1
[1,2]     → 1
[2,4]     → 2
[3,1,2]   → 1
[1,2,4]   → 1
[3,1,2,4] → 1
```

Answer:

```text
3 + 1 + 2 + 4 + 1 + 1 + 2 + 1 + 1 + 1 = 17
```

---

## 💡 Core Idea

Instead of generating every subarray, think from the **element's point of view**.

For every `arr[i]`, find:

> **How many subarrays have `arr[i]` as their minimum?**

Then:

```text
Contribution = number of subarrays × arr[i]
```

So we need to find how far `arr[i]` can expand on both sides while remaining the minimum.

For this we use:

- `PSEE` → Previous Smaller Element or Equal
- `NSE` → Next Smaller Element

---

## 🔹 PSEE — Previous Smaller Element or Equal

For every index `i`, find the nearest element on the left which is:

```text
<= arr[i]
```

If there is no such element:

```text
PSEE[i] = -1
```

### Example

```text
arr = [3,1,2,4]

PSEE = [-1,-1,1,2]
```

For `2` at index `2`:

```text
3  1  2  4
   ↑  ↑
   1  2
```

Previous smaller or equal is `1`.

So:

```text
PSEE[2] = 1
```

### Stack condition

While finding PSEE:

```cpp
while (!st.empty() && arr[st.back()] > arr[i])
    st.pop_back();
```

Remember:

```text
PSEE → pop >
```

---

## 🔹 NSE — Next Smaller Element

For every index `i`, find the nearest element on the right which is:

```text
< arr[i]
```

If there is no such element:

```text
NSE[i] = n
```

For:

```text
arr = [3,1,2,4]
```

We get:

```text
NSE = [1,4,4,4]
```

For `3` at index `0`:

```text
3  1  2  4
   ↑
   next smaller = 1
```

So:

```text
NSE[0] = 1
```

### Stack condition

While finding NSE:

```cpp
while (!st.empty() && arr[st.back()] >= arr[i])
    st.pop_back();
```

Remember:

```text
NSE → pop >=
```

---

## 🔥 Why Different Conditions?

Notice:

```text
PSEE → >
NSE  → >=
```

Why?

Because duplicate values can exist.

If we use the same comparison on both sides, the same subarray can be counted more than once.

So we use one side as:

```text
smaller or equal
```

and the other side as:

```text
strictly smaller
```

This gives every subarray a unique minimum element.

### Easy Trick

```text
PSEE → pop >
NSE  → pop >=
```

---

## 🔹 Counting Subarrays

Suppose:

```text
PSEE[i] = left
NSE[i]  = right
```

Then:

```text
left choices  = i - PSEE[i]
right choices = NSE[i] - i
```

Therefore:

```text
Number of subarrays
= left choices × right choices
```

And contribution is:

```text
Contribution
= (i - PSEE[i]) × (NSE[i] - i) × arr[i]
```

---

## 🧠 Example

Take:

```text
arr = [3,1,2,4]
```

For `arr[2] = 2`:

```text
PSEE[2] = 1
NSE[2]  = 4
```

So:

```text
left choices = 2 - 1 = 1
right choices = 4 - 2 = 2
```

Number of subarrays where `2` is minimum:

```text
1 × 2 = 2
```

Those subarrays are:

```text
[2]
[2,4]
```

Contribution:

```text
2 × 2 = 4
```

---

## 🔹 Complete Example

For:

```text
arr = [3,1,2,4]
```

We get:

```text
PSEE = [-1,-1,1,2]
NSE  = [1,4,4,4]
```

Now calculate contribution of every element:

| Element | PSEE | NSE | Left | Right | Contribution |
|--------:|-----:|----:|-----:|------:|-------------:|
| 3 | -1 | 1 | 1 | 1 | 3 |
| 1 | -1 | 4 | 2 | 3 | 6 |
| 2 | 1 | 4 | 1 | 2 | 4 |
| 4 | 2 | 4 | 1 | 1 | 4 |

Total:

```text
3 + 6 + 4 + 4 = 17
```

---

## 🔹 Why Monotonic Stack?

We need the nearest:

```text
smaller / smaller-or-equal
```

element on both sides.

A monotonic stack helps us find these in:

```text
O(n)
```

instead of checking every element to the left and right.

Even though we have `while` loops, every element is:

```text
pushed once
popped at most once
```

So the total work is `O(n)` amortized.

---

## 💻 C++ Code

```cpp
class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {
        const long long MOD = 1e9 + 7;

        int n = arr.size();

        vector<int> psee(n);
        vector<int> nse(n);

        vector<int> st;

        // Previous Smaller Element or Equal
        for (int i = 0; i < n; i++) {

            while (!st.empty() && arr[st.back()] > arr[i]) {
                st.pop_back();
            }

            psee[i] = st.empty() ? -1 : st.back();

            st.push_back(i);
        }

        st.clear();

        // Next Smaller Element
        for (int i = n - 1; i >= 0; i--) {

            while (!st.empty() && arr[st.back()] >= arr[i]) {
                st.pop_back();
            }

            nse[i] = st.empty() ? n : st.back();

            st.push_back(i);
        }

        long long ans = 0;

        // Calculate contribution of every element
        for (int i = 0; i < n; i++) {

            long long left = i - psee[i];
            long long right = nse[i] - i;

            long long contribution =
                left * right * arr[i];

            ans = (ans + contribution) % MOD;
        }

        return ans;
    }
};
```

---

## 🔑 Pattern to Remember

**Step 1:** Find `PSEE`

```text
pop >
```

**Step 2:** Find `NSE`

```text
pop >=
```

**Step 3:** Count choices

```text
left  = i - PSEE[i]
right = NSE[i] - i
```

**Step 4:** Calculate contribution

```text
left × right × arr[i]
```

**Step 5:** Add all contributions

```text
answer = Σ contribution
```

---

## 🧠 Memory Trick

```text
PSEE → Previous → Left → pop >
NSE  → Next     → Right → pop >=
```

Or simply remember:

```text
PSEE → >
NSE  → >=
```

---

### ⏱ Complexity

* **Time:** `O(n)` amortized
* **Space:** `O(n)`

---

## 📌 Final Formula

```text
Contribution of arr[i]

= (i - PSEE[i])
  × (NSE[i] - i)
  × arr[i]
```

```text
Answer = Sum of all contributions
```
