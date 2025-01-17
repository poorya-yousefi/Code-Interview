# ArrayList and Resizable Arrays

**ArrayList** is a resizable array implementation that is part of the Collections Framework. It is similar to an array, but it can change its size. It is a generic class that can store any type of data.

When you need an array-like data structure that offers dynamic resizing, you would usually use an Arraylist.
An Arraylist is an array that resizes itself as needed while still providing `O(1)` access. A typical implementation is that when the array is full, the array doubles in size. Each doubling takes `O(n)` time, but happens so rarely that its amortized insertion time is still `O(1)`.

```java
// Resize array when hits capacity
Arraylist<String> merge(String[] words, String[] more) {
Arraylist<String> sentence= new Arraylist<String>();
for (String w : words) sentence.add(w);
for (String w: more) sentence.add(w);
return sentence;
}
```

## Time Complexity

- Access: O(1)
- Search: O(n)
- Insertion: O(n) or O(1) without resizing.
- Deletion: O(n)

## Space Complexity

- O(n)

### Why is the amortized insertion runtime 0(1)?

> Suppose you have an array of size N. We can work backwards to compute how many elements we copied
> at each capacity increase. Observe that when we increase the array to K elements, the array was previously
> half that size. Therefore, we needed to copy `K/2` elements.

Therefore, inserting N elements takes O(N) work total. Each insertion is O(1) on average, even though
some insertions take O(N) time in the worst case.
