# 3. Longest Substring Without Repeating Characters

### Description

Given a string, find the length of the longest substring without repeating characters.

Example 1:

Input: "abcabcbb" 

Output: 3 

Explanation: The answer is "abc", with the length of 3.

 Example 2:

Input: "bbbbb" 

Output: 1 

Explanation: The answer is "b", with the length of 1.

 Example 3:

Input: "pwwkew"

Output: 3 

Explanation: The answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

link:  [https://leetcode-cn.com/problems/add-two-numbers/](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

### Solution

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int result = 0;
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0, j = 0; j < s.length(); j++) {
            if (map.containsKey(s.charAt(j))) {
                i = Math.max(map.get(s.charAt(j)), i);
            }
            result = Math.max(result, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        return result;
    }
}
```

