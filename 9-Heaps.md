# Classes and Methods
This problemset is focused on Priority Queues, and more specifically the heap implementation of priority queues. It will slowly walk you through building your own Max Priority Queue with various methods to operate on it. I will start you off with a basic heap template with few methods implemented, and the problems will require you to implement the rest of the heap methods yourself. Please note that the heap you will be constructing is a MAX heap, meaning that the parent is greater than its children, and the greatest element is stored at index 1. Implementation of a MIN heap is almost the same, but with opposite comparisons. Even though you will be constructing a max heap, you can still use it to simulate a min heap by negating elements before adding them, and negating them back to normal after taking them out.

## MaxPQ Base Class
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Arrays;

public class MaxPQ {
    //We'll be using an arraylist to handle resizing automatically
    private ArrayList<Integer> heap;

    //Constructor for empty heap
    public MaxPQ() {
        heap = new ArrayList<Integer>();
        heap.add(0); //We ignore index 0, just add a dummy value
    }

    //Constructor for preexisting arraylist (might not be heap yet)
    public MaxPQ(ArrayList<Integer> h) {
        heap = h;
        heap.add(0, 0); //We ignore index 0, just add a dummy value at the beginning
    }

    //Print method to help with testing
    public void print(int index, String indent, boolean isRight, boolean isRoot) {
        System.out.print(indent);

        if (!isRoot) System.out.print(isRight ? "┣━R━ " : "┗━L━ ");
        else System.out.print("┗━━━ ");

        if (index >= heap.size()) {
            System.out.println("empty");
            return;
        }
        
        System.out.println(heap.get(index));

        if (2*index >= heap.size()) return;

        indent += isRight ? "┃    " : "     ";

        print(2*index+1, indent, true, false);
        print(2*index,  indent, false, false);
    }

