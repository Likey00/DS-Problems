# Classes and Methods
Before introducing any problems, I would like to share the various classes and methods which I use in my solutions, and also some methods which I use to test my code. Your implementations of these classes may be different, but sharing mine will allow you to understand my solutions completely.

## Node class
```java
public class Node {
    int data;
    Node next;

    //No argument constructor, empty node
    public Node() {
        data = 0;
        next = null;
    }

    //Lets you initialize Node with data and next already set 
    public Node(int d, Node n) {
        data = d;
        next = n;
    }
}
```
## Method to print out a list (helps for testing)
```java
public static void printList(Node head) {
    //Need a check for empty list to avoid NPE
    if (head == null) System.out.println("Empty list!");

    //Iterate through the list with a for loop, printing out each element
    System.out.print(head.data);
    for (Node ptr = head.next; ptr != null; ptr = ptr.next) {
        System.out.print("->" + ptr.data);
    }
    
    //I like to add a newline at the end
    System.out.println();
}
```
<br>

# Valid Parenthesis
Write a method isBalanced that takes a String `input` as an argument and determines whether its parenthesis are properly balanced. You should be using a stack to implement this. You may assume that the only possible characters are `[({})]`. For parenthesis to be balanced:

- Every opening paren must be closed by a closing paren of the same type
- Every opening paren inside a pair of parenthesis must be closed within that pair

**Example Input:** `[()]{}{[()()]()}`

**Example Output:** true

**Example Input:** `[(])`

**Example Output:** false

NOTE: I will be using the built in java Stack class for this solution

<details>
<summary>Click to reveal solution</summary>

# Solution
Iterate over the characters in the given string. When an open paren is encountered, push it to the stack. When a closing paren is encountered, pop the top element from the stack, and if the parenthesis don't match up, the parenthesis aren't balanced. If the stack is empty by the end of the string, return true. Otherwise, return false. The stack ensures that the most recently added open paren must be closed before anything else can be closed, and if it is empty at the end every open paren has been closed. 

```java
public static boolean isBalanced(String input) {
    char[] parens = input.toCharArray();
    Stack<Character> s = new Stack<Character>();
    
    //Iterate through every character in the input string
    for (char c : parens) {
        //If open paren, push to the stack
        if (c == '[' || c == '(' || c == '{') {
            s.push(c);
        }
        //If closing paren
        else {
            //If I can't pop an element, unbalanced
            if (s.isEmpty()) return false;

            //Pop the top off the stack
            char open = s.pop();

            //If the closing doesn't match the top of the stack, unbalanced
            if (open == '[' && c != ']') return false;
            if (open == '(' && c != ')') return false;
            if (open == '{' && c != '}') return false;
        }
    }

    //If we make it through the loop and stack is empty, return true
    return s.isEmpty();
}
```
Since pushing and popping from the stack is O(1) and we iterated through the input string once, the runtime of this method is O(n) where n is the number of characters in the string. Since I created a new stack which might potentially be filled with n elements, the space complexity is O(n).
</details>
<br>

# Max in a Linked List
Write a method max that takes a Node `head` as an argument and returns the value of the maximum key in the list. Assume that all keys are positive integers, and return 0 if the list is empty. As a challenge, try to write a recursive solution afterwards.

**Example Input:** `2->7->6->13->8->1`

**Example Output:** 13

<details>
<summary>Click to reveal solution</summary>

## Iterative Solution
Maintain a variable which stores the current max of all the elements you've already seen. As you iterate through each node in the linked list, update the current max variable if the current node is greater. At the end of the list, your variable will store the max of the whole list.

```java
public static int max(Node head) {
    int mx = 0; //I start at a value smaller than any possible element

    //Iterate through the list
    for (Node ptr = head; ptr != null; ptr = ptr.next) {
        //Update mx if ptr.data is larger
        mx = Math.max(mx, ptr.data);
    }
    
    return mx; //Note that if head is null, the for loop does nothing
}
```
If n is the length of the given linked list, the time complexity of this solution is O(n) since you visit each node once and apply one operation per node. The space complexity is O(1) since we only create an int and a pointer object. 
<br>

## Recursive Solution
The simplest possible case is an empty list, in which case we can just return 0, so this is our base case. Given a non empty list, we can assume we know solutions closer to the base case, since a recursive call to head.next will return the max of the rest of the list. If we know the max of the rest of the list, all we have to do is return the max of the current node and the rest of the list recursively.

```java
public static int max(Node head) {
    if (head == null) return 0;
    return Math.max(head.data, max(head.next));
}
```
If n is the length of the given linked list, the time complexity of this solution is O(n) since you visit each node once and apply one operation per node. The space complexity this time however is O(n) since each recursive call requires call stack space, and we make n recursive calls. If you want to understand recursion better through practice, I highly recommend `codingbat.com/java` and their recursion section. 
</details>
<br>

