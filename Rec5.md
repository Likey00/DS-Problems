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
    - 2 numbers that are greater than the value at index 3 but is smaller than the value at index 4.

<details>
<summary>Click to reveal solution</summary>

![](https://i.imgur.com/7BW0u4z.jpeg)

1. The index with the least number of comparisons would be the one at the top of the search tree, or `index 3`. Referencing a diagram, it can be seen that a successful search for the element at index 3 involves only `1 comparison` (the one for equality).
   
2. The index with the greatest number of comparisons would be one of the indices at the very bottom of the search tree, or in this case only `index 7`. Referencing a diagram and tracing the search process for index 7, it can be seen that a successful search for the element at index 7 involves `7 comparisons` (make sure you include both equality checks and inequality checks).

3. The least number of comparisons for an unsuccessful search can be found by tracing a diagram to one of the shallowest failure nodes. Referencing a diagram and tracing the search process for one of the shallowest failure nodes (for instance the leftmost one in my diagram), it can be seen that an unsuccessful search for such an element involves `6 comparisons`. Note that there is no final equality comparison for failure nodes.

4. The greatest number of comparisons for an unsuccessful search can be found by tracing a diagram to one of the deepest failure nodes. Referencing a diagram and tracing the search process for one of the deepest failure nodes (for instance the rightmost one in my diagram), it can be seen that an unsuccessful search for such an element involves `8 comparisons`. Note that there is no final equality comparison for failure nodes.

5. When finding the average number of comparisons for successful search, it is reasonable to assume that all the elements have an equal probability of being searched. From a diagram, it can be seen that there is 1 element which takes 1 comparison to successfully search, 2 elements which take 3 comparisons, 4 elements which take 5 comparisons, and 1 element which takes 7 comparisons. Thus, the average is `(1*1 + 2*3 + 4*5 + 1*7)/2`, and this solution can be expressed as `34/8`, `17/4`, or `4.25`.

6. When finding the average number of comparisons for unsuccessful search without concrete data, we **cannot** do what we do for successful search, adding up the number of comparisons for failure nodes and dividing by the number of failure nodes. The failure nodes are not concrete values, and we **cannot** assume that every failure node is reached with equal probability. For example, the first failure node represents a value less than index 0 of the array, the second failure node represents a value between index 0 and 1 of the array, etc. Without additional information, we don't know how often an unsuccessful search falls in each of these nodes, since based on the input data the average case could be wildly different. **However**, we know for a fact whenever something fails it takes either 6 or 8 comparisons. Thus, all we can say is that we know the average falls `between 6 and 8 comparisons`. 

7. Now we are actually given where the searches fail, and how many searches fail in each location, so we can come up with an average case for failure for the given input data. The failure node corresponding to "smaller than the value at index 0" takes 6 comparisons to reach, and the failure node corresponding to "greater than index 3 and smaller than index 4" also takes 6 comparisons. Thus, we know the average must also be `6 comparisons`. Alternatively, we could compute `(3(6) + 2(6)) / 5 = 6`. 
</details>

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