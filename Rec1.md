# Reverse Arrays
Write a method that reverses a given character array, and analyze the space and runtime complexity of your algorithm. Try to find a solution which does not create a new array, but instead modifies the given array in place. 

**Example input:** ["H","a","n","n","a","h"]

**Example output:** ["h","a","n","n","a","H"]

<details>
<summary>Click to reveal solution</summary>
    
A valid but naive solution would be to create a new array, then iterate through the given array and put each element into the new array in reverse order. Finally, copy the new array over to the given array.

```java
public static void reverse(char arr[]) {
    //Note that arr is the array we want to reverse

    int n = arr.length //We will use arr.length a lot

    //Create a new array to copy elements into
    char[] reversed = new char[n];

    //Iterate through arr
    for (int i = 0; i < n; i++) {
        /*The index of the last element is n-1, so we can
        access the ith element from the end with n-1-i*/
        reversed[n-1-i] = arr[i];
    }

    //Copy everything back over from reversed to arr
    for (int i = 0; i < n; i++) arr[i] = reversed[i];
}
```