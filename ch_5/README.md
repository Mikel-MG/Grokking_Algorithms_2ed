In previous chapters, we have seen that finding an element in an array with *linear search* takes $O(n)$ time; this means that the time it takes scales linearly with the size of the array. *Binary search* scales much better, taking $O(\log n)$ time. Could we make the access to the elements of an array independent of the size of the array? In other words, in *constant time*, $O(1)$?

# Hash functions

A hash function is a function which takes some data and returns a number; in other words, it *maps* data, for example strings, to numbers. Hash functions need to fulfill the following requirements:
* they need to be **consistent** $\rightarrow$ a given input will **always** return the same output
* they should map different inputs to different numbers. In the best case, every different word should map to a different number.

# Hash tables

We can use such hash functions to create *hash tables*. This data structure consists essentially of an array, which will store the values, and a hash function, which will map each 'key' of our table to its location in the array. The hash function removes the need for searching for the value, since the mapping is *calculated* from the given key.

***
**Example**

In this pseudocode example, we implement a hash table to store a value (5) associated with a key ('apple')
```
array = [ | | | | | | | ]
memory_address = hash('apple') -> returns 0
array[memory_address] = 3.5
array[hash('apple')] -> returns 3.5!
```
***

This works because
* the hash function consistently maps a name to an index
* the hash function maps different strings to different indexes
* the hash function only returns valid indexes within the given array

Hash tables are among the most useful complex data structures. They are also known as *hash maps*, *maps*, *dictionaries*, and *associative arrays*. Since hash functions remove the need to search for items, hash tables are very fast.

> Note: The kind of 1-to-1 mapping we have described is called *injective function*. In reality, most implementations of hash maps involve some level of overlap, technically known as *collisions*. A discussion on these can be found further below in this chapter.

Many programming languages include an implementation of hash tables; in Python, they are known as *dictionaries*


```python
# instantiate a new, empty hash table
hash_table = {}

# add a mapping
hash_table['apple'] = 5

# retrieve a value
price = hash_table['apple']
print(f"The price of apples is {price}!")
```

    The price of apples is 5!


Let us check whether hash tables are really faster than plain arrays in the task of checking whether they contain a given element


```python
# let us create a random array (list), and a random dictionary (same values, mapped to range(0,N)
import random
N = 100000
random_array = [random.randint(0, N*10) for i in range(N)]
last_element = random_array[-1]
print(last_element)
```

    932580



```python
random_dict = dict(zip(random_array, range(N)))
```


```python
%%timeit
last_element in random_array
```

    508 μs ± 43.6 μs per loop (mean ± std. dev. of 7 runs, 1,000 loops each)



```python
%%timeit
last_element in random_dict
```

    20.9 ns ± 0.556 ns per loop (mean ± std. dev. of 7 runs, 10,000,000 loops each)


The hash table is much faster for sparse data accession!

# Use cases

## Using hash tables for lookups

Hash tables can be used to store data associated with a key; this behaviour can be leveraged to model relationships from one item to another, as well as to store data in a look-up table.

## Preventing duplicate entries

A hash table can instantly check whether a key has an associated value or not, which can be used to check for duplicate values very fast.

## Using hash tables as cache

Hash tables can be used to store information or results that have already been generated or are frequently requested. This helps speed up computations and reduces the amount of work a program needs to do.

# Collisions

In previous sections we have said that "*the hash function maps different strings to different indexes*". In reality, it is almost impossible to generate a hash function that does this. If a hash function maps two (or many) inputs to one memory address, a naive implementation of a hash table will overwrite the previously saved mapping! This is called a *collision*.

There are several ways to deal with this. If multiple keys map to the same slot, the simplest thing to do is to start a linked list at that slot, which will hold all the key-value pairs. Accessing the particular linked list is instantaneous, thanks to the top-level hash table, and accessing a specific element within the linked list is fast *enough*, provided the linked list is *relatively* short.

For example, if our hash function would map every value to the same slot, the hash table would essentially become a linked list with extra steps, and would be as slow as a regular linked list. This highlights the following:
* The hash function is really important. Ideally, it will map keys evenly all over the hash
* If the linked list gets too long, it will slow down the hash table a lot. This is why we need a good hash function!

# Performance

