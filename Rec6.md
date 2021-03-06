# Classes and Methods
This problemset is focused on BST's, and will slowly walk you through building your own BST with various methods to operate on it. I will start you off with a basic BST template with few methods implemented, and the problems will require you to implement the rest of the BST methods yourself.

 **Insert** is the first problem, since it is useful to be able to insert elements in order to test any of the other methods. If you have trouble implementing it, I suggest looking through the solution and writing a correct implementation in your BST before continuing. 
 
 Some of the methods require access to the **size** of a subtree, so it is included in the Node class. I have provided a wrapper `size()` method which returns 0 for a null node, and the size variable for a non null node. I have also provided a `print()` method to print out your tree to help with testing. 

 Keep in mind for **recursive** methods, it is good practice to write a recursive **private** method with all the recursion parameters you need, and then write a **public** wrapper method after that simply passes the root into this method, and does not require you to specify the Node you're operating on. This abstracts unnecessary parameters away from the user, and using/testing the BST methods is as streamlined as possible.

 Note that the vast majority of problems in this set involve recursion, and if you don't feel comfortable with it I highly recommend https://codingbat.com/java/Recursion-1

## BST Base Class
```java
public class BST {
    //I implement the BST Node class first
    private static class Node {
        private int key;
        private Node left, right;
        private int size;

        //By default a Node will have a key and size 1
        public Node(int k) {
            key = k;
            left = null;
            right = null;
            size = 1;
        }
    }

    //The BST Object stores a reference to the root of the tree
    private Node root;

    //Wrapper method for size, makes sure null nodes are handled
    public static int size(Node n) {
        return (n == null) ? 0 : n.size;
    }
    
    //This is a method to print out the BST in your terminal to help you test
    //Not part of your curriculum, but you can try to understand it after completing this problemset!
    private static void print(Node n, String indent, boolean isRight, boolean isRoot) {
        System.out.print(indent);
        
        if (!isRoot) System.out.print(isRight ? "┣━R━ " : "┗━L━ ");
        else System.out.print("┗━━━ ");
        if (n == null) {
            System.out.println("null");
            return;
        }
        
        System.out.println(n.key);

        if (n.left == null && n.right == null) return;

        indent += isRight ? "┃    " : "     ";

        print(n.right, indent, true, false);
        print(n.left,  indent, false, false);
    }

    //Wrapper non-recursive method which doesn't require parameters
    //This makes it so that you can run the method simply as ".print()" in your main method
    public void print() {
        print(root, "", false, true);
    }
} 
```
<br>

# Insert (Hard)
Write a **recursive** method `insert` in your BST class to insert a new key in the correct place. Your recursive method should take in the current root Node and the key to insert, and return a Node representing the root of the tree after the element is inserted. As an extra challenge, once you implemented a proper insert method, try to implement it in such a way that it updates the size variable of each node to be accurate after the insertion.

<details>
<summary>Click to reveal solution</summary>

## Solution
Insert for BST involves navigating to where the node would go if it existed in the list, then inserting the new node when you hit null. If the new key's value already exists, your method can simply do nothing. We can break the recursion into several cases. 

The first case is where the root is null. In this case, we can simply return a new node containing the new key. 

The next case is when the key we want to insert is less than the current node's key. In this case, we want to set the current node's ".left" to a recursive call to the left, since we know we will be inserting somewhere in the left subtree. If the key we want to insert is greater, we want to do the same but for the right.

```java
//Takes in a node and key, and returns the node after the new element is inserted
private static Node insert(Node n, int k) {
    //If current node is null, return a new node with new key
    if (n == null) return new Node(k);

    //If new key is smaller, set n.left to a recursive call
    if (k < n.key) n.left = insert(n.left, k);

    //If new key is bigger, set n.right to a recursive call
    else if (k > n.key) n.right = insert(n.right, k);

    //Update the size of the current node, and then return it
    n.size = size(n.left) + size(n.right) + 1;
    return n;

    //Note that if k == n.key, nothing happens and n is returned unchanged
}

//Public wrapper method, only takes in k and applies insert to the root
public void insert(int k) {
    root = insert(root, k);
}
```
If n is the number of elements in the BST, the worst case runtime of insert is O(n) given a completely imbalanced tree, since you may have to traverse every element in the tree before inserting. The space complexity in this worst case is O(n) as well, since you are making n recursive calls, resulting in O(n) call stack space. Even though the worst case is O(n), in the average case with a relatively balanced tree, the runtime and space complexity should both be O(logn), corresponding to the height of the tree. 
</details>
<br>

