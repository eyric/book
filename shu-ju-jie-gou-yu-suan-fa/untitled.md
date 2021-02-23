# Leetcode解题笔记（1-20）

### 001两数之和

#### 题目描述

> 给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。
>
> 你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
>
> **示例:**
>
> ```text
> 给定 nums = [2, 7, 11, 15], target = 9
>
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]
> ```

#### 解题思路

```java
 public int[] twoSum(int[] nums, int target) {
        //边界条件
        if(nums == null || nums.length == 0){
            return new int[]{-1,-1};
        }
        //使用map记录 target - nums[i] 的位置
        Map<Integer,Integer> map = new HashMap<>();
        for(int i = 0;i<nums.length;i++){
            int num = target-nums[i];
            if(map.containsKey(nums[i])){ //num + nums[i] = target
                return new int[]{map.get(nums[i]),i};
            }
            map.put(num,i); 
        }
        //未找到结果
        return new int[]{-1,-1}; 
    }
```

### 002两数相加

#### 题目描述

> 给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。
>
> 如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。
>
> 您可以假设除了数字 0 之外，这两个数都不会以 0 开头。
>
> **示例：**
>
> ```text
> 输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
> 输出：7 -> 0 -> 8
> 原因：342 + 465 = 807
> ```

#### 解题思路

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        //记录相加的结果
        ListNode result = new ListNode(-1);
        ListNode p = result;
        //记录进位结果
        int next = 0;
        while(l1 != null && l2 != null){
            int sum = l1.val + l2.val + next;
            int val = sum%10; //当前位
            next = sum/10;    //进位
            p.next = new ListNode(val);
            l1 = l1.next;
            l2 = l2.next;
            p = p.next;
        }

        while(l1 != null){
           int sum = l1.val  + next;
            int val = sum%10;
            next = sum/10;
            p.next = new ListNode(val);
            l1 = l1.next;
            p = p.next;
        }

        while(l2 != null){
           int sum = l2.val  + next;
            int val = sum%10;
            next = sum/10;
            p.next = new ListNode(val);
            l2 = l2.next;
            p = p.next;
        }

        if(next != 0){
            p.next = new ListNode(next);
        }

        return result.next;
    }
```

### 003无重复字符的最长子串

#### 题目描述

> 给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。
>
> **示例 1:**
>
> ```text
> 输入: "abcabcbb"
> 输出: 3 
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
> ```
>
> **示例 2:**
>
> ```text
> 输入: "bbbbb"
> 输出: 1
> 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
> ```
>
> **示例 3:**
>
> ```text
> 输入: "pwwkew"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
>      请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
> ```

#### 解题思路

```java
//使用滑动窗口
    public int lengthOfLongestSubstring(String s) {
        //边界条件
        if(s == null || s.length() == 0){
            return 0;
        }

        //最长字符串长度，前一次出现该字符的位置
        int max = 0, start = 0;
        //记录出现过的字符的最后位置
        Map<Character,Integer> map = new HashMap<>();
        for(int i = 0; i<s.length(); i++){
            char c = s.charAt(i);
            if(map.containsKey(c)){ //出现重复字符
                start = Math.max(map.get(c),start) ;
            }

            max = Math.max(max,i-start+1);
            map.put(c,i + 1);
        }
        return max;
    }
```

### 004寻找两个有序数组的中位数

#### 题目描述

> 给定两个大小为 m 和 n 的有序数组 `nums1` 和 `nums2`。
>
> 请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O\(log\(m + n\)\)。
>
> 你可以假设 `nums1` 和 `nums2` 不会同时为空。
>
> **示例 1:**
>
> ```text
> nums1 = [1, 3]
> nums2 = [2]
>
> 则中位数是 2.0
> ```
>
> **示例 2:**
>
> ```text
> nums1 = [1, 2]
> nums2 = [3, 4]
>
> 则中位数是 (2 + 3)/2 = 2.5
> ```

#### 解题思路

```java
 public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int len = nums1.length + nums2.length;
        //前n/2的最大值
        int max = 0,i = 0,j = 0;
        while(i < nums1.length && j < nums2.length && (i+j+1) <= len / 2){
            if(nums1[i] <= nums2[j]){
                max = nums1[i++];
            }else{
                max = nums2[j++];
            }
        }

        while(i+j+1 <= len / 2){
            if(i < nums1.length){
                max = nums1[i++];
            }

            if(j < nums2.length){
                max = nums2[j++];
            }
        }

        int min ; //后n/2的最小值
        if(i >= nums1.length){
            min = nums2[j];
        }else if(j>= nums2.length){
            min = nums1[i];
        }else{
            min = Math.min(nums1[i],nums2[j]);
        }

        if(len % 2 == 0){ //偶数
            return  (max+min)/2.0;
        }else{ //奇数
            return min;
        }

    }
