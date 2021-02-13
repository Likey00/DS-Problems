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
    Stack<Integer> s = new Stack<Integer>();
    
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