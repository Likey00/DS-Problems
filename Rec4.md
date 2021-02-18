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

`insertEnd`: By drawing a picture, it can be shown that inserting at the end is the exact same thing as inserting at the front, but we have to update the last pointer to the new node. I have already implemented the `insertFront` method in an earlier solution, so I can use that method in this one.

```java
public void insertEnd(T newValue) {
    insertFront(newValue);
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
If n is the length of the input list including the possible cycle, we have 2 possible cases to consider for the time complexity: No cycle or cycle. If there is no cycle, the hare will reach the end in n/2 iterations, so the runtime is O(n). If there is a cycle, the tortoise will take some number of steps X to enter the cycle, and be lapped in some number of steps Y from there. X is bounded by the n, and Y is bounded by the length of the cycle which is also bounded n. All in all, we have O(n) for the time complexity in both input cases. The space complexity is O(1) since we only create 2 new pointers.

NOTE: A more natural solution can be constructed using a hash table to store nodes we have already seen as we traverse with one pointer. The time complexity would still be O(n), but the space complexity would be O(n) as well. If you don't know what hash tables are yet, you can revisit this approach later.
</details>
<br>

# Delete Target Node - CLL
Write a method in your CLL class to delete the node containing `target` from the list. If there is no node containing `target`, do nothing. Think about the different cases. Remember that if you're using my provided CLL class, `target` is of type T, so comparisons will need to use `.equals()` rather than `==`.

**Example Input:** `1->2->3->4->5(last)`, `target = 3`, remember that 5 is the stored node last, and it also has a pointer back to 1.

**Example Output:** `1->2->4->5(last)`

<details>
<summary>Click to reveal solution</summary>

## Solution
The first case to consider is when there are multiple (more than 1) nodes. In this case, we can simply traverse the list until we hit target, maintaining a pointer to the node before it. We can do this either by maintaining a prev pointer (which I will be doing) or checking the current node's ".next" at all times. Once we find the target node, we can simply set the prev's ".next" to the target's ".next" to delete it. If the target node was last, we will need to update our last pointer. If there is only one node, all we have to do is check if that node is equal to target and set last to null if it is. If the list is empty, we just do nothing.

```java
public void deleteTarget(T target) {
    if (last == null) return; //Empty list, do nothing
    
    //If one node with target, just set last to null
    if (last.next == last && last.data.equals(target)) {
        last = null;
        return;
    }
    
    //I handle this case here, since my loop won't be able to handle it
    if (last.next.data.equals(target)) {
        removeFront(); //Already implemented removeFront
        return;
    }
    
    //Traverse with a prev pointer, go until prev is last, so ptr actually hits last
    for (Node ptr = last.next, prev = null; prev != last; prev = ptr, ptr = ptr.next) {
        if (ptr.data.equals(target)) {
            //Delete a node
            prev.next = ptr.next;

            //If we are the last, just update last to the node before
            if (ptr == last) last = prev;
        }
    }
}
```
If n is the length of the CLL, the time complexity is O(n) since we might traverse the entire list. The space complexity is O(1) since we create at most 2 new nodes, for prev and ptr.
</details>
<br>

# Add to End - DLL
Write a method append() in your DLL class that takes in a T `target` and inserts a new node containing `target` at the end of the DLL.

**Example Input:** `1<->2<->3`, `target = 17`

**Example Output:** `1<->2<->3<->17`

<details>
<summary>Click to reveal solution</summary>

## Solution
If the list is empty, simply create a new node with the data and set it as head. If it's not, navigate to the end of the list and add a node with the prev pointer set.

```java
public void append(T target) {
    if (head == null) {
        head = new Node<T>(target, null, null);
        return; 
    }

    Node<T> ptr = head;
    while (ptr.next != null) ptr = ptr.next;

    ptr.next = new Node<T>(target, ptr, null);
}
```
If the length of the DLL at the time this method is called is n, the time complexity is O(n) since the whole list is traversed. The space complexity is O(1) since we are only creating 2 new nodes.
</details>
<br>

# Partition - Linked List
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