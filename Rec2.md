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
        if (ptr.data == target) ptr.next = new Node(newValue, ptr.next);
        break;
    }
}
```
If n is the size of the input list, the time complexity is O(n) since in the worst case the list is traversed once. The space complexity is O(1) since we only created a pointer variable and a new node.
</details>
