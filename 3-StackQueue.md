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