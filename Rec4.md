# Classes and Methods
Before introducing any problems, I would like to share the various classes and methods which I use in my solutions, and also some methods which I use to test my code. Your implementations of these classes may be different, but sharing mine will allow you to understand my solutions completely. Note: This time I will not include a printList method, but it's different based on the type of linked list. Try implementing one yourself for each type!

## Generic Node class
```java
public class Node<T> {
    //Should look familiar, just with generic type T
    T data;
    Node<T> next;

    //No argument constructor
    public Node() {
        data = null;
        next = null;
    }

    //Lets you initialize Node with data and next already set
    public Node(T d, Node<T> n) {
        data = d;
        next = n;
    }
}
```

## Circular Linked List base class
```java
public class CLL<T> {
    //I nested a node class for private use, it's the same as above
    private class Node<T> {
        T data;
        Node<T> next;
        
        public Node() {
            data = null;
            next = null;
        }
        
        public Node(T d, Node<T> n) {
            data = d;
            next = n;
        }
    }

    //Just need one reference to back of CLL    
    private Node<T> last;
}
```
## Doubly Linked List base class
```java
public class DLL<T> {
    //This time the nested node class has prev pointer
    private class Node<T> {
        T data;
        Node<T> prev, next;

        public Node() {
            data = null;
            prev = null;
            next = null;
        }
        
        public Node(T d, Node<T> p, Node<T> n) {
            data = d;
            prev = p;
            next = n;
        }
    }

    //Just need one reference to head of DLL 
    private Node<T> head;
}
```
<br>

# Insert at Front - CLL
Write a method in your CLL class to insert an element `newValue` at the front. Remember that CLLs store a reference to last, and last.next is the front of the list. Think about the possible cases that need to be covered. If you're using my provided CLL class, remember that `newValue` is of type T so comparisons will have to use `.equals()` rather than `==`. 

**Example Input:** `1->2->3->4->5(last)`, `newValue = 17`, remember that 5 is the stored node last, and it also has a pointer back to 1. 

**Example Output:** `17->1->2->3->4->5(last)`, Now 5 points to 17 which points to 1

Solutions will be available on `2/19`
<br>

# Insert After Target - DLL
Write a method in your DLL class to insert an element `newValue` after the node containing `target`. If there is no node containing `target`, do nothing. Think about the possible cases that need to be covered. If you're using my provided DLL class, remember that `newValue` and `target` are of type T so comparisons will have to use `.equals()` rather than `==`.

**Example Input:** `1<->2<->3<->4`, `target = 3`, `newValue = 17`

**Example Output:** `1<->2<->3<->17<->4`

Solutions will be available on `2/19`
<br>

# Queue - CLL Implementation
Write a queue implementation (this means a new queue class) that uses a CLL. If you're using my provided CLL class, note that your queue will be of type T (generic). The queue should implement the following methods:

- `void enqueue(T x)`: Enqueues element x to the front of the queue
- `int dequeue()`: Removes the element at the back of the queue and returns it
- `boolean isEmpty()`: Returns true if the queue is empty, false otherwise

NOTE: You might want to add some methods to your CLL class before implementing the queue, I suggest `insertFront()` and `removeBack()`.

Solutions will be available on `2/19`
<br>

# Linked List Cycle
Given the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to. Note that pos is not passed as a parameter.

Return true if there is a cycle in the linked list. Otherwise, return false.

LeetCode: https://leetcode.com/problems/linked-list-cycle/

Solutions will be available on `2/19`
<br>

# Delete Target Node - CLL
Write a method in your CLL class to delete the node containing `target` from the list. If there is no node containing `target`, do nothing. Think about the different cases.

**Example Input:** `1->2->3->4->5(last)`, `target = 3`, remember that 5 is the stored node last, and it also has a pointer back to 1.

**Example Output:** `1->2->4->5(last)`

Solutions will be available on `2/19`
<br>

# Add to End - DLL
Write a method append() in your DLL class that takes in a T `target` and inserts a new node containing `target` at the end of the DLL.

**Example Input:** `1<->2<->3`, `target = 17`

**Example Output:** `1<->2<->3<->17`

Solutions will be available on `2/19`
<br>

# Partition - Linked List
Write a method partition() which takes a Node<Integer> `head` and an int `target` as arguments, and reorders the list such that all the elements with a value less than `target` appear before all the elements with a value greater than or equal to `target`. The element(s) containing `target` can appear anywhere in the right side, and do not have to be in between the values less and the values greater.

**Example Input:** `3->5->8->5->10->2->1`, `partition = 5`

**Example Output:** `3->1->2->10->5->5->8`, Note that this is one of many possible solutions with the right property

Solutions will be available on `2/19`
<br>