Hash tables take $O(1)$ for search, insert, and delete operations. This does not mean it is instantaneous, it means that the time it takes for these operations will stay the same, no matter how large the hash table is. In the average case, hash tables are really fast ($O(1)$). In the worst case, a hash table takes $O(n)$ --linear time-- for each of these operations, which is really slow (see the table below).


|            | Hash tables<br>(average) | Hash tables<br>(worst) | Arrays | Linked list |
|------------|------------|------------|------------|------------|
| Search) |  $O(1)$  | $O(n)$ | $O(1$) | $O(n)$ |
| Insert  |  $O(1)$  | $O(n)$ | $O(n)$ | $O(1$) |
| Delete  |  $O(1)$  | $O(n)$ | $O(n)$ | $O(1$) |





On average, hash tables are as fast as arrays at searching (getting a value at an index), and as fast as linked lists at inserts and deletes. In the worst case though, hash tables are slow at all these operations. It is important to avoid hitting worst-case performance with hash tables, and for this, we need to avoid collisions. This means
* A low load factor
* A good has function

## Load factor

The load factor of a hash table measures how *full* it is. 

$$\text{Load factor} = \frac{\text{Number of items in hash table}}{\text{Total number of slots}}$$

For example,
* if our hash table has 100 slots, and only 50 of them are filled, the load factor will be $\frac{1}{2}$ or 0.5.
* if our hash table has 100 slots, and it stores 150 items, the load factor will be $\frac{3}{2}$ or 1.5.

Having a load factor greater than 1 means we have more items than slots in our array, and the larger it is, the larger the number of *collisions*.

Once the load factor starts to grow, we will need to add more slots to our hash table. This is called *resizing*, and it can be a costly operation.

The computational expense of resizing is due to the fact that it entails creating a new array (typically, double the size of the original one), and then reinserting all the items into the new hash table using the hash function. This will reduce the load factor by a factor of 2. A good implementation of a hash table will balance the cost of resizing with the gained performance from reducing the load factor.

As a rule of thumb, when the load factor is greater than 0.7, the hash table should be resized.

## A good hash function

A good hash function distributes values in the array evenly. A bad hash function groups values together and produces a lot of collisions. We will typically never have have to worry about implementing our own.

For example, Google's Abseil library uses CitiHash. It is an open source C++ library based on internal Google code. It proves all kinds of general-purpose C++ functions, and it can be used as a hash function.

# Exercises

**Which of these hash functions are consistent?**

**5.1)** $f(x) = 1$

**Answer**: It is consistent. It is not useful, because it will map every value to 1, but, effectively, it is consistent.

**5.2)** $f(x) =$ random.random()

**Answer**: It is not consistent, and repeated calls with the same input will return different values

**5.3)** $f(x) =$ next_empty_slot() `# returns the index of the next empty slot in the hash table`

**Answer**: It is not consistent, as it will return the same value until we add an additional element to the hash table

**5.4)** $f(x) =$ len(x) `# returns the length of the input string`

**Answer**: It is consistent, as it will be invariant for a given input. It is not a great hash function, and we can expect a fair amount of collissions.

It’s important for hash functions to have a good distribution.
They should map items as broadly as possible. The worst case is
a hash function that maps all items to the same slot in the
hash table.

Suppose you have these four hash functions that work with strings:
1. Return “1” for all input.
2. Use the length of the string as the index.
3. Use the first character of the string as the index. So, all strings starting with a are hashed together, and so on.
4. Map every letter to a prime number: a = 2, b = 3, c = 5, d = 7, e = 11, and so on. For a string, the hash function is the sum of all the characters modulo the size of the hash. For example, if your hash size is 10, and the string is “bag,” the index is (3 + 2 + 17) % 10 = 22 % 10 = 2.

For each of these examples, which hash functions would provide a good distribution? Assume a hash table size of 10 slots.

**5.5)** A phonebook where the keys are names and values are phone
numbers. The names are as follows: Esther, Ben, Bob, and Dan.

**Answer**: 4) seems the best option, given that the other three would incur into larger amount of collision.

**5.6)** A mapping from battery size to power. The sizes are A, AA, AAA,
and AAAA.

**Answer**: In this particular case, 2) and 4) would be best, as all the strings have different lengths. 1) and 3) would be obviously bad.

**5.7)** A mapping from book titles to authors. The titles are Maus, Fun Home, and Watchmen.

**Answer**: 3) and 4) would be good options, but not 2), given 'Fun Home' and 'Watchmen' have the same number of letters. 