# Level Order Traversal
Write a method `printLevel()` in your BST class which prints out your tree in *level order*. This means you print out the root key, then you print out the keys on the second level in order, then the keys on the third level in order, etc. Every node in the above levels should have been printed before the current level, and every level should be printed in order from left to right. This order of visiting nodes is also known as a *BFS*. Hint: Use a Queue.

Note: I will be using the built in Java Queue class for my solution.

<details>
<summary>Click to reveal solution</summary>

## Solution
The procedure to conduct a level order traversal of a BST with a queue involves first enqueueing the root. Then, at each step, you dequeue from the queue, enqueue the children of the current node, and print out the current node. This process ensures that all the nodes of one level are visited before the next, since the next level is enqueued after all the nodes of the current level. By enqueueing the left and then the right child, every level will be printed out from left to right. 

```java
public void printLevel() {
    Queue<Node> q = new LinkedList<>();
    q.add(root); //Enqueue the root

    //Keep going until we run out of nodes to print
    while (!q.isEmpty()) {
        //Store the dequeued node
        Node temp = q.remove();

        //Enqueue the left and right if they're not null
        if (temp.left != null) q.add(temp.left);
        if (temp.right != null) q.add(temp.right);

        //Print out the current node and continue
        System.out.print(temp.key + " ");
    }
}
```
If n is the number of elements in the BST, the runtime is O(n) since each element is enqueued once and dequeued once. The space complexity is also O(n) since there is a point when the entire bottom row of the tree is enqueued. If the tree is complete, this row is about size n/2, making the space complexity O(n).
</details>
<br>

# Max
Write a **recursive** method `max` in your BST class which takes in a Node as an argument and returns the maximum key in the BST.

<details>
<summary>Click to reveal solution</summary>

## Solution
In a BST, all the nodes to the left of the current node have a key with less value, and all the nodes to the right have greater value. 

Thus, if the right is null, the current node contains the max key, and we can simply return it. 

Otherwise, we know the max is somewhere on the right, so we can recursively search the right subtree. If the given node is null we don't need to do anything, since our program will throw a Null Pointer Exception, indicating there is no max.

```java
//Takes in current node, returns max of that subtree
private static int max(Node n) {
    //If there is no right subtree, just return key
    if (n.right == null) return n.key;

    //Otherwise, recurse on the right
    return max(n.right);
}

//Wrapper method with no arguments, calls max on root
public int max() {
    return max(root);
}
```
If n is the number of nodes in the BST, the method will have a runtime of O(n) in the worst case, since the tree will be completely unbalanced to the right, and all the nodes will have to be visited before you get to the max. The space complexity in the worst case would also be O(n), since you're making n recursive calls which are stored in the call stack. However in the average case for a relatively balanced tree, the height of the tree will be on the order of logn, so both the time and space complexity would be O(logn).
</details>
<br>

# Search
Write a **recursive** method `search` in your BST class which takes in a Node and a key, and returns true if the key exists in the BST and false otherwise. This method is similar to the insert method, but does not modify the tree.

<details>
<summary>Click to reveal solution</summary>

## Solution
If the given node is null, then the given key cannot exist in the tree, so we return false. 

If the key of the given node is the same as the given key, we found it so we can return true. Otherwise, based on whether the given is less than or greater than that of the node, we can continue searching on either the left or the right.

```java
//Takes in a node and key, returns whether the key is present in the subtree
private static boolean search(Node n, int k) {
    //If n is null, the key isn't present
    if (n == null) return false;

    //If k == n.key, we've found the element
    if (k == n.key) return true;

    //If k is too small, search on the left, and otherwise search on the right
    if (k < n.key) return search(n.left, k);
    return search(n.right, k);
}

//Wrapper method which only requires k, and runs search on root
public boolean search(int k) {
    return search(root, k);
}
```
If n is the number of elements in the BST, the worst case runtime is O(n) since the tree could be completely imbalanced, and all the nodes would have to be visited. The worst case space complexity is also O(n) since we make n recursive calls, which take up call stack space. However, in the average case when the tree is relatively balanced, the height of the tree is on the order of logn, thus both the time and space complexity would be O(logn).
</details>
<br>

