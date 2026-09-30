# Remove K Digits — Monotonic Stack & Greedy

## Problem

Given a string `num` representing a non-negative integer and an integer `k`, remove exactly `k` digits from `num` so that the resulting number is the **smallest possible**.

### Example

```text
Input:
num = "1432219"
k = 3

Output:
"1219"
```

---

## Core Idea 💡

We want the **smallest possible number**.

For two neighboring digits:

```text
... a b ...
```

If:

```text
a > b
```

then keeping `a` before `b` makes the number unnecessarily larger.

So, if we still have digits available to remove, we should remove `a`.

### Example

```text
4 3
```

Since:

```text
4 > 3
```

remove `4`.

```text
3
```

This greedy observation is the main idea of the problem.

---

# Why Greedy Works

In a number, **earlier digits have greater importance** than later digits.

For example:

```text
523...
↑
```

The `5` affects the number much more than digits appearing later.

Therefore, whenever we find:

```text
previous digit > current digit
```

removing the previous digit gives us a smaller number.

So we process the number from **left to right** and remove larger previous digits whenever possible.

---

# Why Do We Need a Stack?

Suppose:

```text
num = "4321"
k = 2
```

Process from left to right.

### Step 1

```text
4
```

Stack:

```text
[4]
```

### Step 2

Current digit = `3`

```text
4 > 3
```

Remove `4`.

```text
[3]
```

`k = 1`

### Step 3

Current digit = `2`

Again:

```text
3 > 2
```

Remove `3`.

```text
[2]
```

`k = 0`

### Step 4

Add `1`.

```text
[2, 1]
```

Answer:

```text
21
```

The stack allows us to easily access the **most recently added digit**, which is exactly the digit we may need to remove.

---

# Important Pattern

This creates an **increasing / non-decreasing stack**.

We try to maintain:

```text
stack[0] <= stack[1] <= stack[2] ...
```

Whenever this property breaks:

```text
stack.top() > current
```

we remove the top element.

This is a very important **Monotonic Stack pattern**.

---

# Why `while`, Not `if`?

This is one of the most important observations.

Consider:

```text
num = "4321"
k = 2
```

When `3` comes:

```text
4 > 3
```

Remove `4`.

Then when `2` comes:

```text
3 > 2
```

Remove `3`.

A single current digit can cause **multiple previous digits** to be removed.

Therefore we need:

```cpp
while (...)
```

not:

```cpp
if (...)
```

---

# Algorithm

For every digit `ch` in `num`:

1. While:

   * stack is not empty
   * `k > 0`
   * top of stack is greater than current digit

   Remove the top digit.

2. Push current digit into the stack.

3. After processing all digits:

   * If `k > 0`, remove remaining digits from the **back**.
   * This happens when the number is already increasing.

4. Remove leading zeros.

5. If nothing remains, return `"0"`.

---

# Dry Run

## Example

```text
num = "1432219"
k = 3
```

### Start

```text
stack = []
k = 3
```

### Read `1`

```text
stack = [1]
```

### Read `4`

```text
1 < 4
```

No removal.

```text
stack = [1,4]
```

### Read `3`

Now:

```text
4 > 3
```

Remove `4`.

```text
stack = [1]
k = 2
```

Push `3`:

```text
stack = [1,3]
```

### Read `2`

```text
3 > 2
```

Remove `3`.

```text
stack = [1]
k = 1
```

Push `2`:

```text
stack = [1,2]
```

### Read next `2`

```text
2 <= 2
```

Push:

```text
stack = [1,2,2]
```

### Read `1`

```text
2 > 1
```

Remove one `2`.

```text
stack = [1,2]
k = 0
```

Since `k = 0`, no more removal.

Push `1`:

```text
stack = [1,2,1]
```

### Read `9`

```text
stack = [1,2,1,9]
```

Final:

```text
1219
```

---

# C++ Solution

```cpp
class Solution {
public:
    string removeKdigits(string num, int k) {

        string st;

        for(char ch : num) {

            while(!st.empty() && k > 0 && st.back() > ch) {
                st.pop_back();
                k--;
            }

            st.push_back(ch);
        }

        // If digits are already increasing
        // remove remaining digits from the end
        while(k > 0) {
            st.pop_back();
            k--;
        }

        // Remove leading zeros
        int i = 0;

        while(i < st.size() && st[i] == '0') {
            i++;
        }

        string ans = st.substr(i);

        return ans.empty() ? "0" : ans;
    }
};
```