```

### 005最长回文子串

#### 题目描述

> 给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。
>
> **示例 1：**
>
> ```text
> 输入: "babad"
> 输出: "bab"
> 注意: "aba" 也是一个有效答案。
> ```
>
> **示例 2：**
>
> ```text
> 输入: "cbbd"
> 输出: "bb"
> ```

#### 解题思路

```java
 //动态规划解法
    public String longestPalindrome(String s) {
        //边界条件
        if(s == null || s.length()<2){
            return s;
        }

        //字符串长度,最长回文串起始位置，终止位置
        int n = s.length(), st = 0, end = 0;
        boolean[][] dp = new boolean[n][n];

        for(int i = 0;i<n;i++){
            dp[i][i] = true;
            for(int j = 0;j<i;j++){
                if(i - j == 1){ 
                    dp[j][i] = s.charAt(i) == s.charAt(j);
                }else{
                    dp[j][i] = dp[j+1][i-1] && s.charAt(i) == s.charAt(j);
                }

                if(dp[j][i] && i - j +1 > end - st){
                    st = j;
                    end = i;
                }
            }
        }

        return s.substring(st,end+1);
    }
```

### 006Z字形变换

#### 题目描述

> 将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。
>
> 比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：
>
> ```text
> L   C   I   R
> E T O E S I I G
> E   D   H   N
> ```
>
> 之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"`。
>
> 请你实现这个将字符串进行指定行数变换的函数：
>
> ```text
> string convert(string s, int numRows);
> ```
>
> **示例 1:**
>
> ```text
> 输入: s = "LEETCODEISHIRING", numRows = 3
> 输出: "LCIRETOESIIGEDHN"
> ```
>
> **示例 2:**
>
> ```text
> 输入: s = "LEETCODEISHIRING", numRows = 4
> 输出: "LDREOEIIECIHNTSG"
> 解释:
>
> L     D     R
> E   O E   I I
> E C   I H   N
> T     S     G
> ```

#### 解题思路

```java
public static String convert(String s, int numRows) {
        if (s == null || s.length() <= numRows || numRows == 1) {
            return s;
        }

        List<StringBuilder> list = new ArrayList<>(numRows);
        for (int i = 0; i < numRows; i++) {
            list.add(new StringBuilder());
        }

        boolean flag = true; //向下
        int currentRow = 0;
        for (char c : s.toCharArray()) {
            list.get(currentRow).append(c);
            currentRow = flag ? ++currentRow : --currentRow;

            if (currentRow == numRows - 1 || currentRow == 0) { //转向
                flag = !flag;
            }
        }

        return String.join("", list);
    }
```

### 007整数反转

#### 题目描述

> 给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。
>
> **示例 1:**
>
> ```text
> 输入: 123
> 输出: 321
> ```
>
> **示例 2:**
>
> ```text
> 输入: -123
> 输出: -321
> ```
>
> **示例 3:**
>
> ```text
> 输入: 120
> 输出: 21
> ```
>
> **注意:**
>
> 假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 \[−231, 231 − 1\]。请根据这个假设，如果反转后整数溢出那么就返回 0。

#### 解题思路

```java
public static int reverse2(int x) {
        boolean flag = x < 0; //负数标识
        StringBuilder sb = new StringBuilder(String.valueOf(abs(x))).reverse();

        long value = Long.valueOf(sb.toString());
        return value > Integer.MAX_VALUE ? 0 : (flag ? -(int) value : (int) value);
    }

    public static long abs(long x) {
        return x < 0 ? -x : x;
    }
```