# Ceiling (Hard)
Write a **recursive** method `ceiling` in your BST class which takes in a Node and a target value, and returns the *smallest* element present in the BST which is *greater than or equal to* the target value. I recommend returning an Integer rather than an int so you can return null if necessary, indicating no ceiling was found.

<details>
<summary>Click to reveal solution</summary>

## Solution
If the given node is null, there is no ceiling and we can simply return null, indicating no ceiling was found.

If the target value is equal to the current node's key, we know there will be no smaller element which is greater than or equal to the target, so we can simply return the key. 

If the target value is greater than the current node's key, we know that the ceiling will have to be on the right if it exists, so we can simply recurse to the right. 

If the target value is less than the current node's key, it is *possible* that we are at the solution, but there may be a solution which is greater than or equal to the target to the left. Thus, we recurse to the left, and check the result of this recursion. If we managed to find a closer element, we will return that. If the recursion returned null indicating no ceiling was found on the left, we are at our solution and we can return the key of the current node.

```java
//Takes in current node and target, and returns an Integer (can be null)
private static Integer ceiling(Node n, int target) {
    //If n is null, no ceiling was found so we return null 
    if (n == null) return null;

    //If we found the exact key, we can simply return it
    if (target == n.key) return n.key;
    
    //If we are still less than target, we know our solution is on the right
    if (target > n.key) return ceiling(n.right, target);

    //If we're greater, we first store the result to the left    
    Integer leftCeil = ceiling(n.left, target);
    
    //If no ceiling found, return current key
    //Otherwise, return that value
    return (leftCeil == null) ? n.key : leftCeil;
}

//Wrapper method which only needs target, and runs ceiling on root
public Integer ceiling(int target) {
    return ceiling(root, target);
}
```
If n is the number of elements in the BST, the worst case is that we get a completely unbalanced tree and we go all the way to the bottom, in which case the time and space complexity are O(n), since we must visit every node and make n recursive calls, which take up call stack space. In the average case where the tree is relatively balanced, the height is on the order of logn, so the runtime and space complexity would be O(logn).
</details>
<br>

# Rank (Hard)
Write a **recursive** method `rank` in your BST class which takes in a Node and a target value, and returns the *number of elements in the BST* which are smaller than the element containing target. If there exists no element containing target, your program can just throw an exception. Another way to interpret the meaning of rank is the *index* of the element containing target if the BST was converted to a sorted array. 

NOTE: This method will require the use of `size()`, so make sure your insert method properly updates the size variables for each node before starting this one!

<details>
<summary>Click to reveal solution</summary>

## Solution
In order to find the rank of the given element, we must locate the node containing the element, and then use the size of the left subtree to determine the number of elements less than the target. Note that as we traverse to the right, we may be excluding all the smaller elements that were in the left subtree, so we need to handle that as a special case.

If the given node is null, we don't need to do anything, and we can let the program throw a NPE.

If the target is equal to the current node's key, we can simply return the size of the left subtree, since the left subtree consists of all the elements which are smaller than the current node.

If the target is less than the current node's key, we need to continue searching for the target recursively on the left

If the target is greater than the current node's key, we need to continue searching recursively on the right. Unfortunately, when we go to the right, we are no longer considering all the elements which are less in the left subtree, and the current node. Thus, we return 1 + the size of the left subtree + the recursive call. This ensures we are counting the size of the left subtree and the current node as elements which are less.

```java
//Takes in the current node and a target, returns the rank of the target in the tree
private static int rank(Node n, int target) {
    //If we find the target, we can simply return the size of the left subtree
    if (target == n.key) return size(n.left);
    
    //If target is less, we can recursively search on the left
    if (target < n.key) return rank(n.left, target);
    
    //If it's greater, we need to add size(n.left) + 1 to the rank in the right subtree
    return size(n.left) + 1 + rank(n.right, target);
}

//Wrapper method which only needs target and calls rank on root
public int rank(int target) {
    return rank(root, target);
}
```
If n is the number of elements in the BST, the worst case is that we get a completely unbalanced tree and we go all the way to the bottom, in which case the time and space complexity are O(n), since we must visit every node and make n recursive calls, which take up call stack space. In the average case where the tree is relatively balanced, the height is on the order of logn, so the runtime and space complexity would be O(logn).
</details>
<br>

