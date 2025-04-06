# Your first tree

Tree terminology
* *Trees* are a type of *graph*, and just like these, are made of *nodes* and *edges* (check chapter 6).
* Nodes can have children (*out neighbors*), and child nodes can have a parent (*in neighbor*).
* In a tree, nodes have at most one parent, and the only node with no parents is the *root*.
* In this book, we will work with *rooted trees*, this is, trees that have one node that leads to all the other nodes.
* Nodes with no children are called *leaf nodes*.

## File directories

Since a tree is a type of graph, we can run graph algorithms on it, such as *breadth-first search* (check chapter 6). Consider a file directory as a tree; we can use breadth-first search to *traverse* the tree and visit all of the nodes. We can then make use of this traversal for printing the name of each file and folder. This could be used to implement our own `ls` command!

The idea is as follows:
```
1. Visit every node in the tree
2. If this node is a file, print out its name
3. If the node is a folder, add it to a queue of folders to search for files
```

The following code is reminiscent of our breadth-first search implementation from the previous chapter


```python
from os import listdir
from os.path import isfile, join
from collections import deque

def list_files_and_folders(start_dir):
    """
    This is a breadth-first implementation
    """
    # initialization
    search_queue = deque()
    search_queue.append(start_dir)

    # traversal through the tree
    while search_queue:
        # we extract an 'target' directory to the search list
        target_dir = search_queue.popleft()
        # we go through every item in the target directory
        for file in sorted(listdir(target_dir)):
            full_path = join(target_dir, file)
            # if it is a file, print it
            if isfile(full_path):
                print(full_path)
            # if not, add it to the search list
            else:
                search_queue.append(full_path)
```


```python
list_files_and_folders('./test_dir/')
```

    ./test_dir/folder_a/file_1.txt
    ./test_dir/folder_a/file_2.png
    ./test_dir/folder_a/file_3.mp3
    ./test_dir/folder_b/data.txt
    ./test_dir/folder_a/folder_a1/ultrasecret_file.txt
    ./test_dir/folder_b/folder_b1/secret_file
    ./test_dir/folder_b/folder_b2/public_file


There are two key differences with the implementation in chapter 6:
* Are not using the algorithm to find a path, we just want to traverse the whole tree. Thus, there is no 'match' to be made.
* Since trees cannot have cycles, and each node only has one parent, we do not have to keep track of which folders we have already searched. By definition, we will only visit each folder just once. This has made the code easier. **Keypoint**: trees do not have cycles!

> **A note on symbolic links**
> 
> Symbolic links can be created in Unix-like system with `ln -s TARGET DIRECTORY`. This is a way of introducing a cycle in a file directory. Consider, for example, `ln -s test_dir/folder_a test_dir/folder_a/folder/folder_a`. Effectively, this means that now there is a link from within folder_a that points to folder_a, in other words, a cycle!. Now our file directory is not a tree anymore. If we tried the approach above in this scenario, Python would throw a `OSError: Too many levels of symbolic links` error.

# A space odyssey: depth-first search

Let us now traverse our file directory, but recursively (depth-first)


```python
from os import listdir
from os.path import isfile, join

def list_files_and_folders(start_dir):
    """
    This is a depth-first implementation
    """
    # no need for initialization
  
    # traversal through the tree
    for file in sorted(listdir(start_dir)):
        full_path = join(start_dir, file)
        # if it is a file, print it
        if isfile(full_path):
            print(full_path)
        # if not, start recursion
        else:
            list_files_and_folders(full_path)
```


```python
list_files_and_folders('./test_dir/')
```

    ./test_dir/folder_a/file_1.txt
    ./test_dir/folder_a/file_2.png
    ./test_dir/folder_a/file_3.mp3
    ./test_dir/folder_a/folder_a1/ultrasecret_file.txt
    ./test_dir/folder_b/data.txt
    ./test_dir/folder_b/folder_b1/secret_file
    ./test_dir/folder_b/folder_b2/public_file


For the depth-first search implementation we do not require a queue, since each folder is searched to the bottom soon as it is visited. Because of this, the order of the printed files is different!

For reference, this is the result of the breadth-first search implemented above
```
./test_dir/folder_a/file_1.txt
./test_dir/folder_a/file_2.png
./test_dir/folder_a/file_3.mp3
./test_dir/folder_b/data.txt
./test_dir/folder_a/folder_a1/ultrasecret_file.txt
./test_dir/folder_b/folder_b1/secret_file
./test_dir/folder_b/folder_b2/public_file
```

Breadth-first search and depth-first search are closely related, and often are mentioned together. A fundamental difference is that, even if both are methods of traversing trees, **depth-first cannot be used for finding the shortest path**. This is due to the fact that it does not *prioritize* minimum depth when looking for items. Depth-first search has other uses, such as to find the topological sort (check chapter 6).

## A better definition of trees

Now that we have seen some examples, we can come up with a better definition for trees. A tree is a *connected, acyclic graph*. A tree must be
* connected $\rightarrow$ there exists a path from every node to every other node
* acyclic $\rightarrow$ there are no loops such that a node can be visited twice while traversing the graph
* graph $\rightarrow$ a data structure made out of nodes and the edges that connect them

In our particular case, the tree is also
* rooted $\rightarrow$ a root node is the only one that has no parent node

# Binary trees