### 008字符串转换整数 \(atoi\)

#### 题目描述

> 请你来实现一个 `atoi` 函数，使其能将字符串转换成整数。
>
> 首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。
>
> 当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。
>
> 该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。
>
> 注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。
>
> 在任何情况下，若函数不能进行有效的转换时，请返回 0。
>
> **说明：**
>
> 假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 \[−231, 231 − 1\]。如果数值超过这个范围，qing返回 INT\_MAX \(231 − 1\) 或 INT\_MIN \(−231\) 。
>
> **示例 1:**
>
> ```text
> 输入: "42"
> 输出: 42
> ```
>
> **示例 2:**
>
> ```text
> 输入: "   -42"
> 输出: -42
> 解释: 第一个非空白字符为 '-', 它是一个负号。
>      我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
> ```
>
> **示例 3:**
>
> ```text
> 输入: "4193 with words"
> 输出: 4193
> 解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
> ```
>
> **示例 4:**
>
> ```text
> 输入: "words and 987"
> 输出: 0
> 解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
>      因此无法执行有效的转换。
> ```
>
> **示例 5:**
>
> ```text
> 输入: "-91283472332"
> 输出: -2147483648
> 解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
>      因此返回 INT_MIN (−231) 。
> ```

#### 解题思路

```java
 public static int myAtoi(String str) {
        //边界条件
        if (str == null || str.equals("") || (str = str.trim()).equals(""))
            return 0;

        char[] ca = str.toCharArray();
        int i = 0;
        // 判断是否为正
        boolean isPos = ca[0] == '+' || ca[0] != '-';
        int res = 0;
        // 判断第一位是否为符号
        i = (ca[0] == '+' || ca[0] == '-') ? 1 : 0;
        while (i < ca.length && Character.isDigit(ca[i])) {

            int right = ca[i] - '0';
            //加进来之前考虑是否会溢出
            if (res > (Integer.MAX_VALUE - right) / 10) {
                return isPos ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            res = res * 10 + right;
            i++;
        }
        return isPos ? res : -res;
    }
```

### 009回文数

#### 题目描述

> 判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。
>
> **示例 1:**
>
> ```text
> 输入: 121
> 输出: true
> ```
>
> **示例 2:**
>
> ```text
> 输入: -121
> 输出: false
> 解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
> ```
>
> **示例 3:**
>
> ```text
> 输入: 10
> 输出: false
> 解释: 从右向左读, 为 01 。因此它不是一个回文数。
> ```

#### 解题思路

```java
public boolean isPalindrome(int x) {
        if(x < 0 || (x % 10 == 0 && x != 0)){
            return false;
        }

        int revertedNumber = 0;
        while(x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + x % 10;
            x /= 10;
        }

        return x == revertedNumber || x == revertedNumber/10;
    }
```

### 010正则表达式匹配

#### 题目描述

> 给定一个字符串 \(`s`\) 和一个字符模式 \(`p`\)。实现支持 `'.'` 和 `'*'` 的正则表达式匹配。
>
> ```text
> '.' 匹配任意单个字符。
> '*' 匹配零个或多个前面的元素。
> ```
>
> 匹配应该覆盖**整个**字符串 \(`s`\) ，而不是部分字符串。
>
> **说明:**
>
> * `s` 可能为空，且只包含从 `a-z` 的小写字母。
> * `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `.` 和 `*`。
>
> **示例 1:**
>
> ```text
> 输入:
> s = "aa"
> p = "a"
> 输出: false
> 解释: "a" 无法匹配 "aa" 整个字符串。
> ```
>
> **示例 2:**
>
> ```text
> 输入:
> s = "aa"
> p = "a*"
> 输出: true
> 解释: '*' 代表可匹配零个或多个前面的元素, 即可以匹配 'a' 。因此, 重复 'a' 一次, 字符串可变为 "aa"。
> ```
>
> **示例 3:**
>
> ```text
> 输入:
> s = "ab"
> p = ".*"
> 输出: true
> 解释: ".*" 表示可匹配零个或多个('*')任意字符('.')。
> ```
>
> **示例 4:**
>
> ```text
> 输入:
> s = "aab"
> p = "c*a*b"
> 输出: true
> 解释: 'c' 可以不被重复, 'a' 可以被重复一次。因此可以匹配字符串 "aab"。
> ```
>
> **示例 5:**
>
> ```text
> 输入:
> s = "mississippi"
> p = "mis*is*p*."
> 输出: false
> ```

#### 解题思路

```java
 //动态规划解法