# Kth Largest (Hard)
Write a **recursive** method `kthLargest` in your BST class which takes in a Node and a number k, and returns the kth largest element in the tree. For example, if k = 1, you would return the largest element. If k = 2, you would return the second largest element, and so on.

NOTE: This method will require the use of `size()`, so make sure your insert method properly updates the size variables for each node before starting this one!

<details>
<summary>Click to reveal solution</summary>

## Solution
The important quantity to consider when finding the kth largest element is the size of the right subtree. Since the right subtree contains all the elements greater than the current node, we know we're at the kth largest element if its size is k-1. If it's bigger or smaller, we can search recursively to the left or right, but we have to careful when going to the left, because we need to adjust our k to account for all the greater elements on the right subtree that we are no longer considering.

If our current node is null, there is no kth largest so the input was invalid. We can just let Java throw a Null Pointer Exception, so we don't need to do anything in this case.

If the size of the right subtree is k-1, we are at the kth largest so we can simply return the current key.

If the size of the right subtree is greater than k-1, we know our kth largest is in the right subtree, so we can continue searching there.

If the size of the right subtree is less than k-1, we know our kth largest is in the left subtree. As we go to the left, we don't actually want the kth largest in the left subtree, we want to adjust it so it's not considering everything to the right of it. Thus, we pass in k - size of right subtree - 1, which accounts for the right subtree and the current node.

```java
//Takes in current node and k, returns the key of the kth largest element in the subtree
private static int kthLargest(Node n, int k) {
    //If the size of the right subtree is k-1, just return key
    if (size(n.right) == k-1) return n.key;

    //If it's bigger, recursively search on the right
    if (size(n.right) >= k) return kthLargest(n.right, k);
    
    //If it's smaller, search on the left, but adjust k to remove current node and right subtree
    return kthLargest(n.left, k-size(n.right)-1);
}

//Wrapper method which only needs k, and runs kthLargest on root
public Integer kthLargest(int k) {
    return kthLargest(root, k);
}
```
If n is the number of elements in the BST, the worst case is that we get a completely unbalanced tree and we go all the way to the bottom, in which case the time and space complexity are O(n), since we must visit every node and make n recursive calls, which take up call stack space. In the average case where the tree is relatively balanced, the height is on the order of logn, so the runtime and space complexity would be O(logn).
</details>
<br>

# Nums In Range (Hard)
Write a **recursive** method `numsInRange` in your BST class which takes in a Node, a number lo, and a number hi, and returns the number of elements in the BST which are between lo and hi inclusive. 

<details>
<summary>Click to reveal solution</summary>

## Solution
If our input node is null, there are no elements in the range to be found here, so we can simply return 0.

If our input node has a key in the range, we can add 1 to our solution variable, and this 1 represents the current node.

If our input node is less than hi, there might still be more nodes in the range to the right of the current node, so we can add a recursive call to the right to our solution variable.

If our input node is greater than lo, there might still be more nodes in the range to the left of the current node, so we can add a recursive call to the left to our solution variable.

Finally, we can just return our solution variable.

```java
//Takes in current node and range (lo, hi), returns number of elements in current BST in that range
private static int numsInRange(Node n, int lo, int hi) {
    //If n is null, there are no nums in range rooted here
    if (n == null) return 0;

    //If n.key is in the range, I start result at 1 to account for the current node
    //Otherwise, I start it at 0, meaning we aren't in the range yet 
    int result = (n.key >= lo && n.key <= hi) ? 1 : 0;
    
    //If key < hi, I add the numsInRange of the right subtree
    if (n.key < hi) result += numsInRange(n.right, lo, hi);

    //If key > lo, same thing for left subtree
    if (n.key > lo) result += numsInRange(n.left, lo, hi);

    //I can now return the total number of nums in the range
    return result;
}

//Wrapper method which only requires lo and hi, calls numsInRange on the root
public int numsInRange(int lo, int hi) {
    return numsInRange(root, lo, hi);
}
```
If n is the number of elements in the BST, the worst case is that we get a completely unbalanced tree and we go all the way to the bottom, in which case the time and space complexity are O(n), since we must visit every node and make n recursive calls, which take up call stack space. In the average case where the tree is relatively balanced, the height is on the order of logn, so the runtime and space complexity would be O(logn).
</details>