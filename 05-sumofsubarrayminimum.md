. Problem

Har possible subarray ka minimum find karo aur sabhi minimums ka sum return karo.

Example:

arr = [3, 1, 2, 4]

Subarrays:

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

Answer:

3 + 1 + 2 + 4 + 1 + 1 + 2 + 1 + 1 + 1
= 17

LeetCode constraints n <= 3 × 10^4 hain, isliye O(n²) approach practical nahi hai. 

2. Brute Force

Simple idea:

for(int i = 0; i < n; i++) {

    int mini = INT_MAX;

    for(int j = i; j < n; j++) {

        mini = min(mini, arr[j]);

        ans += mini;
    }
}
Complexity
Time  = O(N²)
Space = O(1)

Problem: N = 30000 tak ja sakta hai.

So humein O(N) solution chahiye.

3. 🔥 Main Observation

Brute force mein hum soch rahe the:

"Har subarray ka minimum kya hai?"

Optimal approach mein question reverse kar do:

"Har element kitne subarrays ka minimum ban raha hai?"

For example:

arr = [3, 1, 2, 4]

          1
        /   \
       3     2

1 bahut saare subarrays ka minimum hai.

Agar hum calculate kar lein ki 1 exactly kitne subarrays ka minimum hai, then:

contribution of 1
= 1 × number of subarrays

Similarly har element ka contribution calculate karenge.

4. Contribution Formula ⭐

For every arr[i], find:

PSE = Previous Smaller Element
NSE = Next Smaller Element

Suppose:

PSE[i] = previous smaller boundary
NSE[i] = next smaller/equal boundary

Then:

left choices  = i - PSE[i]
right choices = NSE[i] - i

Therefore:

Number of subarrays
= left choices × right choices

And:

Contribution
= arr[i] × left choices × right choices
Final formula
       arr[i]
          ×
(i - PSE[i])
          ×
(NSE[i] - i)
5. 🤔 left × right kyun?

Ye most important concept hai.

Consider:

arr = [3, 1, 2, 4]
          ↑
        arr[i]

For 1:

PSE = -1
NSE = 4

So:

left  = i - PSE
      = 1 - (-1)
      = 2

right = NSE - i
      = 4 - 1
      = 3
Left choices

1 se subarray ko left mein kitna extend kar sakte hain?

[1]
[3,1]

2 choices.

Right choices

Right mein:

[1]
[1,2]
[1,2,4]

3 choices.

Therefore:

Total subarrays
= 2 × 3
= 6

And 1 ka contribution:

1 × 2 × 3 = 6

Actually ye 6 subarrays hain:

[1]
[3,1]

[1,2]
[3,1,2]

[1,2,4]
[3,1,2,4]

In sab mein 1 minimum hai.

6. PSE kaise find karna hai?
Previous Smaller Element

Har arr[i] ke left mein nearest strictly smaller element chahiye.

Example:

arr = [3, 1, 2, 4]

PSE:

index:   0   1   2   3
arr:     3   1   2   4
PSE:    -1  -1   1   2

3 ke left mein kuch nahi:

PSE[0] = -1

1 ke left mein smaller kuch nahi:

PSE[1] = -1

2 ke left mein nearest smaller = 1:

PSE[2] = 1

4 ke left mein nearest smaller = 2:

PSE[3] = 2
PSE Monotonic Stack

Left → Right traverse:

for(int i = 0; i < n; i++) {

    while(!st.empty() && arr[st.top()] >= arr[i])
        st.pop();

    if(!st.empty())
        pse[i] = st.top();
    else
        pse[i] = -1;

    st.push(i);
}
Important
arr[st.top()] >= arr[i]

Why >=?

Because PSE ko strictly smaller chahiye.

So equal element ko bhi remove karna hai.

7. NSE kaise find karna hai?
Next Smaller or Equal Element

Ab right side mein nearest:

smaller OR equal

element chahiye.

Traverse:

Right → Left
for(int i = n - 1; i >= 0; i--) {

    while(!st.empty() && arr[st.top()] > arr[i])
        st.pop();

    if(!st.empty())
        nse[i] = st.top();
    else
        nse[i] = n;

    st.push(i);
}
Important difference 🚨

PSE:

>=

NSE:

>

This is intentional.

8. ⭐ Why >= on one side and > on other?

Ye duplicates ki wajah se hai.

Suppose:

arr = [2, 2]

