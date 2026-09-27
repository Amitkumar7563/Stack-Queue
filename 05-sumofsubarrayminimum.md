# Sum of Subarray Minimums — Monotonic Stack

🔗 [LeetCode: Sum of Subarray Minimums](https://leetcode.com/problems/sum-of-subarray-minimums/)

---

## 💡 Core Idea

Hume har subarray ka minimum nikal ke add nahi karna hai.

Instead:

> **Har element ko dekho aur calculate karo ki kitne subarrays me ye element minimum ban sakta hai.**

Then:

```text
Contribution =
left choices × right choices × arr[i]
code
class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {

        const long long MOD = 1e9 + 7;

        int n = arr.size();

        vector<int> psee(n);
        vector<int> nse(n);

        vector<int> st;

        // -------------------------
        // Previous Smaller or Equal
        // -------------------------

        for(int i = 0; i < n; i++) {

            while(!st.empty() &&
                  arr[st.back()] > arr[i]) {

                st.pop_back();
            }

            psee[i] =
                st.empty() ? -1 : st.back();

            st.push_back(i);
        }

        // Clear stack
        st.clear();

        // -------------------------
        // Next Smaller Element
        // -------------------------

        for(int i = n - 1; i >= 0; i--) {

            while(!st.empty() &&
                  arr[st.back()] >= arr[i]) {

                st.pop_back();
            }

            nse[i] =
                st.empty() ? n : st.back();

            st.push_back(i);
        }

        // -------------------------
        // Contribution
        // -------------------------

        long long ans = 0;

        for(int i = 0; i < n; i++) {

            long long left =
                i - psee[i];

            long long right =
                nse[i] - i;

            long long contribution =
                left * right * arr[i];

            ans =
                (ans + contribution) % MOD;
        }

        return ans;
    }
};