public boolean isMatch(String s, String p) {
        int sLen = s.length(), pLen = p.length();
        //目标串的前i个元素和模式串的前j个元素是否匹配
        boolean[][] memory = new boolean[sLen + 1][pLen + 1];
        memory[0][0] = true;
        for (int i = 0; i <= sLen; i++) {
            for (int j = 1; j <= pLen; j++) {
                if (p.charAt(j - 1) == '*') {
                    memory[i][j] = memory[i][j - 2] || (i > 0 && (s.charAt(i - 1) == p.charAt(j - 2) ||
                            p.charAt(j - 2) == '.') && memory[i - 1][j]);
                } else {
                    memory[i][j] = i > 0 && (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '.')
                            && memory[i - 1][j - 1];
                }
            }
        }
        return memory[sLen][pLen];
    }
```

### 011盛最多水的容器

#### 题目描述

> 给定 _n_ 个非负整数 _a_1，_a_2，...，_a_n，每个数代表坐标中的一个点 \(_i_, _ai_\) 。在坐标内画 _n_ 条垂直线，垂直线 _i_ 的两个端点分别为 \(_i_, _ai_\) 和 \(_i_, 0\)。找出其中的两条线，使得它们与 _x_ 轴共同构成的容器可以容纳最多的水。
>
> **说明：**你不能倾斜容器，且 _n_ 的值至少为 2。
>
> ![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)
>
> 图中垂直线代表输入数组 \[1,8,6,2,5,4,8,3,7\]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
>
> **示例:**
>
> ```text
> 输入: [1,8,6,2,5,4,8,3,7]
> 输出: 49
> ```

#### 解题思路

```java
public int maxArea(int[] height) {
        //边界条件
        if(height == null || height.length < 2){
            return 0;
        }

        //双指针
        int i = 0,j = height.length-1;
        int max = 0; //最大面积

        while(i<j){
            int area = Math.min(height[i],height[j])*(j-i);
            if(area > max){
                max = area;
            }

            if(height[i]<height[j]){
                i++;
            }else{
                j--;
            }
        }
        return max;
    }
```

### 012整数转罗马数字

#### 题目描述

> 罗马数字包含以下七种字符： `I`， `V`， `X`， `L`，`C`，`D` 和 `M`。
>
> ```text
> 字符          数值
> I             1
> V             5
> X             10
> L             50
> C             100
> D             500
> M             1000
> ```
>
> 例如， 罗马数字 2 写做 `II` ，即为两个并列的 1。12 写做 `XII` ，即为 `X` + `II` 。 27 写做 `XXVII`, 即为 `XX` + `V` + `II` 。
>
> 通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 `IIII`，而是 `IV`。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 `IX`。这个特殊的规则只适用于以下六种情况：
>
> * `I` 可以放在 `V` \(5\) 和 `X` \(10\) 的左边，来表示 4 和 9。
> * `X` 可以放在 `L` \(50\) 和 `C` \(100\) 的左边，来表示 40 和 90。 
> * `C` 可以放在 `D` \(500\) 和 `M` \(1000\) 的左边，来表示 400 和 900。
>
> 给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内。
>
> **示例 1:**
>
> ```text
> 输入: 3
> 输出: "III"
> ```
>
> **示例 2:**
>
> ```text
> 输入: 4
> 输出: "IV"
> ```
>
> **示例 3:**
>
> ```text
> 输入: 9
> 输出: "IX"
> ```
>
> **示例 4:**
>
> ```text
> 输入: 58
> 输出: "LVIII"
> 解释: L = 50, V = 5, III = 3.
> ```
>
> **示例 5:**
>
> ```text
> 输入: 1994
> 输出: "MCMXCIV"
> 解释: M = 1000, CM = 900, XC = 90, IV = 4.
> ```

#### 解题思路

```java
 public static String intToRoman(int num) {

        int values[] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String reps[] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        StringBuilder res = new StringBuilder();
        for (int i = 0; i < 13; i++) {
            while (num >= values[i]) {
                num -= values[i];
                res.append(reps[i]);
            }
        }
        return res.toString();
    }
