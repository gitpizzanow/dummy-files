# Example: Knapsack DP Step by Step

## Objects:

| # | Weight | Gain |
| - | ------ | ---- |
| 1 | 1      | 10   |
| 2 | 2      | 15   |

Capacity K = 3

---

## Step 1: Initialize DP table

We create DP with n+1 rows (objects) and K+1 columns (capacity):

```python
DP = [[0]*(3+1) for _ in range(2+1)]
```

So table initially looks like:

```
         Capacity →
       0   1   2   3
Item↓ ----------------
0 |   0   0   0   0
1 |   0   0   0   0
2 |   0   0   0   0
```

* Row 0 → no items → gain = 0
* Column 0 → capacity 0 → gain = 0

---

## Step 2: Fill row 1 (object 1, weight=1, gain=10)

* w=1 → weight ≤ capacity → `max(10 + DP[0][0], DP[0][1]) = max(10,0) = 10`
* w=2 → `max(10 + DP[0][1], DP[0][2]) = max(10+0,0) = 10`
* w=3 → `max(10 + DP[0][2], DP[0][3]) = max(10+0,0) = 10`

Table after row 1:

```
       0   1   2   3
Item↓ ----------------
0 |   0   0   0   0
1 |   0  10  10  10
2 |   0   0   0   0
```

---

## Step 3: Fill row 2 (object 2, weight=2, gain=15)

* w=1 → 2 > 1 → cannot take → `DP[2][1] = DP[1][1] = 10`
* w=2 → 2 ≤ 2 → `max(15 + DP[1][0], DP[1][2]) = max(15+0,10) = 15`
* w=3 → 2 ≤ 3 → `max(15 + DP[1][1], DP[1][3]) = max(15+10,10) = 25`

Final DP table:

```
       0   1   2   3
Item↓ ----------------
0 |   0   0   0   0
1 |   0  10  10  10
2 |   0  10  15  25
```

---

## Step 4: Result

* Best gain = `DP[2][3] = 25`  ✅
* Achieved by taking **both objects** (weight=1+2=3, gain=10+15=25)

---

## Step 5: How the formula works

For `w=3` and object 2:

```
objects[i-1].gain + DP[i-1][w - objects[i-1].poids]
= 15 + DP[1][3-2]
= 15 + DP[1][1]
= 15 + 10
= 25
```

* `w - objects[i-1].poids = 3-2 = 1` → remaining capacity
* `DP[i-1][1] = 10` → best gain using previous objects with remaining capacity 1
* Add current gain = 15 → total = 25 ✅