# Josephus Problem
N people are in dire straits and agree to the following strategy to reduce the population. They arrange themselves in a circle (at positions numbered from 0 to `N`–1) and proceed around the circle, eliminating every `M`th person remaining until only one person is left. Write a method josephus which takes an int `N` and an int `M` as arguments and prints the order in which people are eliminated. You should be using a queue to implement this.

**Example Input:** `N = 7`, `M = 2`

**Example Output:** `1 3 5 0 4 2 6`

NOTE: I will be using the built in java Queue class for this solution.

<details>
<summary>Click to reveal solution</summary>

## Solution
Fill a queue with the numbers from 0 to `N`-1. The queue will represent the people who are yet to be eliminated. Then, continue the following process until the queue is empty. Remove `M`-1 people from the queue, but immediately enqueue them again since they are not eliminated. Then remove the next person, print out their number, and do not enqueue them, since they are now eliminated. 

```java
public static void josephus(int N, int M) {
    Queue<Integer> q = new LinkedList<Integer>();

    for (int i = 0; i < N; i++) q.add(i);

    while (!q.isEmpty()) {
        for (int i = 0; i < M-1; i++) q.add(q.remove());

        System.out.print(q.remove() + " ");
    }
}
```
Since this solution starts with N people and does M dequeues for every one person eliminated, the runtime is O(NM). Since the maximum size of the queue is N, the space complexity is O(N).
</details>
<br>

# Task Scheduler (Hard)
You are given a character array tasks, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or be idle. However, there is a non-negative integer n that represents the cooldown period between two same tasks (the same letter in the array). That is, there must be at least n units of time between any two same tasks. Return the least number of units of time that the CPU will take to finish all the given tasks. You can write/test your solution at the following leetcode link: https://leetcode.com/problems/task-scheduler/

**Example Input:** `['A','A','A','B','B','B']`, `n = 2`

**Example Output:** 8

**Explanation:** The following task order is optimal: `A, B, Idle, A, B, Idle, A, B`. This is 8 units of time.

<details>
<summary>Click to reveal solution</summary>

## Solution
A good way to solve this problem involves thinking about wasted time. The time where the CPU is idle is considered wasted time, since no task is being executed. Since the same task requires n units of time before it can be executed again, we can begin by considering the most frequent letter alone, which will result in (max frequency - 1) * n idle units of time. You can think of groups of n units of time sandwiched between this letter. Now we can slot the rest of the tasks in, and reduce the wasted time in the process. If the element has the same frequency as the max frequency, it will remove (max frequency - 1) units of idle time, since the last instance will go at the end, which is not considered wasted time. Otherwise, the element will remove (its frequency) units of idle time, since each instance of the element will use up one unit of wasted time and be evenly spaced. At the end, we can just return the number of elements + wasted time.

```java
public static int taskScheduler(char[] tasks, int n) {
    //Store the frequencies of each character, and sort them
    int[] frequencies = new int[26];
    for (char c : tasks) frequencies[c-'A']++;
    Arrays.sort(frequencies);
    
    //Initially, only consider max frequency
    int maxFreq = frequencies[25];
    int wastedTime = (maxFreq - 1) * n;
    
    //Go through all the other frequencies
    for (int i = 0; i < 25; i++) {
        if (frequencies[i] == maxFreq) wastedTime -= (maxFreq - 1);
        else wastedTime -= frequencies[i];
    }
    
    //Use Math.max with 0 since negative wasted time is just 0 wasted time
    return tasks.length + Math.max(0, wastedTime);
}
```
If n is the size of the input array, this algorithm will have a runtime of O(n), since we loop through the whole input array once to calculate the frequencies. Even though we created a new array, our space complexity is O(1) since the new array will always be of size 26 regardless of our input size.
</details>
<br>

# Move To Front
Implement a method moveToFront which takes a Node `head` and int `target` as arguments and returns a Node pointing to the front of the modified list. If there is a node containing `target` in the list, move it to the head of the list. If no such node exists, you may simply create a new node containing `target` at the head of the list. 

**Example Input:** `1->2->3->4`, `target = 3`

**Example Output:** `3->1->2->4`

**Example Input:** `1->2->3->4`, `target = 17`

**Example Output:** `17->1->2->3->4`

<details>
<summary>Click to reveal solution</summary>

## Solution
A special case to consider is when the list is empty, in which case we can simply return a new node containing target. Otherwise, we want to iterate through the given list, and delete the node containing target if it exists. Finally, we can simply insert target at the front of the list and return it. Note that we need a pointer to the node before the node we want to delete.