```

### 013罗马数字转整数

#### 题目描述

> 罗马数字包含以下七种字符: `I`， `V`， `X`， `L`，`C`，`D` 和 `M`。
>
> ```text
> 字符          数值
> I             1
> V             5
> X             10
> L             50
> C             100
> D             500
> M             1000
> ```
>
> 例如， 罗马数字 2 写做 `II` ，即为两个并列的 1。12 写做 `XII` ，即为 `X` + `II` 。 27 写做 `XXVII`, 即为 `XX` + `V` + `II` 。
>
> 通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 `IIII`，而是 `IV`。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 `IX`。这个特殊的规则只适用于以下六种情况：
>
> * `I` 可以放在 `V` \(5\) 和 `X` \(10\) 的左边，来表示 4 和 9。
> * `X` 可以放在 `L` \(50\) 和 `C` \(100\) 的左边，来表示 40 和 90。 
> * `C` 可以放在 `D` \(500\) 和 `M` \(1000\) 的左边，来表示 400 和 900。
>
> 给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。
>
> **示例 1:**
>
> ```text
> 输入: "III"
> 输出: 3
> ```
>
> **示例 2:**
>
> ```text
> 输入: "IV"
> 输出: 4
> ```
>
> **示例 3:**
>
> ```text
> 输入: "IX"
> 输出: 9
> ```
>
> **示例 4:**
>
> ```text
> 输入: "LVIII"
> 输出: 58
> 解释: L = 50, V= 5, III = 3.
> ```
>
> **示例 5:**
>
> ```text
> 输入: "MCMXCIV"
> 输出: 1994
> 解释: M = 1000, CM = 900, XC = 90, IV = 4.
> ```

#### 解题思路

```java
public static int romanToInt(String s) {
        List<String> keys = Arrays.asList("I", "V", "X", "L", "C", "D", "M");
        int[] values = {1, 5, 10, 50, 100, 500, 1000};

        int num = 0;
        for (int i = 0; i < s.length(); i++) {
            int idx = keys.indexOf(s.substring(i, i + 1));
            if (i < s.length() - 1 && values[idx] <
                    values[keys.indexOf(s.substring(i + 1, i + 2))]) {
                num -= values[idx];
            } else {
                num += values[idx];
            }
        }

        return num;
    }
```

### 014 最长公共前缀

#### 题目描述

> 编写一个函数来查找字符串数组中的最长公共前缀。
>
> 如果不存在公共前缀，返回空字符串 `""`。
>
> **示例 1:**
>
> ```text
> 输入: ["flower","flow","flight"]
> 输出: "fl"
> ```
>
> **示例 2:**
>
> ```text
> 输入: ["dog","racecar","car"]
> 输出: ""
> 解释: 输入不存在公共前缀。
> ```
>
> **说明:**
>
> 所有输入只包含小写字母 `a-z` 。

#### 解题思路

```java
public String longestCommonPrefix(String[] strs) {
        //边界条件
        if(strs == null || strs.length == 0){
            return "";
        }

        //以第一个字符串作为参照，纵向扫描
        //也可以横向扫描，找出第一个和第二个字符串的公共前缀
        //再找这个前缀与第三个字符串的公共前缀
        String first = strs[0];
        for(int i = 0;i<first.length();i++){
            boolean flag = false;
            for(String str : strs){
                if(i>= str.length() || str.charAt(i) != first.charAt(i)){
                    flag = true;
                    break;
                }
            }

            if(flag){
                return first.substring(0,i);
            }
        }

        return first;
    }
