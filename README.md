# LEETCODE-Array-1140
Let's dry-run the `stoneGameII` method from the provided `Solution` class to understand how it works. We will walk through the function step by step, covering the logic and flow.

### Problem Understanding

The problem is a variant of the stone game where Alice and Bob take turns picking stones from a sequence of piles. Alice wants to maximize the number of stones she can collect, and Bob plays optimally to minimize Alice's total. Given `piles`, the method `stoneGameII` calculates the maximum number of stones Alice can collect if she starts with a move limit of `M` and can pick up to `2 * M` piles in one turn.

### Approach and Dry-Run

1. **Initialization**

   - `piles` is the array of stone piles.
   - `suffix[i]` represents the sum of stones from index `i` to the end of the `piles` array.
   - `mem[i][M]` is used to store the result of the subproblem where `i` is the current index and `M` is the current move limit.

2. **Setup**

   ```java
   final int n = piles.length;
   int[][] mem = new int[n][n];
   int[] suffix = new int[n];
   Arrays.stream(mem).forEach(A -> Arrays.fill(A, -1));
   suffix[n - 1] = piles[n - 1];
   for (int i = n - 2; i >= 0; --i)
       suffix[i] = suffix[i + 1] + piles[i];
   ```

   - We initialize the `suffix` array to store cumulative sums from the end of the `piles` array.
   - We initialize `mem` with `-1` to indicate that no subproblem has been solved yet.

3. **Recursive Function: `stoneGameII(suffix, i, M, mem)`**

   This function calculates the maximum number of stones Alice can collect starting from index `i` with a move limit `M`.

   ```java
   if (i + 2 * M >= suffix.length)
       return suffix[i];
   if (mem[i][M] != -1)
       return mem[i][M];
   ```

   - **Base Case**: If Alice can take all remaining piles (i.e., `i + 2 * M >= suffix.length`), return the sum of all remaining stones.
   - **Memoization Check**: If this subproblem has already been solved (i.e., `mem[i][M] != -1`), return the stored result.

   ```java
   int opponent = suffix[i];
   for (int X = 1; X <= 2 * M; ++X)
       opponent = Math.min(opponent, stoneGameII(suffix, i + X, Math.max(M, X), mem));
   ```

   - **Compute Optimal Play**: For each possible move `X` (from 1 to `2 * M`), calculate the result if Alice picks `X` piles.
   - `opponent` keeps track of the minimum number of stones Bob can force Alice to collect, given the optimal moves by both players.

   ```java
   return mem[i][M] = suffix[i] - opponent;
   ```

   - **Store and Return Result**: Store the result in `mem[i][M]` and return it.

### Example Dry-Run

Consider `piles = [3, 9, 1, 2]`. Here's a step-by-step dry-run:

1. **Initialize `suffix` Array:**

   ```
   suffix[3] = 2
   suffix[2] = 1 + 2 = 3
   suffix[1] = 9 + 3 = 12
   suffix[0] = 3 + 12 = 15
   ```

   So `suffix` is `[15, 12, 3, 2]`.

2. **First Call: `stoneGameII(suffix, 0, 1, mem)`**

   - Here, `suffix[0] = 15`. Alice can pick 1 or 2 piles.

   **For `X = 1`:**

   - Alice picks 1 pile, and Bob faces `stoneGameII(suffix, 1, 1, mem)`.
   - **Call: `stoneGameII(suffix, 1, 1, mem)`**
     - Here, `suffix[1] = 12`. Alice can pick 1 or 2 piles.
     - **For `X = 1`:**
       - Alice picks 1 pile, and Bob faces `stoneGameII(suffix, 2, 1, mem)`.
       - **Call: `stoneGameII(suffix, 2, 1, mem)`**
         - Here, `suffix[2] = 3`. Alice can pick 1 or 2 piles.
         - **For `X = 1`:**
           - Alice picks 1 pile, and Bob faces `stoneGameII(suffix, 3, 1, mem)`.
           - **Call: `stoneGameII(suffix, 3, 1, mem)`**
             - Here, `suffix[3] = 2`. Alice can pick all remaining piles.
             - Returns `2`.
         - **For `X = 2`:**
           - Alice picks 2 piles, and Bob faces `stoneGameII(suffix, 4, 2, mem)`.
           - **Call: `stoneGameII(suffix, 4, 2, mem)`**
             - Beyond the array bounds, Alice picks all remaining piles.
             - Returns `0`.
         - Minimum Bob can force is `2`. Hence Alice gets `3 - 2 = 1`.
     - **For `X = 2`:**
       - Alice picks 2 piles, and Bob faces `stoneGameII(suffix, 3, 2, mem)`.
       - **Call: `stoneGameII(suffix, 3, 2, mem)`**
         - Same as before, Alice picks all remaining piles.
         - Returns `2`.
     - Minimum Bob can force is `2`. Hence Alice gets `12 - 2 = 10`.

   **For `X = 2`:**

   - Alice picks 2 piles, and Bob faces `stoneGameII(suffix, 2, 2, mem)`.
   - **Call: `stoneGameII(suffix, 2, 2, mem)`**
     - Same process as before, minimum Bob can force is `2`. Alice gets `3 - 2 = 1`.

   Overall, the best Alice can achieve is `15 - 10 = 5`.

### Conclusion

The `stoneGameII` function calculates the maximum stones Alice can collect using dynamic programming and memoization. By considering all possible moves and recursively calculating the results, the function efficiently determines the optimal strategy for Alice.
