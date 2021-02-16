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
Write a method in your CLL class to insert an element `newValue` at the front. Remember that CLLs store a reference to last, and last.next is the front of the list. Think about the possible cases that need to be covered.

**Example Input:** `1->2->3->4->5(last)`, `newValue = 17`, remember that 5 is the stored node last, and it also has a pointer back to 1. 

**Example Output:** `17->1->2->3->4->5(last)`, Now 5 points to 17 which points to 1

<details>
<summary>Click to reveal solution</summary>

## Solution
Since last.next is the front of the CLL, we essentially want to insert a node at last.next. By drawing a diagram, it can be visualized that we want to first create a new node with `newValue`, then make last.next point to this new node, and make this new node point to the old last.next. The only special case is when the list is empty, in which case we can create a new node which points to itself and set it to last.

```java
public void insertFront(T newValue) {
    //If the CLL is empty
    if (last == null) {
        //Create a new node with newValue, and set its reference to itself
        last = new Node<T>(newValue, null);
        last.next = last;
        return;
        
        //Note that I couldn't do this in one line, since using last in the 
        //constructor would set the new node's next to null
    }
    
    //If the CLL isn't empty, just make new node point to last.next, and last point to new node
    last.next = new Node<T>(newValue, last.next);
}
```
If n is the length of the CLL at the time this method is called, the time and space complexity are both O(1) since you are always only creating 1 new node and 2 pointers, and there is no list traversal which is dependent on n.
</details>
<br>

# Insert After Target - DLL
Write a method in your DLL class to insert an element `newValue` after the node containing `target`. If there is no node containing `target`, do nothing. Think about the possible cases that need to be covered. If you're using my provided DLL class, remember that `newValue` and `target` are of type T so comparisons will have to use `.equals()` rather than `==`.

**Example Input:** `1<->2<->3<->4`, `target = 3`, `newValue = 17`

**Example Output:** `1<->2<->3<->17<->4`

<details>
<summary>Click to reveal solution</summary>

## Solution
A good first step is to get access to the node containing `target`, and this can be accomplished by iterating through the list until we reach it. Let's call the node containing `target` the target node, the new node we create that contains `newValue` the new node, and the original target node's ".next" the right node. It can be visualized by drawing a diagram that in order to insert the new node, we need to set the target node's ".next" to the new node and the new node's ".next" to the right node. If this were a singly linked list we would be done, but we also need to update the new node's ".prev" to the target node, and the right node's ".prev" to the new node. 

```java
public void insertAfter(T target, T newValue) {
    //Iterate through the list
    for (Node<T> ptr = head; ptr != null; ptr = ptr.next) {
        //Notice I used .equals since we're working with objects
        if (ptr.data.equals(target)) {
            //Set up the new node with its value, its prev, and its next pointers
            Node<T> newNode = new Node<T>(newValue, target, target.next);
            
            //If the right node isn't null, set its prev
            //I need to check since calling .prev on null leads to NPE
            if (target.next != null) target.next.prev = newNode;

            //Set the target's next finally
            //We have to do it at the end since otherwise right node would be lost
            target.next = newNode;
        }
    }

    //Notice how if the target isn't found nothing happens
}
```
If n is the size of the DLL at the time this method is called, the runtime is O(n) since it is possible that the whole list is traversed. The space complexity is O(1) since we always create two new nodes, one for the pointer and one for the new list element.
</details>
<br>

# Queue - CLL Implementation
Write a queue implementation (this means a new queue class) that uses a CLL. If you're using my provided CLL class, note that your queue will be of type T (generic). The queue should implement the following methods:

- `void enqueue(T x)`: Enqueues element x to the front of the queue
- `int dequeue()`: Removes the element at the back of the queue and returns it
- `boolean isEmpty()`: Returns true if the queue is empty, false otherwise

NOTE: You might want to add some methods to your CLL class before implementing the queue, I suggest `insertEnd()`, `removeFront()`, and `isEmpty()`.

<details>
<summary>Click to reveal solution</summary>

## Solution
We must implement `insertEnd`, `removeFront`, and `isEmpty` into our CLL class before we can use it to make our queue. Let's go through these 3 methods.

`insertEnd`: If we're inserting at the end of the CLL, the last pointer will have to be updated to this new node. If you draw a diagram, you will see that we need to first create a new node with the new value, then set its ".next" to the old last's ".next", set the old last's ".next" to the new node, and finally update last to this new node.

```java
public void insertEnd(T newValue) {
    //If empty CLL, just create a node with newValue which points to itself
    if (last == null) {
        last = new Node<T>(newValue, null);
        last.next = last;
        return;
    }

    //Set last.next to the new node, and set the new node's .next to the front
    last.next = new Node<T>(newValue, last.next);

    //Update last to be the new node, since it's at the end now
    last = last.next;
}
```

`removeFront`: Since we have a reference to last, and the front is last.next, we can simply remove it with last.next = last.next.next. We do have to be careful of the situation where there is only one node, since we'd end up not doing anything. Note that we need to return the element we removed in order for our dequeue function to work later.

```java
public T removeFront() {
    //Note how if the list is empty, we just let java throw an exception
    T elem = last.data; //Store for returning later

    //If CLL has one node, just set last to null to delete
    if (last.next == last) last = null;
    //Otherwise, we can just delete front with last.next = last.next.next
    else last.next = last.next.next

    return elem; 
}
```

`isEmpty`: This one is simple, but necessary since our CLL last pointer is private. All we have to do is return whether or not last is null.

```java
public boolean isEmpty() {
    return last == null
}
```

Now that our CLL has all the methods we need, we are ready to implement our queue class. Since we did all the heavy lifting and logic in our CLL class, we have abstracted away the internal node structure, which will make this implementation very clean.

```java
public class Queue<T> {
    private CLL<T> list;

    public Queue() {
        list = new CLL<T>();
    }
    
    public void enqueue(T x) {
        list.insertEnd(x);
    }
    
    public T dequeue() {
        return list.removeFront();
    }

    public boolean isEmpty() {
        return list.isEmpty();
    }
}
```
Wasn't that final implementation nice? As expected, our time complexity for all these methods is O(1) since we aren't traversing through the list, we are only working with the last and front which we have immediate access to. The space complexity for each method individually is O(1) as well, since we are only ever creating one node at most. An important thing to consider is that if you were to add one element the time and space complexity would be O(1), but if you were to add on the order of n elements, it would be O(n) for both.
</details>
<br>

# Linked List Cycle
Given the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to. Note that pos is not passed as a parameter.

Return true if there is a cycle in the linked list. Otherwise, return false.

LeetCode: https://leetcode.com/problems/linked-list-cycle/

Solutions will be available on `2/19`
<br>

# Delete Target Node - CLL
Write a method in your CLL class to delete the node containing `target` from the list. If there is no node containing `target`, do nothing. Think about the different cases. Remember that if you're using my provided CLL class, `target` is of type T, so comparisons will need to use `.equals()` rather than `==`.

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