Computer science is full of different types of trees. One of the most common ones is the *binary tree*. A binary tree is a special type of tree where nodes can have at most two children (hence the binary nomenclature). These are traditionally called left child and right child.

```
   O
  / \
 O   O
/ \ / \
L R L R
```
L $\rightarrow$ Left child<br>
R $\rightarrow$ Right child

An ancestry tree is an example of a binary tree since everyone has two biological parents. Although in this case the connection between the nodes has a very specific meaning (they are all family), the data can be totally arbitrary.

The important thing is that every node has at most two children. Sometimes people refer to the left subtree or to the right subtree (this is, left- and right- children and all their children).

# Huffman coding

Huffman coding is a neat example of an application of binary trees, and it is the foundation for text compression algorithms. In this section we will not describe the algorithm in detail, but we will explore how it works and how it makes clever use of trees.

In order to understand how compression works, we first need to know how much space a text file takes. Suppose we have a text file with just one word: *tilt*. How much space does that use?


```python
# let us write a file with UTF-8 encoding
with open("test.txt", "w", encoding="utf-8") as file:
    file.write("tilt")
```


```python
# let us check how much space does the file take in disk
! stat --format="%s" test.txt 
```

    4


Assuming we are using ISO-8859-1 (see below), each letter takes exactly 1 byte (8 bits). For example, the letter `a` is ISO-8859-1 code `97`, which can be written in binary as `01100001`. There are 256 possible combinations of 0s and 1s with 8 bits, thus 256 possible letters in ISO-8859-1 code.

> **Character encoding**
>
> There are many different ways to encode characters; this means that there are many different ways to write the letter *a* in binary.
>
> It started with ASCII in the 1960s. ASCII is a 7-bit encoding, and because of this, it does not include many characters. For example, umlauts like ü or ö are missing, as are common currencies such as the British pound or Japanese yen.
>
> Thus, ISO-8859-1 was created. ISO-8859-1 is an 8-bit encoding, so it doubles the number of characters that ASCII provided. It went from 128 possible characters to 256. This was deemed not to be enough, and soon after several countries started making their own encodings. For example, Japan has several encodings for Japanese since ISO-8859-1 ans ASCII were focused on European languages. This lead to a messy situation and a number of incompatibilities, until Unicode was introduced.
>
> Unicode is an encoding standard, as it aims to provide characters for any language. It has 129,186 characters as of version 15; more than 1000 of these are emojis. Although it is a standard, we need to use an encoding that follows this standard. The most popular today is UTF-8. UTF-8 is a variable-length character encoding, which means that characters can be encoded with anywhere from 1 to 4 bytes (8-32 bits). UTF-8 is the most common Unicode encoding.
>
> Takeaways:
> * Compression algorithms try to reduce the number of bits needed to store each character.
> * If you need to pick an encoding for a project, UTF-8 is a good default choice. 


Let us decode a binary number to ISO-8859-1: 011100100110000101100100

First, since we know each letter is 8 bits (see above), we can divide the string in 8-bit chunks<br>
01110010 01100001 01100100

Alright, we have three letters. Converting to decimal values, these would be<br>
01110010 $\rightarrow$ 114<br>
01100001 $\rightarrow$ 97<br>
01100100 $\rightarrow$ 100<br>

Checking a ISO-8859-1 [code table](https://www.w3schools.com/CHARSETS/ref_html_8859.asp), we see that these values map to: `r a d`. A text editor takes the binary data in a text file and displays it as ISO-8859-1. We can view the binary information by using the `xxd` Unix utility.


```python
!xxd -b test.txt
```

    00000000: 01110100 01101001 01101100 01110100                    [1;32mt[0m[1;32mi[0m[1;32ml[0m[1;32mt[0m


Now, here is the trick. For the word *tilt*, we do not need 256 possible letters, we just need three. So instead of using 8 bits, we can only use 2. We could come up with our own 2-bit code just for these three letters
```
t = 00
i = 01
l = 11
```

Thus, we could rewrite *tilt* as follows: `00 01 11 00`, in just 8 bits (a byte)!

But how to come up with an effective mapping? The Huffman coding looks at the characters being used and tries to use less than 8 bits, thus compressing the data. Huffman coding generates a tree which encodes the mapping from binary to letters. In this tree, the left- or right- children nodes specify whether there is a `0` or a `1`. Critically, only leaf nodes (those with no children) encode for characters, while all their parents encode the binary string that they map to. The tree stores characters at different depths, in other words, with varying number of bits.

How do we know how to chunk a given binary string? We do not, and thus we cannot use chunking anymore. The way the algorithm works is by parsing a binary string while traversing the tree. Consider the binary string `0010010`. We would read

(From top root node):<br>
`0` $\rightarrow$ Left (no character)<br>
`00` $\rightarrow$ Left, Left (no character)<br>
`001` $\rightarrow$ Left, Left, Right (character "A")<br>

(continue with the rest of the binary string)

And so on. Although it is more work to do this instead of chunking, the fact that some letters can be encoded with fewer bits is a big benefit. Even more so if we position the most common letters further up the tree, where they can be encoded with shorter binary strings. The Huffman encoding takes advantage of the following properties of trees<br>
* There is no overlap between letters due to the fact that the characters are located only in leaf nodes
* There is only one code for each letter, since there is a unique path from the root to each leaf node
* There is a root node which marks the beginning of the encoding. Graphs do not necessarily have a root node.
* The type of tree used is a *binary tree*, which makes it possible to use binary code.