---

# Understanding the Important Code

### 1. Stack

```cpp
string st;
```

We use a `string` as a stack.

Why?

Because digits are characters and we only need:

```cpp
st.back();      // top
st.push_back(); // push
st.pop_back();  // pop
```

So a separate `stack<char>` is not necessary.

---

### 2. Main Greedy Condition

```cpp
while(!st.empty() && k > 0 && st.back() > ch)
```

This means:

> "If I still have deletions available and the previous digit is larger than the current digit, remove the previous digit."

This is the heart of the solution.

---

### 3. Push Current Digit

```cpp
st.push_back(ch);
```

After removing all unnecessary larger previous digits, add the current digit.

---

### 4. Remaining `k`

Suppose:

```text
num = "123456"
k = 2
```

There is no:

```text
previous > current
```

because the number is already increasing.

So the greedy loop cannot remove anything.

We still have to remove exactly `k` digits.

The best option is to remove digits from the **end**:

```text
123456
   ↓
1234
```

Therefore:

```cpp
while(k > 0) {
    st.pop_back();
    k--;
}
```

---

# Leading Zeros

Example:

```text
num = "10200"
k = 1
```

After removing `1`:

```text
0200
```

But the answer should be:

```text
200
```

So remove leading zeros.

```cpp
while(i < st.size() && st[i] == '0') {
    i++;
}
```

---

# Edge Case: Everything Becomes Zero

Example:

```text
num = "10"
k = 2
```

Everything is removed.

So:

```cpp
ans.empty() ? "0" : ans
```

returns:

```text
"0"
```

---

# Universal Monotonic Stack Template 🔥

This problem teaches an important reusable pattern:

```cpp
for(auto x : nums) {

    while(!st.empty() && condition(st.back(), x)) {
        st.pop_back();
    }

    st.push_back(x);
}
```

The **condition** changes depending on the problem.

For Remove K Digits:

```cpp
st.back() > x
```

Meaning:

```text
Remove previous larger element
```

This is related to problems such as:

* Next Greater Element
* Previous Smaller Element
* Daily Temperatures
* Stock Span
* Sum of Subarray Minimums
* Remove K Digits

---

# The Main Mental Model 🧠

Don't memorize:

```cpp
while(!st.empty() && k > 0 && st.back() > ch)
```

Instead remember:

> **I want the smallest number. Therefore, whenever a larger digit appears before a smaller digit, I should remove that larger digit if I still have a deletion available.**

Then ask:

```text
How do I efficiently remove the previous digit?
        ↓
Stack
        ↓
What if several previous digits are larger?
        ↓
while loop
```

So the solution naturally becomes:

```text
Greedy observation
       ↓
Remove previous larger digit
       ↓
Need access to previous digit
       ↓
Stack
       ↓
Several removals possible
       ↓
while loop
```

---

# Complexity

Let `n = num.length()`.

### Time Complexity

```text
O(n)
```

Although there is a `while` loop, each digit is pushed once and popped at most once.

Therefore total operations are linear.

### Space Complexity

```text
O(n)
```

for the stack.

---

# Quick Revision Notes

```text
Problem: Remove K Digits

Goal:
Make the smallest possible number after removing k digits.

Pattern:
Greedy + Monotonic Increasing Stack

Observation:
If previous digit > current digit,
remove previous digit.

Why?
Earlier digits have greater significance.

Main condition:
while(!st.empty() && k > 0 && st.back() > ch)

Why while?
One current digit can remove multiple previous larger digits.

If k remains:
Remove from the back.

Finally:
Remove leading zeros.

Empty result:
Return "0".

Complexity:
Time  = O(n)
Space = O(n)
```

## Most Important Takeaway

**Don't think "Remove K Digits".**

Think:

> **"I am building the smallest possible number from left to right. Whenever the current digit can make a previous larger digit unnecessary, remove that previous digit."**

That thinking is the real concept to carry forward to other **Greedy + Monotonic Stack** problems.
