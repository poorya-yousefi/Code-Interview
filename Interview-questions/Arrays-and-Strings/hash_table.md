# Hash Table (Hash Map)

is a data structure that maps keys to values for highly efficient lookup. A hash table uses a hash function to compute an index into an array of buckets or slots, from which the desired value can be found.

> A hash table is just an array of linked lists. Each linked list holds all the items in the table that share the same hash code. Initially, all the lists are empty (represented as null in the array).

## Direct Address Table

> As we know length of RAM is finite, If length of map's array equals to input's length, then it's a *Direct Address Table*.

## Hash Function

A hash function is any function that can be used to map data of arbitrary size to fixed-size values. The values returned by a hash function are called hash values, hash codes, digests, or simply hashes.

## Sample Hash Functions

Here are some pseudocode examples of simple hash functions:

### Division Method

```plaintext
function hash(key, table_size):
    return key % table_size
```

### Multiplication Method

The benefits of the Multiplication Method include:

- It tends to distribute keys more uniformly than the Division Method.
- It is less sensitive to the choice of table size.
- It can be more efficient on some hardware due to the use of multiplication and bitwise operations.
- It avoids certain patterns that can occur with the Division Method, leading to fewer collisions in practice.
- It is simple to implement and understand.

```plaintext
function hash(key, table_size):
    A = (sqrt(5) - 1) / 2 // A = 0.6180339887...
    return floor(table_size * (key * A % 1))
```

## Hash Collision

A hash collision is when a hash function returns the same output for two distinct elements.

## Types of Hash Table

- Open Hashing(Closed Addressing or Separate Chaining)
  > In open addressing, all elements are stored in the hash table itself. In separate chaining, each bucket is independent, and has some sort of list of entries with the same index.
- Closed Hashing(Open Addressing)
  > In closed addressing, all elements are stored in the hash table itself.

## Instructions

### Insert

To insert kay and value as element in hash table, we need to follow these steps:

1- First, compute the key's hash code, which will usually be an int or long. Note that two different keys
could have the same hash code, as there may be an infinite number of keys and a finite number of integers.
2- Then, map the hash code to an index in the array. This could be done with something like hash (key)
% array_length. Two different hash codes could, of course, map to the same index.
3- At this index, there is a linked list of keys and values. Store the key and value in this index. We must use a
linked list because of collisions: you could have two different keys with the same hash code, or two different
hash codes that map to the same index.

## Hash Table Complexity

> If the number of collisions is very high, the worst case runtime is `O(N)`, where N is the number of keys.
However, we generally assume a good implementation that keeps collisions to a minimum, in which case
the lookup time is `O(1)`.

- **Space complexity**: O(n + k)
  > where `n` is the number of keys(Elements) and `k` is the number of buckets(table_size).
- **Search complexity**: O(1)
- **Insert complexity**: O(1)
- **Delete complexity**: O(1)

## Hash Table vs Array

- **Hash Table**: Used when we have a set of keys to map to values.
- **Array**: Used when we have a set of integer keys.

## Alternate Implementation Structure

### Balanced Binary Search Tree

Alternatively, we can implement the hash table with a `balanced binary search` tree. This gives us an `O(log N)`
lookup time. The advantage of this is potentially using less space, since we no longer allocate a large array. We
can also iterate through the keys in order, which can be useful sometimes.
