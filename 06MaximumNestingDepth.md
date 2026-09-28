# Maximum Nesting Depth of the Parentheses

🔗 **LeetCode:** [Maximum Nesting Depth of the Parentheses](https://leetcode.com/problems/maximum-nesting-depth-of-the-parentheses/)

---

## 🧠 Main Concept

The important thing to learn from this problem is **not just parentheses depth**.

It teaches two reusable patterns:

1. **Balance / Counter Pattern**
2. **Stack for Nested Structures**

The best solution uses a counter because we only need to know **how many opening brackets are currently active**.

---

# 1. Balance / Counter Pattern ⭐

### Basic idea

```text
'(' → +1
')' → -1
```

The current `balance` represents the current nesting depth.

```text
(       → 1
((      → 2
(()     → 1
(())    → 0
```

So:

```text
Maximum balance = Maximum nesting depth
```

### Reusable Template

```cpp
int balance = 0;
int answer = 0;

for (char ch : s) {

    if (ch == '(')
        balance++;
    else if (ch == ')')
        balance--;

    answer = max(answer, balance);
}

return answer;
```

---

# 2. General "Maximum Active" Pattern ⭐

The same idea works beyond parentheses.

Whenever something **starts → +1** and **ends → -1**:

```cpp
int active = 0;
int maximum = 0;

for (...) {

    if (start)
        active++;

    else if (end)
        active--;

    maximum = max(maximum, active);
}
```

### Can be used for

* Maximum nesting depth
* Maximum overlapping intervals
* Maximum people in a room
* Maximum active processes
* Maximum concurrent meetings
* Maximum open connections

### Mental Model

```text
START → active++
END   → active--

maximum active → answer
```

---

# 3. Stack Pattern

A stack can also solve the problem.

### Why?

Every opening bracket is stored:

```text
(
((
(((
```

When a closing bracket appears, remove the latest opening bracket:

```text
) → pop
```

### Code

```cpp
stack<char> st;
int result = 0;

for (char ch : s) {

    if (ch == '(')
        st.push(ch);

    else if (ch == ')')
        st.pop();

    result = max(result, (int)st.size());
}

return result;
```

### Complexity

```text
Time  : O(n)
Space : O(n)
```

---

# 4. Important Optimization: Stack → Counter ⭐⭐⭐

This is the most valuable observation.

Ask:

> **Do I actually need the elements inside the stack?**

Here, we don't.

We only need:

```cpp
st.size()
```

So instead of:

```cpp
stack<char> st;
```

we can maintain:

```cpp
int openBrackets = 0;
```

Then:

```text
push → ++openBrackets
pop  → --openBrackets
```

Therefore:

```text
Stack
  ↓
Only size matters
  ↓
Use counter
  ↓
O(n) space → O(1) space
```

### General Rule

> If you only need the **count/size** of a data structure, check whether you can maintain that information using a variable.

---

# 5. State Tracking Pattern

Another reusable concept is **maintaining a state while traversing**.

```cpp
int state = 0;
int answer = 0;

for (auto x : data) {

    // Update state
    // ...

    // Update answer
    answer = max(answer, state);
}
```

For this problem:

```text
State = current number of open brackets
```

```text
'(' → state++
')' → state--
```

Then:

```text
answer = maximum state
```

---

# 6. Prefix Balance

You can also think of the solution as a **prefix balance** problem.

For every position, calculate:

```text
balance = (# opening brackets) - (# closing brackets)
```

Example:

```text
String: ( ( ) ( ) )

Char       Balance
------------------
(             1
(             2  ← maximum
)             1
(             2  ← maximum
)             1
)             0
```

Therefore:

```text
Maximum prefix balance = answer
```

This connects to the broader **Prefix Sum / Prefix Balance** pattern.

---

# 7. How to Recognize This Pattern

When you see:

* opening / closing
* start / end
* enter / leave
* active / inactive
* overlapping events
* nested structures

Ask:

```text
Can I represent the current situation
using a counter/balance?
```

If yes:

```text
START → +1
END   → -1
```

Then check whether the problem asks for:

```text
Maximum → max(balance)
Minimum → min(balance)
Current → balance
Final   → final balance
```

---

# 8. When Should I Use Stack Instead?

Use a **counter** when you only need the number.

Use a **stack** when you need the actual elements.

### Counter

```text
Question:
"How many are currently active?"

→ Counter
```

### Stack

```text
Question:
"Which element was opened most recently?"
"Which bracket matches this?"
"Can I access the previous unmatched element?"

→ Stack
```

---

# 9. Final Reusable Templates

### 🔹 Balance Counter

```cpp
int balance = 0;

for (char ch : s) {

    if (open)
        balance++;

    else if (close)
        balance--;
}
```

### 🔹 Maximum Balance

```cpp
int balance = 0;
int answer = 0;

for (char ch : s) {

    if (open)
        balance++;

    else if (close)
        balance--;

    answer = max(answer, balance);
}
```

### 🔹 Stack for Nested Structure

```cpp
stack<T> st;

for (auto x : data) {

    if (opening_condition)
        st.push(x);

    else if (closing_condition)
        st.pop();
}
```

---

# ⭐ Key Takeaways

```text
1. Opening event  → +1
2. Closing event  → -1
3. Maximum balance → maximum active/nesting level
4. Need actual elements? → Stack
5. Need only count? → Counter
6. Stack size only? → Try replacing stack with counter
7. Track state while traversing → State Tracking Pattern
8. Balance over prefixes → Prefix Balance Pattern
```

### One-line revision

> **Nested/active problem → maintain balance. If only count matters, prefer a counter over a stack.**