    //Print wrapper method with no parameters
    public void print() {
        print(1, "", false, true);
        System.out.println();

        System.out.println("Array Storage: " + heap);
        System.out.println("Index 0 is ignored for the heap structure");
    }
}
```
<br>

# Insert
Write a method `insert` in your MaxPQ class to insert a new key into your heap, maintaining the heap structure. Consider writing a `siftUp` method first (also known as swim) which takes in an index and swaps that element with its parent until it is less than its parent. Remember that the parent of index `k` can be accessed by index `k/2`, and its children can be accessed by `2k` and `2k+1`. Also remember that index 0 is ignored, and not considered as an element.

<details>
<summary>Click to reveal solution</summary>

## Solution
We will begin by implementing a `siftUp` method. We take in the starting index of our siftUp process, and then compare the element to its parent. If it is greater than its parent, we can simply swap them, update our current index, and repeat the process. If the element ends up at index 1, it is the max element and we are done. 

```java
//Sifts up index i until it is less than its parent
public void siftUp(int i) {
    //Keep going until i possibly reaches 1
    while (i > 1) {
        //If i > parent, swap them and update i to its parent
        if (heap.get(i) > heap.get(i/2)) {
            Collections.swap(heap, i, i/2);
            i /= 2;
        }
            
        //Otherwise we're done, and i is in the right place
        else break;
    }
}
```

Now that we have a `siftUp` method to fix the heap structure after insertion of an element, our `insert` method is very simple. We can just add the element to the end of our ArrayList, and then sift it up to maintain heap structure.  

```java
//Inserts an element into the heap
public void insert(int n) {
    //Add the element to the end and sift it up
    heap.add(n);
    siftUp(heap.size()-1);
}
```
If n is the number of elements in the heap, the height of the heap is on the order of O(logn), since the heap tree structure is complete. Thus, at worst you will have to sift up O(logn) times, meaning that insert is an O(logn) operation. 
</details>
<br>

# Delete Max
Write a method `deleteMax` in your MaxPQ class to delete the current max element of the heap (always stored at index 1), fix the heap structure, and return the deleted element. Consider writing a `siftDown` method first (also known as sink) which takes in an index and swaps that element with its greater child until it is greater than both its children. 

<details>
<summary>Click to reveal solution</summary>

## Solution
We will begin by implementing a `siftDown` method. We take in the starting index of our siftDown process, compare both its children, compare the current element to its greater child, and swap them if it is less. We can then update the current index and repeat the process. If we reach an element with no children, we are done. 

```java
//Sifts down index i until it is greater than both children
public void siftDown(int i) {
    //Keep going until there are no children to check
    while (i*2 < heap.size()) {
        int newI; //Will store the greater child

        //If no right child or left child is bigger, newI is left child, otherwise right child
        if (1+i*2 == heap.size() || heap.get(i*2) > heap.get(1+i*2)) newI = i*2;
        else newI = 1+i*2;
            
        //If i is less than its child, swap them and update i
        if (heap.get(i) < heap.get(newI)) {
            Collections.swap(heap, i, newI);
            i = newI;
        }

        //Otherwise i is in the right spot, we're done
        else break;
    }
}
```
Now that we have a `siftDown` method to fix the heap structure, our `deleteMax` method is very simple. We can store the element at index 1 to return at the end, swap it with the last element, delete the element to be returned from the heap, and then sift down the element at the top of the heap to a valid location.

```java
//Deletes and returns the max element in the heap
public int deleteMax() {
    int mx = heap.get(1); //We know the max is stored at index 1

    //Swap the last value in, remove the current max from the heap
    Collections.swap(heap, 1, heap.size()-1);
    heap.remove(heap.size()-1);

    //Sift down the previous last value, and return the max
    siftDown(1);
    return mx;
}
```
If n is the number of elements in the heap, the height is on the order of O(logn), since the heap tree structure is complete. Thus, at worst the replaced element will be sifted down O(logn) times, meaning that deleteMax is an O(logn) operation.
</details>
<br>

# Heapify
Write a method `heapify` in your MaxPQ class which modifies the currently stored ArrayList (may not yet be a valid max heap) into a valid max heap. The most efficient way to achieve this is to start at the end of the stored ArrayList and sift each element down to its proper location, working your way to the beginning. It will be explained rigorously why this is the most efficient method in the solution. You may use the provided constructor which takes in an preexisting ArrayList to test your method.

<details>
<summary>Click to reveal solution</summary>

## Solution
Elements which do not have children do not need to be sifted down, so we can start at the parent of the last element. Then, we can simply call `siftDown` for every index from that element down to index 1, ensuring that the heap structure is satisfied at the end.

```java
//Converts the current arraylist to a valid heap
public void heapify() {
    //Start at the parent of the last element
    int i = (heap.size() - 1) / 2; 

    //Sift down and decrement i until i = 0
    while (i >= 1) {
        siftDown(i);
        i--;
    }
}
```
Surprisingly, when the operations are performed in this order, the `heapify` method runs in O(n) time, rather than the expected O(nlogn)! While other ways of transforming an array into a heap (such as sifting up rather than down) result in O(nlogn) time, this method is bounded by O(n). For the purposes of the course, all you need to know is that heapify is an O(n) process. However, I have provided a derivation of this runtime (requires Calculus 2 knowledge).

![](https://i.imgur.com/j9qBMJM.jpeg)
</details>
<br>

# Top K
Write a method `topK` which takes in an ArrayList of Integers and an int k, and uses a heap object to help efficiently return the top k elements in the given ArrayList.

<details>
<summary>Click to reveal solution</summary>

We can initialize a new MaxPQ object taking in the given ArrayList, which sets the MaxPQ's heap to the ArrayList we are interested in. We can then call heapify to transform it into a heap, and call deleteMax() k times in order to extract the top k elements. 

```java
//Returns the top k elements of the given arraylist
public static ArrayList<Integer> topK(ArrayList<Integer> a, int k) {
    //Initialize a new heap with the arraylist, and heapify it
    MaxPQ pq = new MaxPQ(a);
    pq.heapify();

    //Sol will be used to store the top k elements
    ArrayList<Integer> sol = new ArrayList<Integer>();

    //Simply deleteMax() k times 
    for (int i = 0; i < k; i++) sol.add(pq.deleteMax());
    
    //Return our final arraylist
    return sol;
}
```
As addressed in the heapify problem, our `heapify` method runs in O(n) time, where n is the size of the given ArrayList. Each time we call deleteMax(), we are doing an O(logn) operation. Thus, our total runtime is O(n + klogn). Notice that this is better than simply sorting the ArrayList and iterating through the top k elements, which is O(nlogn + k) time since n is always greater than or equal to k, and potentially much larger.
</details>
<br>

# Min Heap Trace
Simulate on your own inserting the elements `[5, 1, 19, 25, 17, 21, 5, 19, 20, 9, 15, 14]` into a MIN heap. Then verify whether the heap structure and final array structure you arrived at is accurate using your MaxPQ class. Remember that by negating numbers before inserting them, you can cause your max heap to function as a min heap. Also notice that duplicate numbers in heaps are totally fine. 

No solution for this one! Check it with your program!
