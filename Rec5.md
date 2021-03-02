# Binary Search
Given a **sorted** (in ascending order) integer array `nums` of `n` elements and a `target` value, write a function to search `target` in nums. If `target` exists, then return its index, otherwise return `-1`.

**Example Input:** `[-1,0,3,5,9,12]`, `target = 9`

**Example Output:** `4`

Leetcode: https://leetcode.com/problems/binary-search/

NOTE: My solution will be written and tested in leetcode, and use the same method signature.

<details>
<summary>Click to reveal solution</summary>

## Solution
In order to conduct our binary search, we create 2 variables `lo` and `hi` which keep track of the current range of indices we are searching in. At each iteration, we compare the value at the midpoint of our current search range with the target value, and update our search range accordingly. This method of eliminating half your current search space at each step ONLY works because the array is sorted.

```java
public int search(int[] nums, int target) {
    //Set my current search range as the whole array
    int lo = 0, hi = nums.length-1;
    
    //Keep going until hi becomes less than lo
    //When they're equal, there's only one element to check
    while (lo <= hi) {
        //Get the middle of the range
        int m = (lo + hi) / 2;
        
        //If we've found the element, return index m
        if (nums[m] == target) return m;
        
        //If m is too big, update our search range to [lo,m-1]
        if (nums[m] > target) hi = m-1;
        else lo = m+1; //If m is too small, update to [m+1,hi]
    }
    
    return -1; //If we get here, element wasn't found
}
```
If n is the size of the input array, our runtime is O(logn), since we are halving the size of our current search space at every step. If our search space starts at n, the number of steps is the number of times we can divide n by 2 until we get 1, or O(logn). The space complexity is O(1), since we always create 3 new int variables: lo, hi, and m.
</details>

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
Given a (not necessarily sorted) array `nums` without duplicates, return an array containing 3 elements of `nums` such that the values sum to 0. If there are no such elements, simply return null.

**Example Input:** `[-1,0,1,2,-4]`

**Example Output:** `[-1,0,1]`

NOTE: Using a binary search approach, this problem can be solved in O(n^2 logn) which is the intended solution. If you know about hash tables, there is an O(n^2) solution, but feel free to revisit this problem later otherwise. I recommend solving the problem with the binary search at least once for practice.

<details>
<summary>Click to reveal solution</summary>

## Solution
The strategy to find 3 numbers that add up to 0 is to first sort the array so that you can use binary search. Then iterate through every combination of 2 numbers, and binary search the array for the last number you need. If the number you need is found and isn't one of the numbers in your current combination, you can simply return the solution. If it is in your current combination, you can break out of the binary search and keep looking.

```java
public static int[] threeSum(int[] nums) {
    Arrays.sort(nums);
    int n = nums.length;

    //Iterate through all combinations (i, j) in the array
    for (int i = 0; i < n; i++) {
        for (int j = i+1; j < n; j++) {

            //target + nums[i] + nums[j] = 0
            int target = 0 - nums[i] - nums[j];
            int lo = 0, hi = n-1;

            //Binary search for target
            while (lo <= hi) {
                int m = (lo + hi) / 2;

                if (nums[m] == target) {
                    //If we aren't using duplicate values, return solution
                    if (m != i && m != j) {
                        int[] solution = {nums[i], nums[j], target};
                        return solution;
                    }

                    //Otherwise, break out and try a different combination
                    else break;
                }

                if (nums[m] > target) hi = m-1;
                else lo = m+1;
            }
        }
    }

    //If we didn't find anything, return null
    return null; 
}
```
Since iterating through all the combinations (i, j) in the array is O(n^2), and for each of these combinations we're doing a binary search in O(logn), our total runtime is O(n^2 logn). Our space complexity is O(1) since we're not creating any new arrays of size dependent on n.
</details>