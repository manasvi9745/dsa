# Longest Palindrome — Two Approaches (Very Detailed)

> **Goal:** Given a string `s` of lowercase/uppercase letters, return the **length** of the longest palindrome that can be built using those letters (letters are case‑sensitive: `A` ≠ `a`). You may reorder letters.

---

## Problem Statement

- **Input:** `s` (string)
- **Output:** Integer — maximum possible length of any palindrome formed from letters of `s`.
- **Note:** At most **one** character may have an odd frequency (it can sit in the **center** of the palindrome). All other used characters must appear an **even** number of times (to pair symmetrically left/right).

**Examples**

- `s = "abccccdd"` → `7` (e.g., `"dccaccd"`).
- `s = "a"` → `1`.
- `s = "Aa"` → `1` (case‑sensitive; `A` and `a` don’t match, so only one can be used in the center).

---

# Approach 1 — Count Frequencies, Add Even Parts, Add One Center (Your Approach)

### Intuition

A palindrome has mirrored pairs around an optional center:

- Any **even count** of a character is fully usable (e.g., 4 `c`s can form `cc` on the left and `cc` on the right).
- Any **odd count** contributes all but one to pairs (`count - 1` is even). From **all** odd-count characters, we may place **exactly one** leftover single in the center.

Therefore:

1. Sum **all even counts** fully.
2. For each **odd** count `x`, add `x - 1` (the paired part) to the total.
3. If there was **at least one** odd count, add **1** (one single letter can be the center).

### Step-by-step

1. Build a frequency map `mymap[ch]` for all characters.
2. Iterate the map:
   - If `freq` is **even** → add `freq` to `finallength`.
   - If `freq` is **odd** → add `freq - 1` to `finallength` and increment `oddoccurances`.
3. After the loop, if `oddoccurances > 0`, add **1** to `finallength`.
4. Return `finallength`.

### Code (C++)

```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        int finallength = 0;
        int oddoccurances = 0;
        unordered_map<char,int> mymap;

        // 1) Count frequencies
        for (char ch : s) {
            mymap[ch]++;
        }

        // 2) Sum pairs; count odds
        for (auto &p : mymap) {
            int cnt = p.second;
            if (cnt % 2 == 0) {
                finallength += cnt;      // fully usable
            } else {
                finallength += cnt - 1;  // use even part, drop one
                oddoccurances++;
            }
        }

        // 3) Add center if any odd exists
        if (oddoccurances > 0) finallength += 1;

        return finallength;
    }
};
```

### Dry Runs

**Example A — **``

- Frequencies: `A:3 (odd)`, `B:3 (odd)`
- From odds: add `(3-1) + (3-1) = 2 + 2 = 4` to `finallength`; `oddoccurances = 2`.
- Since `oddoccurances > 0`, add `1` for center → total `5`.
- One possible palindrome length = `5` (e.g., `"AABBA"` or `"BBAAB"`).

**Example B — **``

- Frequencies: `a:1 (odd)`, `b:1 (odd)`, `c:4 (even)`, `d:2 (even)`
- Add from even counts: `4 + 2 = 6`
- For odds `a` and `b`: add `(1-1) + (1-1) = 0` to `finallength`; `oddoccurances = 2`.
- `oddoccurances > 0` → add `1` → total `7`.

**Example C — **``

- Frequencies: `a:1`, `b:1`, `c:1` (all odd)
- Pairs contribute `0 + 0 + 0 = 0`; `oddoccurances = 3`.
- Add center `+1` → total `1`.

### Complexity

- **Time:** `O(n)` to count + iterate distinct characters (total linear in input size).
- **Space:** `O(k)` where `k` is number of distinct characters (at most 52 for `a–z` & `A–Z`; or up to 128/256 if you use an ASCII map).

---

# Approach 2 — Single-Pass Toggle Using `oddCount` (More Compact)

This is the version that does *counting and odd/even tracking in a single pass*.

### Intuition

- Maintain an `oddCount` that represents **how many distinct characters currently have an odd frequency** while scanning the string left→right.
- For each character `ch`:
  - Increment its frequency (`++cnt[ch]`).
  - If the **new** frequency becomes **odd**, do `oddCount++` (this char now contributes to the odd bucket).
  - If the **new** frequency becomes **even**, do `oddCount--` (we just formed a new pair for this char, so it leaves the odd bucket).
- After the single pass, `oddCount` equals the **final number of odd-frequency characters**.
- **Only one** odd-frequency character can be used as the **center**; every other odd forces us to drop **one** character to make it even. Thus:

$$
\text{answer} = |s| - \max(\text{oddCount} - 1,\ 0)
$$

This matches the pairing logic from Approach 1, just computed more directly.

**Key Learnings that resolve typical doubts:**

- We **do not** throw away an entire letter type when it’s odd; we only **drop a single extra** from each odd (except one). The even portion is always preserved as pairs.
- The `oddCount++/--` is just parity tracking: each time a character’s count flips between odd/even, we move it in/out of the “odd bucket”.
- If `oddCount = 0`, all counts are even → we can use **all** characters.
- If `oddCount = 1`, exactly one odd exists → we can use **all** characters (that odd is the center).
- If `oddCount > 1`, we must drop `oddCount - 1` characters total (one from each extra odd) → that’s why we subtract `oddCount - 1` from `|s|`.

