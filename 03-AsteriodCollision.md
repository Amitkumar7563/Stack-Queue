# Asteroid Collision — Stack

🔗 [LeetCode: Asteroid Collision](https://leetcode.com/problems/asteroid-collision/)

## 💡 Core Idea

Use a `vector` as a **stack**.

```cpp
vector<int> st;
```

* `push_back()` → push
* `back()` → top
* `pop_back()` → pop

### When can collision happen?

Only when:

```text
stack top  > 0
current    < 0
```

Example:

```text
5   →     ← -3
```

They move towards each other → **collision**.

But:

```text
-5   →     → 3
```

No collision.

---

## 🔄 Collision Logic

For every asteroid `x`:

### 1. Positive asteroid

Simply push:

```cpp
if(nums[i] > 0)
    st.push_back(nums[i]);
```

---

### 2. Negative asteroid

Keep removing smaller positive asteroids:

```cpp
while(!st.empty() &&
      st.back() > 0 &&
      st.back() < abs(nums[i]))
{
    st.pop_back();
}
```

Example:

```text
st = [5, 3]
current = -7

5? → collision → 3 is removed
3? → collision → 3 is removed

then -7 survives

st = [-7]
```

---

## ⚔️ Three Possible Cases

After the `while` loop:

### Case 1: Same size → both explode

```cpp
if(!st.empty() && st.back() == abs(nums[i]))
    st.pop_back();
```

Example:

```text
5  →   ← -5

Both disappear.
```

---

### Case 2: Stack empty OR top is negative

Current negative asteroid survives:

```cpp
else if(st.empty() || st.back() < 0)
    st.push_back(nums[i]);
```

Example:

```text
[-5, -3]  +  2

No collision → push 2
```

For current `-5`:

```text
[-3] + (-5)

Both move left → no collision
→ push -5
```

---

# 🧠 Easy Mental Model

For every **negative asteroid**:

```text
Is stack top positive?
        ↓
      YES
        ↓
Compare sizes
   ↙          ↘
top smaller   top equal
   ↓             ↓
pop top       pop top
   ↓
repeat

If no positive asteroid remains
        ↓
negative asteroid survives → push
```

---

## ✅ Code

```cpp
class Solution {
public:
    vector<int> asteroidCollision(vector<int>& nums) {

        vector<int> st;

        for(int x : nums) {

            if(x > 0) {
                st.push_back(x);
            }
            else {

                while(!st.empty() &&
                      st.back() > 0 &&
                      st.back() < abs(x)) {
                    st.pop_back();
                }

                if(!st.empty() && st.back() == abs(x)) {
                    st.pop_back();
                }
                else if(st.empty() || st.back() < 0) {
                    st.push_back(x);
                }
            }
        }

        return st;
    }
};
```

## 🔑 Pattern to Remember

**Positive → push directly**

**Negative → check previous positive asteroids**

```text
negative + positive
       ↓
    collision
       ↓
remove smaller
       ↓
equal → both remove
       ↓
no positive left → push negative
```

### ⏱ Complexity

* **Time:** `O(n)` amortized
* **Space:** `O(n)`

Although there is a `while` loop inside the `for`, each asteroid is pushed and popped at most once, so total work is `O(n)`.