```

### 015 三数之和

#### 题目描述

> 给定一个包含 _n_ 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 _a，b，c ，_使得 _a + b + c =_ 0 ？找出所有满足条件且不重复的三元组。
>
> **注意：**答案中不可以包含重复的三元组。
>
> ```text
> 例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
>
> 满足要求的三元组集合为：
> [
>   [-1, 0, 1],
>   [-1, -1, 2]
> ]
> ```

#### 解题思路

```java
public List<List<Integer>> threeSum(int[] nums) {

        List<List<Integer>> listList = new ArrayList<>();
        // 如果为null或者长度小于3则返回空
        if(nums == null || nums.length < 3)
            return listList;
        // 对数组进行排序
        Arrays.sort(nums);

        // 排序后，如果最小值大于0或者最大值小于0则不可能三个数加起来等于0,
        if(nums[0] > 0 || nums[nums.length - 1] < 0)
            return listList;

        // 遍历数组
        for(int i = 0; i < nums.length - 2; i++) {

            // 当不是第一个元素的时候，如果该元素和前面的元素相等则continue；删除重复
            if(i != 0 && nums[i] == nums[i - 1])
                continue;

            // 左右指针
            int l = i + 1;
            int r = nums.length - 1;

            // 左指针小于右指针，而且当前值必须小于1，否则三个数都大于等于1.
            while(l < r && nums[i] < 1) {
                int s = nums[i] + nums[l] + nums[r];
                if(s == 0) { // 满足条件

                    // 结果添加到列表，左右指针相向移动
                    listList.add(Arrays.asList(nums[i], nums[l], nums[r]));
                    l++;
                    r--;
                    // 检查左右指针是否重复，重复则移动
                    while(l < r && nums[l] == nums[l - 1])
                        l++;
                    while(l < r && nums[r] == nums[r + 1])
                        r--;
                } else if(s > 0) {
                    r--;
                } else {
                    l++;
                }
            }
        }

        return listList;
    }
```

### 016 最接近的三数之和

#### 题目描述

> 给定一个包括 _n_ 个整数的数组 `nums` 和 一个目标值 `target`。找出 `nums` 中的三个整数，使得它们的和与 `target` 最接近。返回这三个数的和。假定每组输入只存在唯一答案。
>
> ```text
> 例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.
>
> 与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).
> ```

#### 解题思路

```java
public static int threeSumClosest(int[] nums, int target) {
        // 排序
        Arrays.sort(nums);
        int closestNum = nums[0] + nums[1] + nums[2];
        for (int i = 0; i < nums.length - 2; i++) {
            int l = i + 1, r = nums.length - 1;
            while (l < r) {
                int threeSum = nums[l] + nums[r] + nums[i];
                if (Math.abs(threeSum - target) < Math.abs(closestNum - target)) {
                    closestNum = threeSum;
                }
                if (threeSum > target) {
                    r--;
                } else if (threeSum < target) {
                    l++;
                } else {
                    // 如果已经等于target的话, 肯定是最接近的
                    return target;
                }

            }

        }

        return closestNum;
    }
```

### 017 电话号码的字母组合

#### 题目描述

> 给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。
>
> 给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。
>
> ![img](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)
>
> **示例:**
>
> ```text
> 输入："23"
> 输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
> ```
>
> **说明:** 尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

#### 解题思路

```java
static String[][] letters = {{"a", "b", "c"}, {"d", "e", "f"}, {"g", "h", "i"},
            {"j", "k", "l"}, {"m", "n", "o"}, {"p", "q", "r", "s"},
            {"t", "u", "v"}, {"w", "x", "y", "z"}
    };

    public static List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        if (digits == null || digits.length() == 0) {
            return result;
        }
        char[] chars = digits.toCharArray();
        for (char c : chars) {
            int idx = c - 48;
            String[] str = letters[idx - 2];
            if (result.size() > 0) {
                int size = result.size();
                for (int i = 0; i < size; i++) {
                    String s = result.get(i);
                    result.set(i, s + str[0]);
                    for (int j = 1; j < str.length; j++) {
                        result.add(size , s + str[j]);
                    }
                }
            } else {
                result.addAll(Arrays.asList(str));
            }

        }
        Collections.sort(result);
        return result;
    }