### Code (C++)

```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        unordered_map<char,int> cnt; // or an array[128] for ASCII
        int oddCount = 0;

        for (char ch : s) {
            int after = ++cnt[ch];
            if (after % 2 == 1) {
                oddCount++;   // ch became odd
            } else {
                oddCount--;   // ch became even — we formed another pair
            }
        }

        // If more than one odd remains, we must drop (oddCount - 1) chars.
        // Otherwise, we can use all characters.
        int n = (int)s.size();
        return (oddCount > 1) ? (n - oddCount + 1) : n;
        // Equivalent form: n - max(oddCount - 1, 0)
    }
};
```

### Dry Runs (Parity Toggle View)

**Example A — **``

We track `(cnt['A'], cnt['B'])` and `oddCount` after each character:

| Step | ch | cnt[A] | cnt[B] | Became odd/even? | oddCount |
| ---- | -- | ------ | ------ | ---------------- | -------- |
| 1    | A  | 1      | 0      | A odd            | 1        |
| 2    | A  | 2      | 0      | A even           | 0        |
| 3    | A  | 3      | 0      | A odd            | 1        |
| 4    | B  | 3      | 1      | B odd            | 2        |
| 5    | B  | 3      | 2      | B even           | 1        |
| 6    | B  | 3      | 3      | B odd            | 2        |

Final: `oddCount = 2`, `n = 6` → `ans = 6 - 2 + 1 = 5`.

Interpretation: we keep `AA` and `BB` as pairs (even parts), and **one** of the leftover singles (`A` or `B`) as the center.

**Example B — **``

- Final counts: `a:1 (odd)`, `b:1 (odd)`, `c:4 (even)`, `d:2 (even)` → `oddCount = 2`, `n = 8`.
- `ans = 8 - 2 + 1 = 7`.

**Example C — **``

- Final counts: three odds → `oddCount = 3`, `n = 3` → `ans = 3 - 3 + 1 = 1`.

### Complexity

- **Time:** `O(n)` — single scan over the string, O(1) updates.
- **Space:** `O(k)` for frequency storage (map or fixed array). Using a fixed-size array for ASCII (e.g., `int cnt[128]`) gives *practical* O(1) space.

---

## Equivalence of the Two Approaches

Another common formula you’ll see (equivalent to both approaches):

$$
\text{answer} = \sum_c 2\left\lfloor \frac{\text{freq}[c]}{2} \right\rfloor \; + \; \mathbf{1}_{\exists\,c:\ \text{freq}[c]\text{ is odd}}
$$

- Sum the **even parts** of all frequencies.
- If **any** odd exists, add `1` for the center.

This is exactly what Approach 1 computes explicitly, and what Approach 2 achieves via `oddCount` and the final subtraction formula.

---

## Edge Cases & Pitfalls

- **Case sensitivity:** `"Aa"` → `A` and `a` are different; result is `1`, not `2`.
- **All even counts:** use all letters (e.g., `"aabbcc"` → `6`).
- **All odd counts:** you can only place **one** in the center (e.g., `"abc"` → `1`).
- **Long strings:** prefer a fixed array (`int cnt[128]`) over `unordered_map` for speed if alphabet is known and small.
- **Order doesn’t matter:** we only need the **length**, not the actual palindrome.

---

## What I’d Tell the Interviewer (Keep this for the end)

- **Problem insight:** In a palindrome, all used characters except possibly one must contribute in **pairs**. Therefore, the result is “all even parts plus possibly one center”.
- **Approach 1 (clear & explicit):** Count frequencies, add `even` parts, add `odd-1` for odds, and finally `+1` if any odd exists. Clean and very readable. Complexity: `O(n)` time, `O(k)` space.
- **Approach 2 (compact & one pass):** While counting, maintain `oddCount` by toggling on each char: if a count becomes odd → `oddCount++`, if it becomes even → `oddCount--`. Final answer is `n - max(oddCount - 1, 0)`. Same asymptotics but slightly fewer passes and very elegant.
- **Key clarifications I learned/used:**
  - We **preserve pairs** and only drop **one** from each extra odd character (except one chosen for center).
  - `oddCount` reflects how many characters end **odd**; when `oddCount > 1`, we must drop `oddCount - 1` total.
  - Both formulas are equivalent; demonstrating both reassures correctness.
- **Possible optimizations:** Use a fixed-size array for ASCII to reduce hashmap overhead; early exit not needed since we need counts anyway.
- **Testing:** Checked typical cases — all even, all odd, mixed, case sensitivity.

---

### Quick Test Cases

- `""` → `0`
- `"a"` → `1`
- `"Aa"` → `1`
- `"aa"` → `2`
- `"aab"` → `3`
- `"abccccdd"` → `7`
- `"AAABBB"` → `5`

---

**Summary:** Both approaches are correct and linear. Approach 1 is explicit and beginner-friendly; Approach 2 is compact and interview‑friendly because it communicates a solid understanding of parity with a neat single‑pass toggle.

