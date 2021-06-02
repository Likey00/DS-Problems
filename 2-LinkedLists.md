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

# Linked List Cycle
Given the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to. Note that pos is not passed as a parameter.

Return true if there is a cycle in the linked list. Otherwise, return false.

LeetCode: https://leetcode.com/problems/linked-list-cycle/

<details>
<summary>Click to reveal solution</summary>

## Solution
The solution method for this problem is called Floyd's Cycle Finding Algorithm, and is also known as the Tortoise and Hare method. The idea is that you have one fast pointer and one slow pointer. If there's no cycle in the list, the fast pointer will run off to null and you can return false. If there is a cycle in the list, at some point the slow pointer will have to coincide with the fast pointer. You can think about this as the fast pointer "lapping" the slow pointer in a race. If the pointers meet up, you can return true. Note that I do not use my Node class, I use leetcode's provided ListNode class, since that's where I wrote and tested my code.

```java
public boolean hasCycle(ListNode head) {
    //Empty list has no cycles
    if (head == null) return false;

    //T is for tortoise, H is for hare, hare starts a little ahead 
    ListNode t = head, h = head.next;

    //Keep advancing t by 1 and h by 2, and checking that h.next is not null to avoid NPE 
    for (; h != null && h.next != null; t = t.next, h = h.next.next) {
        //If they ever reference the same node object, we can return true
        if (t == h) return true;
    }

    //If we make it through the loop, the hare encountered null so there was no cycle 
    return false;
}
```
If n is the length of the input list including the possible cycle, we have 2 possible cases to consider for the time complexity: No cycle or cycle. If there is no cycle, the hare will reach the end in n/2 iterations, so the runtime is O(n). If there is a cycle, the tortoise will take some number of steps X to enter the cycle, and be lapped in some number of steps Y from there. X is bounded by n, and Y is bounded by the length of the cycle which is also bounded by n. All in all, we have O(n) for the time complexity in both input cases. The space complexity is O(1) since we only create 2 new pointers.

NOTE: A more natural solution can be constructed using a hash table to store nodes we have already seen as we traverse with one pointer. The time complexity would still be O(n), but the space complexity would be O(n) as well. If you don't know what hash tables are yet, you can revisit this approach later.
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

# Partition - Linked List (Hard)
Write a method partition() which takes a Node<Integer> `head` and an int `target` as arguments, and reorders the list such that all the elements with a value less than `target` appear before all the elements with a value greater than or equal to `target`. The element(s) containing `target` can appear anywhere in the right side, and do not have to be in between the values less and the values greater.

**Example Input:** `3->5->8->5->10->2->1`, `partition = 5`

**Example Output:** `3->1->2->10->5->5->8`, Note that this is one of many possible solutions with the right property

<details>
<summary>Click to reveal solution</summary>

## Solution
We can denote 2 pointers called front and back in order to keep track of places we can insert nodes as we traverse. If the element is less than `target`, we can insert it to the very front of the list, and if the element is greater than or equal to `target`, we can insert it to the very end of the list. We are building our new list as we go through, so the front and end can both begin at the first node of the list. As we traverse through, the distance between these pointers will grow, and eventually the whole list will be partitioned.

```java
public static Node<Integer> partition(Node<Integer> head, int target) {
    Node<Integer> front = head, back = head;

    //Notice we are traversing head, so the original list is being deleted
    while (head != null) {
        //Store the next node we want to visit
        Node<Integer> next = head.next;

        //If head is less, we insert it to the front
        if (head.data < target) {
            //Make it point to current front, and update front
            head.next = front;
            front = head;
        }
        
        //Otherwise, we insert it to back
        else {
            //Make back point to it, and update back
            back.next = head;
            back = head;
            head.next = null; //Truncate this pointer, we don't want infinite loops
        }

        //Update head to the next we stored earlier         
        head = next;
    }

    //Front will be the new head of the list 
    return front;
}
```
Because we loop through the list once, and we have direct access to the front and back pointers, each loop is an O(1) operation, meaning our runtime is O(n). The space complexity is O(1) since we are losing pointers to the original list as we add new pointers to either front or back, so the size of the total list is on the same order as the original list.
</details>
<br>
