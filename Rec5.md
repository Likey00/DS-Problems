# Binary Search
Given a **sorted** (in ascending order) integer array `nums` of `n` elements and a `target` value, write a function to search `target` in nums. If `target` exists, then return its index, otherwise return `-1`.

**Example Input:** `[-1,0,3,5,9,12]`, `target = 9`

**Example Output:** `4`

Leetcode: https://leetcode.com/problems/binary-search/

`Solutions will be released on 2/26`

# Decision Tree
Draw the decision tree for binary search for an array of 8 elements. Include failure nodes, and mark comparisons on the nodes and branches.

- Which index has the least number of comparisons for a successful search? How many comparisons?
- Which index or indexes have the maximum number of comparisons for a successful search? How many comparisons?
- What is the least number of comparisons for unsuccessful search? How many comparisons?
- What is the maximum number of comparisons for unsuccessful search? How many comparisons?
- What is the average number of comparisons for successful searches?
- What is the average number of comparisons for unsuccessful search if we don't know how many values are being searched (give a range, we can't know for sure).
- What is the average number of comparisons for unsuccessful search. Assume the search values are 5 numbers consisting of:
    - 3 numbers that are smaller than the value at index 0.
    - 2 numbers that are greater than the value at index 3 but is smaller than the value at index 5.

`Solutions will be released on 2/26`

# 3Sum (Hard)
Given a (not necessarily sorted) array `nums` without duplicates, return an ArrayList containing 3 elements of `nums` such that the values sum to 0. If there are no such elements, simply return null.

**Example Input:** `[-1,0,1,2,-4]`

**Example Output:** `[-1,0,4]`

NOTE: Using a binary search approach, this problem can be solved in O(n^2 logn) which is the intended solution. If you know about hash tables, there is an O(n^2) solution, but feel free to revisit this problem later otherwise. I recommend solving the problem with the binary search at least once for practice.

`Solutions will be released on 2/26`