```java
//This is one process you could use to delete a target node
public static Node moveToFront1(Node head, int target) {
    if (head == null) return new Node(target, null);

    //Note that we are focusing on ptr.next, so we go until ptr.next is null
    for (Node ptr = head; ptr.next != null; ptr = ptr.next) {
        //If ptr.next is the target, we can delete it using our ptr variable
        if (ptr.next.data == target) {
            ptr.next = ptr.next.next;
            break;
        }
    }
    
    //Note that we didn't check the head, but if it's target we don't need to change anything
    if (head.data == target) return head;
    
    //Otherwise we can create a new node with a reference to the rest of the list
    return new Node(target, head);
}

//This is another process you could use
public static Node moveToFront2(Node head, int target) {
    if (head == null) return new Node(target, null);
    if (head.data == target) return head;

    //We maintain a prev pointer which lags behind by 1 
    for (Node ptr = head.next, prev = head; ptr != null; prev = ptr, ptr = ptr.next) {
        if (ptr.data == target) {
            //We can use the prev pointer to help delete the current pointer
            prev.next = ptr.next;
            break;
        }
    }
    
    return new Node(target, head);
}
```
If the number of nodes in the list is n, the time complexity is O(n) since you are iterating through the list once. The space complexity is O(1) since you are only creating 2 or 3 new nodes.
</details>
<br>

# Reorder Singly Linked List (Hard)
Given a Node `head`, reorder the linked list from the order `1, 2, 3, 4...n` to the order `1, n, 2, n-1, 3, n-2...` Also, you should be shuffling the nodes, not overwriting the data. Please draw a picture to figure this one out.

**Example Input:** `1->2->3->4->5`

**Example Output:** `1->5->2->4->3`

<details>
<summary>Click to reveal solution</summary>

## Solution
There are a couple distinct steps that you can break this problem into. First, you want to split the given list into two, where an extra node would go into the first list. Next, you want to reverse the second list. Finally, you want to insert the elements of the second list in between every other element of the first list.

```java
public static void reorder(Node head) {
    //Split list into two parts:
    Node slow = head, fast = head;
    
    //Advance one pointer twice as fast
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    //Now head2 will be the start of the second half
    Node head2 = slow.next;
    slow.next = null; //Truncate the original list

    //Reverse head2's list
    if (head2 != null && head2.next != null) {
        //Head2 is current, left is to the left, right is to the right
        Node left = null;

        //Keep going until current node runs off the right end 
        while (head2 != null) {
            //Store right, and set head2's pointer to left side
            Node right = head2.next;
            head2.next = left;

            //Advance left and head2, repeat
            left = head2;
            head2 = right;
        }

        //Head2 ran off to null, so left is the new start of reversed list
        head2 = left;
    }

    //Insert and intersperse
    //Iterate through list 1, jump by 2 since we want to insert at every other element
    for (Node ptr = head; ptr != null && ptr.next != null; ptr = ptr.next.next) {
        Node temp = head2.next; //Store the rest of list 2
        head2.next = ptr.next; //Head2 points to the rest of list 1
        ptr.next = head2; //Now head2 is inserted to the list
        head2 = temp; //Advance head2 to the next element in the list
    }
    
    //Note by the end, head2 has been completely deleted and is null
    //All that's left is the interspersed list starting at head
}
```
If the size of the original list is n, our runtime is O(n) since we iterate through the whole list, but there are no nested loops. Our space complexity is O(1) since we are only working with the original nodes and temporary pointer variables. 
</details>
<br>

# Largest Rectangle in Histogram (Insane)
Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram. Refer to the following leetcode link for a visual and to write/test your code: https://leetcode.com/problems/largest-rectangle-in-histogram/

NOTE: I was not personally able to come up with an easily explainable solution faster than O(n^2). This is not fast enough to pass the leetcode test cases, so don't stress if you are getting TLE.

<details>
<summary>Click to reveal solution</summary>

## My Solution
This is not the most efficient solution, but it is one I feel I can explain clearly. I am essentially trying every combination of 2 indices, and returning the max area rectangle of all of them. In order to compute the rectangle between two indices, I need to know the min in that range. Finding the min from scratch every time would be inefficient, but luckily since I am stepping through the array, I can maintain the current minimum in the range I am working, and not waste runtime. 

```java
public int largestRectangleArea(int[] heights) {
    int n = heights.length;
    int largest = 0; //Stores current largest area

    //i represents the starting index of my range 
    for (int i = 0; i < n; i++) {
        //I keep a running min, starting at index i
        int currentMin = heights[i];
        
        //j represents the ending index, and starts at i for 1 cell rects
        for (int j = i; j < n; j++) {
            //Update the currentMin based on heights[j]
            currentMin = Math.min(currentMin, heights[j]);
            
            //The rectangle from i to j is (j-i+1) * currentMin
            largest = Math.max(largest, (j-i+1)*currentMin);
        }
    }
    
    //Return the largest rectangle area seen
    return largest;
}
```
If n is the length of the heights array, the runtime of this solution is O(n^2) since the inner loop runs 1 + 2 + 3... + n = n(n+2)/2 times, and inside this loop we are only doing constant time operations. The space complexity is O(1) since we only create 2 new variables.

## Efficient Solution
Refer to the following youtube video for a full explanation of the efficient solution: https://www.youtube.com/watch?v=ZmnqCZp9bBs
</details>