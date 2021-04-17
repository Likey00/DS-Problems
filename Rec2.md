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

# Remove After - Linked Lists
Write a method removeAfter that takes a Node `head` and an int `target` as arguments. The method should remove the Node right after the Node containing `target` from the list, but keep the rest of the list intact. If there is no node containing `target`, the node after `target` is null, or the list is empty, do nothing.

**Example Input:** `head = 1->2->3->4->5`, `target = 3`

**Example Output:** `head = 1->2->3->5`

<details>
    <summary>Click to reveal solution</summary>

## Solution
Drawing a picture helps for linked list problems like this. If you draw a diagram and attempt to delete a node, it can be visualized that deleting the node after a given node involves setting the given node's ".next" to the given node's ".next.next". It must however be verified that the given node's ".next" is not null, since calling ".next.next" will result in a NPE. All that's left is to loop through the list, find the target node, and apply this process.

```java
public static void removeAfter(Node head, int target) {
    //Iterate through the entire linked list
    for (Node ptr = head; ptr != null; ptr = ptr.next) {
        //If the node is the target node, I must check if ptr.next is null first
        //Then I can use ptr.next = ptr.next.next to delete the following node
        if (ptr.data == target && ptr.next != null) {
            ptr.next = ptr.next.next;
            break;
        }
    }
    //Note that if head is null, the loop will not run at all, and nothing happens
}
```
If n is the size of the input list, the time complexity is O(n), since in the worst case the list is traversed once. The space complexity is O(1), since the only new variable is one Node object ptr. 
</details>
<br>

# Insert After - Linked Lists
Write a method insertAfter that takes a Node `head`, an int `target`, and an int `newValue` as arguments. The method should insert a new node containing `newValue` right after the node containing `target`, keeping the rest of the list intact. If there is no element containing target in the list or the list is empty, do nothing.

**Example Input:** `head = 1->2->3->4->5`, `target = 3`, `newValue = 17`

**Example Output:** `head = 1->2->3->17->4->5`

<details>
    <summary>Click to reveal solution</summary>

## Solution
Drawing a picture to determine what you want to do is once again important here. Once you find the target Node, you want to set the target's ".next" to the new node, and the new node's ".next" to the rest of the list. 

```java
public static void insertAfter(Node head, int target, int newValue) {
    //Iterate through the entire list
    for (Node ptr = head; ptr != null; ptr = ptr.next) {
        //If we reach the target node, create a new node that contains newValue and 
        //points to the rest of the list. Then set ptr.next to this new node.
        if (ptr.data == target) {
            ptr.next = new Node(newValue, ptr.next);
            break;
        }
    }
}
```
If n is the size of the input list, the time complexity is O(n) since in the worst case the list is traversed once. The space complexity is O(1) since we only created a pointer variable and a new node.
</details>
<br>

# Convert Binary in LL to Integer

Write a method toDecimal that takes a node `head` as an argument, where every node in the linked list contains either a 0 or a 1. Return the decimal value of the binary number stored in the linked list.

**Example Input:** `head = 1->0->1->1`

**Example Output:** 11

**Explanation:** This linked list represents 1(8) + 0(4) + 1(2) + 1(1) = 11

<details>
<summary>Click to reveal solution</summary>

## Solution

While it is possible to go through the list once to determine the length and then assign the proper powers of 2 on the second pass, it is possible to solve this problem in one pass with a constructive approach. Maintain a variable to store the number represented by the nodes you have seen so far. Whenever you advance to a new node, simply double the current number and add the new node's value. This doubling is equivalent to shifting the binary representation left by one place value.

```java
public static int toDecimal(Node head) {
    int currentValue = 0;

    //Iterate through the list
    for (Node ptr = head; ptr != null; ptr = ptr.next) {
        //For each node, double current value and add its value
        currentValue = 2*currentValue + ptr.data;
    }
    
    return currentValue;
}
```
If n is the size of the input binary list, the time complexity is O(n) since you must traverse through the list once, and do 2 operations per element. The space complexity is O(1) since we only created a new int and a pointer variable. 
</details>
<br>

# Implement Stack Using Queues

Implement a last in first out (LIFO) stack using only two queues. The implemented stack should behave as a normal stack, and support `push`, `top`, `pop`, and `isEmpty`.

- `void push(int x)`: Pushes element x to the top of the stack
- `int pop()`: Removes the element on the top of the stack and returns it
- `int top()`: Returns the element on the top of the stack
- `boolean isEmpty()`: Returns true if the stack is empty, false otherwise

NOTE: I will be using the built in java Queue class for this solution

<details>
<summary>Click to reveal solution</summary>

## Solution
Let's call the two queues we have access to `q1` and `q2`. `q1` will function as our "main queue" and `q2` will be our temporary storage queue to help us implement the LIFO functionality. Note that there are multiple ways to do this problem, and this is just one of them. `push` can be implemented by simply adding the element to the `q1`. `pop` needs to remove and return the most recently added element to `q1` to follow stack behavior, so we must move all the elements of `q1` to `q2` until there is only one left, then remove and return that element. Then we can swap `q2` and `q1` so `q2` is now the main queue. `top` is the same as `pop`, but we add the last element back into `q2`. `isEmpty` simply checks if the main queue is empty.

```java
import java.util.LinkedList;
import java.util.Queue;

class myStack {
    private Queue<Integer> q1 = new LinkedList<Integer>();
    private Queue<Integer> q2 = new LinkedList<Integer>(); 

    public void push(int x) {
        q1.add(x);
    }
    
    public int pop() {
        int lastElement;

        //Move all but 1 element to q2
        while (q1.size() > 1) {
            q2.add(q1.remove());
        }

        //Store the last element
        lastElement = q1.remove();
        
        //Swap q1 and q2
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;

        //Return stored element
        return lastElement;
    }
    
    public int top() {
        //Just pop, push it back in, and return
        int lastElement = pop();
        push(lastElement);
        
        return lastElement;
    }
    
    public boolean isEmpty() {
        return q1.isEmpty();
    }
}
```
If n is the number of elements currently in our implemented stack, `push` runs in O(1) time, `pop` runs in O(n) time, `top` runs in O(n) time, and `isEmpty` runs in O(1) time. Note that in a proper stack these operations should all have an expected runtime of O(1), so this implementation was more of an exercise than a good stack. 
</details>