Dono elements same minimum ho sakte hain.

Agar dono sides par same comparison use kar diya, to same subarray ko multiple times count kar sakte hain.

Isliye hum ownership decide karte hain:

Left side  → strictly smaller
Right side → smaller OR equal

That means:

PSE → >= pop
NSE → > pop
Memory trick 🧠
PSE = Strictly Smaller
     → pop >=

NSE = Smaller or Equal
     → pop >

One side strict + one side non-strict = duplicate handling.

9. Complete Dry Run

Take:

arr = [3,1,2,4]
PSE
index     0   1   2   3
arr       3   1   2   4
PSE      -1  -1   1   2
NSE
index     0   1   2   3
arr       3   1   2   4
NSE       1   4   4   4

Now calculate contribution.

i	arr[i]	PSE	NSE	Left	Right	Contribution
0	3	-1	1	1	1	3
1	1	-1	4	2	3	6
2	2	1	4	1	2	4
3	4	2	4	1	1	4

Therefore:

Answer = 3 + 6 + 4 + 4
       = 17

✅ Correct.

10. Complete Code
class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {

        int n = arr.size();
        const long long MOD = 1e9 + 7;

        vector<int> pse(n), nse(n);
        stack<int> st;

        // Previous Smaller Element
        for(int i = 0; i < n; i++) {

            while(!st.empty() && arr[st.top()] >= arr[i])
                st.pop();

            pse[i] = st.empty() ? -1 : st.top();

            st.push(i);
        }

        while(!st.empty())
            st.pop();

        // Next Smaller or Equal Element
        for(int i = n - 1; i >= 0; i--) {

            while(!st.empty() && arr[st.top()] > arr[i])
                st.pop();

            nse[i] = st.empty() ? n : st.top();

            st.push(i);
        }

        long long ans = 0;

        // Contribution of every element
        for(int i = 0; i < n; i++) {

            long long left = i - pse[i];
            long long right = nse[i] - i;

            long long contribution =
                (arr[i] * left % MOD) * right % MOD;

            ans = (ans + contribution) % MOD;
        }

        return ans;
    }
};
11. Code ko yaad kaise rakhna hai?

Pura code ratne ki zarurat nahi.

Bas 3 steps yaad rakho:

1. PSE
      ↓
2. NSE
      ↓
3. Contribution
PSE
while(arr[st.top()] >= arr[i])
    pop
NSE
while(arr[st.top()] > arr[i])
    pop
Contribution
left  = i - pse[i]
right = nse[i] - i

ans += arr[i] * left * right
12. 🧠 Universal Pattern

Ye question sirf ek problem nahi hai. Isse ek important stack template nikalo:

"Contribution using nearest boundary"

Jab question ho:

Sum/count of something over all subarrays

and har subarray mein koi:

minimum
maximum
next/previous boundary

important ho...

then think:

Can I calculate contribution of each element?
        ↓
How many subarrays contain this element?
        ↓
How far can it extend?
        ↓
Find boundary using Monotonic Stack
        ↓
left choices × right choices
Minimum
Previous Smaller
+
Next Smaller
Maximum
Previous Greater
+
Next Greater
13. 🔥 Most Important Takeaway

Tumhare Next Greater Element questions mein tum soch rahe the:

"Is element ke baad greater element kahan hai?"

Yahan thought upgrade hai:

"Ye element kitne subarrays ka minimum ban sakta hai?"

Aur answer milta hai:

      PSE             NSE
       ↓               ↓
   ─────── arr[i] ───────
          ↓
    left × right
          ↓
   number of subarrays
          ↓
 arr[i] × left × right
          ↓
      contribution

Yehi actual concept hai jo tumhe yaad rakhna hai—not the code.

GitHub file name

Tumhare existing naming style ke according:

05-sum-of-subarray-minimums.md

Aur iske andar ek Revision Box zaroor rakho:

## ⚡ 30-Second Revision

Pattern: Contribution + Monotonic Stack

PSE → previous strictly smaller
NSE → next smaller or equal

PSE:
while(arr[st.top()] >= arr[i]) pop

NSE:
while(arr[st.top()] > arr[i]) pop

left  = i - PSE[i]
right = NSE[i] - i

contribution = arr[i] * left * right

Why left × right?
Every valid left boundary can pair with
every valid right boundary.

Why >= and >?
One side strict + one side non-strict
prevents duplicate counting.

TC = O(N)
SC = O(N)
