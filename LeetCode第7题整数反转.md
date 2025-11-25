LeetCode第7题整数反转，给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。假设环境不允许存储 64 位整数（有符号或无符号）。借鉴下列这段代码class Solution {
    public int myAtoi(String s) {
        char[] c = s.trim().toCharArray();
        if (c.length == 0) return 0;
        int res = 0, bndry = Integer.MAX_VALUE / 10;
        int i = 1, sign = 1;
        if (c[0] == '-') sign = -1;
        else if (c[0] != '+') i = 0;
        for (int j = i; j < c.length; j++) {
            if (c[j] < '0' || c[j] > '9') break;
            if (res > bndry || res == bndry && c[j] > '7') return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            res = res * 10 + (c[j] - '0');
        }
        return sign * res;
    }
}
给出Java代码实现。

基于你提供的 `myAtoi` 代码逻辑，我们可以在不使用 64 位整数（long）的情况下，利用 `Integer.MAX_VALUE / 10` 作为边界（`bndry`）来预判是否会发生溢出。

以下是借鉴该逻辑实现的 LeetCode 第 7 题 Java 代码：

```java
class Solution {
    public int reverse(int x) {
        int res = 0;
        // 借鉴参考代码：设定边界值，用于预判溢出
        // Integer.MAX_VALUE / 10 = 214748364
        int bndry = Integer.MAX_VALUE / 10;
        // Integer.MIN_VALUE / 10 = -214748364
        int minBndry = Integer.MIN_VALUE / 10;

        while (x != 0) {
            // 取出 x 的最后一位数字（Java中负数取模保留负号，例如 -123 % 10 = -3）
            int pop = x % 10;
            // 去掉 x 的最后一位
            x /= 10;

            // 1. 正数溢出判断（借鉴 myAtoi 的逻辑）
            // 如果 res 已经大于 214748364，再乘以 10 肯定溢出
            // 如果 res 等于 214748364，且新加的位 pop > 7 (MAX_VALUE 尾数为 7)，则溢出
            if (res > bndry || (res == bndry && pop > 7)) {
                return 0;
            }

            // 2. 负数溢出判断（同理）
            // 如果 res 已经小于 -214748364，再乘以 10 肯定溢出
            // 如果 res 等于 -214748364，且新加的位 pop < -8 (MIN_VALUE 尾数为 8)，则溢出
            if (res < minBndry || (res == minBndry && pop < -8)) {
                return 0;
            }

            // 推入结果
            res = res * 10 + pop;
        }
        
        return res;
    }
}
```

### 代码原理解析

1.  **环境限制应对**：题目要求不能存储 64 位整数，因此不能简单的用 `long` 存储结果然后转回 `int` 判断。我们需要在 `res * 10 + pop` 这个操作**发生之前**进行判断。
2.  **核心逻辑借鉴**：
    *   参考代码中使用了 `bndry = Integer.MAX_VALUE / 10`。
    *   在本题中，我们同样使用这个边界。因为整数最大值为 `2147483647`，最小值为 `-2147483648`。
    *   **正向溢出**：当 `res` 大于 `214748364` 时，下一步 `res * 10` 必然超过最大值；如果 `res` 等于 `214748364`，只要下一位数字 `pop` 大于 `7`，也会溢出。
    *   **负向溢出**：同理，当 `res` 小于 `-214748364`，或等于 `-214748364` 且 `pop` 小于 `-8` 时，会发生下溢。
3.  **取模技巧**：在 Java 中，`x % 10` 会保留符号。例如 `-123 % 10` 得到 `-3`，这使得我们可以用一套逻辑处理正负数累加，只需要在边界判断时区分正负即可。