```

### 018 四数之和

#### 题目描述

> 给定一个包含 _n_ 个整数的数组 `nums` 和一个目标值 `target`，判断 `nums` 中是否存在四个元素 _a，\*\*b，c_ 和 _d_ ，使得 _a_ + _b_ + _c_ + _d_ 的值与 `target` 相等？找出所有满足条件且不重复的四元组。
>
> **注意：**
>
> 答案中不可以包含重复的四元组。
>
> **示例：**
>
> ```text
> 给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。
>
> 满足要求的四元组集合为：
> [
>   [-1,  0, 0, 1],
>   [-2, -1, 1, 2],
>   [-2,  0, 0, 2]
> ]
> ```

#### 解题思路

```java
public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 4) {
            return res;
        }
        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i++) {
            // 对第一重元素去重处理
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            for (int j = i + 1; j < nums.length; j++) {
                // 对第二重元素去重
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }
                int p = j + 1;
                int q = nums.length - 1;
                while (p < q) {
                    int sum = nums[i] + nums[j] + nums[p] + nums[q];
                    if (sum == target) {
                        List<Integer> list = new ArrayList<>();
                        list.add(nums[i]);
                        list.add(nums[j]);
                        list.add(nums[p]);
                        list.add(nums[q]);
                        res.add(list);
                        // 对第三重元素去重操作
                        while (p < q && nums[p] == nums[p + 1]) {
                            p++;
                        }
                        while (p < q && nums[q] == nums[q - 1]) {
                            q--;
                        }
                        p++;
                        q--;
                    } else if (sum < target) {
                        p++;
                    } else {
                        q--;
                    }
                }
            }
        }
        return res;
    }
```

### 019 删除链表的倒数第n个节点

#### 题目描述

> 给定一个链表，删除链表的倒数第 _n_ 个节点，并且返回链表的头结点。
>
> **示例：**
>
> ```text
> 给定一个链表: 1->2->3->4->5, 和 n = 2.
>
> 当删除了倒数第二个节点后，链表变为 1->2->3->5.
> ```
>
> **说明：**
>
> 给定的 _n_ 保证是有效的。
>
> **进阶：**
>
> 你能尝试使用一趟扫描实现吗？

#### 解题思路

```java
public static ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode p = head;
        ListNode st = new ListNode(-1);
        st.next = head;

        ListNode q = st;
        int i = 0, j = 0;
        while (p.next != null) {
            j++;
            if (j - i == n) {
                i++;
                q = q.next;
            }
            p = p.next;
        }


        q.next = q.next.next;


        return st.next;
    }
```

### 020 有效的括号

#### 题目描述

> 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。
>
> 有效字符串需满足：
>
> 1. 左括号必须用相同类型的右括号闭合。
> 2. 左括号必须以正确的顺序闭合。
>
> 注意空字符串可被认为是有效字符串。
>
> **示例 1:**
>
> ```text
> 输入: "()"
> 输出: true
> ```
>
> **示例 2:**
>
> ```text
> 输入: "()[]{}"
> 输出: true
> ```
>
> **示例 3:**
>
> ```text
> 输入: "(]"
> 输出: false
> ```
>
> **示例 4:**
>
> ```text
> 输入: "([)]"
> 输出: false
> ```
>
> **示例 5:**
>
> ```text
> 输入: "{[]}"
> 输出: true
> ```

#### 解题思路

```java
 static Map<Character,Character> map = new HashMap<>();
    static{
        map.put('(',')');
        map.put('{','}');
        map.put('[',']');
    }

    public boolean isValid(String s) {
        if(s == null || s.length() == 0){
            return true;
        }

        char[] arr = s.toCharArray();
        Stack<Character> stack = new Stack<>();

        for(char c : arr){
            if(c == '(' || c == '{' || c == '['){
                stack.push(c);
            }else{
                if(stack.isEmpty() || map.get(stack.pop()) != c)
                    return false;
            }
        }

        return stack.isEmpty();
    }
```

