# Stack-Queue
Concept on stack and Queue and special problem...........
# 🥞 Stack — C++ STL Basics

> **Stack follows LIFO:** Last In → First Out

```cpp
#include <stack>
using namespace std;

stack<int> st;
```

---

## 1. `push()` — Add Element

Adds an element to the top.

```cpp
st.push(10);
st.push(20);
st.push(30);
```

```text
TOP → 30
      20
      10
```

---

## 2. `pop()` — Remove Top Element

Removes the top element.

```cpp
st.pop();
```

After removing:

```text
TOP → 20
      10
```

⚠️ `pop()` does **not return** the removed element.

❌ Wrong:

```cpp
int x = st.pop();
```

✅ Correct:

```cpp
int x = st.top();
st.pop();
```

---

## 3. `top()` — Access Top Element

Returns the top element **without removing it**.

```cpp
cout << st.top();
```

Example:

```cpp
st.push(10);
st.push(20);

cout << st.top();   // 20
```

---

## 4. `empty()` — Check Whether Stack Is Empty

Returns:

* `true` → stack is empty
* `false` → stack contains elements

```cpp
if (st.empty()) {
    cout << "Empty";
}
```

### Most common usage

```cpp
while (!st.empty()) {
    cout << st.top() << " ";
    st.pop();
}
```

---

## 5. `size()` — Number of Elements

```cpp
cout << st.size();
```

Example:

```cpp
st.push(10);
st.push(20);
st.push(30);

cout << st.size();   // 3
```

---

# ⭐ Important Functions to Remember

| Function  | Meaning        | Example       |
| --------- | -------------- | ------------- |
| `push(x)` | Add `x`        | `st.push(10)` |
| `pop()`   | Remove top     | `st.pop()`    |
| `top()`   | See top        | `st.top()`    |
| `empty()` | Check empty    | `st.empty()`  |
| `size()`  | Count elements | `st.size()`   |

### Quick Memory Trick

```text
push  → ADD
pop   → REMOVE
top   → SEE
empty → CHECK
size  → COUNT
```

---

# 🔥 Most Important Stack Pattern

A very common pattern in stack problems:

```cpp
while (!st.empty() && condition) {
    st.pop();
}
```

Then:

```cpp
if (!st.empty()) {
    // use st.top()
}
```

Finally:

```cpp
st.push(x);
```

This pattern is extremely common in **Next Greater Element / Monotonic Stack** problems.

---

# 🧠 LIFO Concept

Stack = **Last In, First Out**

```text
push(10)
push(20)
push(30)

        ┌────┐
TOP →   │ 30 │ ← First Out
        ├────┤
        │ 20 │
        ├────┤
        │ 10 │
        └────┘
```

If we call:

```cpp
st.pop();
```

`30` will be removed first.

---

# ⚠️ Important Things

### Never call `top()` or `pop()` on an empty stack

❌

```cpp
stack<int> st;

cout << st.top();   // Wrong
st.pop();           // Wrong
```

✅

```cpp
if (!st.empty()) {
    cout << st.top();
    st.pop();
}
```

---

# 🚀 Example: Process Every Element

```cpp
stack<int> st;

st.push(10);
st.push(20);
st.push(30);

while (!st.empty()) {
    cout << st.top() << " ";
    st.pop();
}
```

Output:

```text
30 20 10
```

Because stack follows **LIFO**.

---

# 📌 Complexity

| Operation | Time |
| --------- | ---: |
| `push()`  | O(1) |
| `pop()`   | O(1) |
| `top()`   | O(1) |
| `empty()` | O(1) |
| `size()`  | O(1) |

Space:

```text
O(n)
```

where `n` = number of elements stored in the stack.

---

# 🎯 DSA Checklist

Before solving stack problems, remember:

```text
☑ push()
☑ pop()
☑ top()
☑ empty()
☑ size()
☑ LIFO
☑ while (!st.empty())
☑ while (!st.empty() && condition)
```

> **Core idea:** Stack problems usually involve keeping some elements temporarily and removing them when they are no longer useful.
