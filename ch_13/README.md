# Linear regression

Linear regression tries to fit a line to a set of data points, which can be made to interpolate or extrapolate additional values. This is ubiquituous in machine learning, and has many applications.

# Inverted indexes

Inverted indexes are a type of data structure that maps each term in a document collection's vocabulary to a list of documents in which that terms occurs. It is typically implement as a hash map and/or a tree, and it is commonly used to build search engines. The data structure can be leveraged for efficient lookup, scalability, and complex queriying.

# The Fourier transform

The fourier transform is a widely used algorithm in signal processing. It converts a signal from the time (or space) domain into the frequency domain. Instead of describing how a signal changes over time, it describes which frequencies make up the signal and how strong they are.

Some of its most important features are that the signal can be reconstructed using an inverse Fourier transform (reversibility), and that the transform of a sum of signals equals the sum of their transforms (linearity).

Its applications are wide, ranging from audio and image analysis and processing (such as noise reduction), to periodicity detection in biological sequences.

>Recommendation: "An interactive guide to the fourier transform", Better Explained website.

# Parallel algorithms

Modern computers are gradually reaching a plateau with regards to single-core performance, and instead, computing architectures feature an increasingly higher number of core counts. Thus, performance-critical software has to implement parallel-running algorithms in order to be able to benefit from having multiple cores.

For example: we have learned that the best-case scenario of a sorting algorithm is roughly $O(n \log{n})$. However, there is a parallel version of quicksort that will sort an array in $O(n)$ time!

Parallel algorithms are hard to design, and it is difficult to check that they work correctly and to determine what kind of performance improvement they will yield. Also, the time gains are not linear. This means that running a program on two cores instead of one does not result in double the performance. There are several reasons for this:

* *Overhead of managing the parallelism*: Dividing the workload and merging it back together takes some extra computation. If we divide a problem in too many small pieces, the overhead may exceed the performance gains, making the program slower!

* *Amdahl's law*: When optimizing a part of a system (or in this case, an algorithm), the performance gain is limited by how much time that part of the system takes. For example, if we optimize by 50% a part of the algorithm which only takes 5% of the running time, the overall optimization will be of 2.5%!

* *Load balancing*: If not all the distributed tasks take the same amount of time, it is possible that some cores will finish their jobs early and will waste time waiting for the other cores. How can we distribute the work evenly so all cores are working as much as possible?

# map/reduce

*Distributed* algorithms are a special type of parallel algorithm which instead of running *only* in parallel cores, they can also run across multiple computers (each with multiple cores). This is particularly useful for high-performance clusters and large-scale computations.

Google popularized a distributed algorithm that they called MapReduce, but these kinds of functions have been around for longer.

# Bloom filters and HyperLogLog

Hash maps offer a fast look-up-table function ($O(1)$), but when we consider large (VERY large) sets of data, the hash starts getting larger and larger, and it ends up taking up a lot of space.

Bloom filters offer a solution to this issue, in the form of a *probabilistic data structure*. They provide an answer (is an item in a given set?) that is correct *most* of the time.
* False positives are possible: bloom filters may falsely indicate that a given element is within a set.
* False negatives are NOT possible: bloom filters will never fail to indicate the presence of an item in a set.

Bloom filters are great because they take up very little space, instead of having to store every element of a set. In exchange, they provide a correct answer only *most* of the time, which for many large-set applications is a very acceptable trade (large databases of internet content aimed at recommendation systems, for example).

Conversely, the HyperLogLog algorithm approximates the number of unique elements in a set. It will not give an exact answer, but it will come close enough, while using only a fraction of the memory that an exact algorithm would require. HyperLogLog is another example of a probabilistic data structure.

# HTTPS and the Diffie-Hellman key exchange

HTTPS is the backbone of the internet, enabling secure online transactions, from password entry to online purchases. HTTPS works by encrypting messages between the client and the server. This is done by passing a message and a secret key to a function, which then generates an encrypted message. To decrypt the message, we pass the encrypted message and the *same* secret key into another function. The question is, how do we make sure that the browser and the server have the same key? (and that no one else has it, else they could fake their identity!)

The obvious answer would be sending the key together with the message to the server, but that defeats the point of the encryption, since it could be intercepted by a malicious actor! The Diffie-Hellman key exchange algorithm solves this problem in a very clever way.

Step 1: the client and the server generate their own separate keys, independently. These keys are different and unknown to each other. These keys are *private*

Step 2: a common pattern is generated, known to both client and server. This pattern is public, and we do not care who sees it.

Step 3: the public pattern is combined with each of the private keys, independently. This gives a set of two public keys, which are exchanged between client and server, and which anyone can see.

Step 4: the client takes the server's public key (common pattern + server private key) and combines it with the client's private key. The server does the same with the client's public key. In algebraic notation:

```
check_client = server_pub + client_priv
check_client = (server_priv + pattern_pub) + client_priv

check_server = client_pub + server_priv
check_server = (client_priv + pattern_pub) + server_priv
```

if `check_client == check_server`, client and server have a 'shared secret', which is a key they can agree upon, without ever sharing their private keys!


Here are some terms in connection to HTTPS:
* *TLS* (Transport Layer Security) is a protocol to establish secure connections
* *SSL* (Secure Sockets Layer) is the old name for TLS, but people often do not make a distinction. Every version of the SSL protocol that came before the TLS protocol is broken, and should not be used.
* *Symmetric key encryption*: In our example, both client and server used the same key, but there is also *asymmetric key encryption*, where both sides have different keys. HTTPS uses symmetric key encryption though.

Particularly, HTTPS uses a modified version of the Diffie-Hellman key exchange called the *ephemeral Diffie-Hellman key exchange*. It works as illustrated here, except the private keys are generated fresh for every connection. This means that even if an attacked discovers one of the private keys, they can only decrypt the messages from one connection.

>Recommendation: "Real-world cryptography" book by David Wong

# Locality-sensitive hashing (SimHash)

A lot of hash functions are locality insensitive, which means that small alterations in a string will result in a widely different hash. This is often desirable since then an attacker cannot compare hashes to see whether they are close to cracking a password.

However, sometimes is useful to have a locality-sensitive hash function, such as *Simhash*. With it, a small change in a string will result in a small change in the hash. This allows us to compare hashes, which is very useful to detect and analyze similarities and distances between strings.

# Min heaps and priority queues

Min heaps are data structures built using trees. These heaps store numbers, and allow for quick retrieval of the smallest value, since it is always at the root. This is the main use of min heaps, as they can look at the smallest element in $O(1)$ time. We can also remove that element from the heap and put a new min in its place in $O(\log{n})$ time.

We can use this to iteratively extract the minimum value, in order to obtain a sorted list of numbers! This algorithm is called *heapsort*.

Max heaps are similar to min heaps, but now the root is the largest value.

Heaps are great for implementing priority queues (check chapter 6). Queues are a FIFO data structure (First In, First Out), while stacks are LIFO (Last In, First Out). A priority queue is just like a queue, except when we ask for an item, it gives us the item with the highest priority. A to-do list application is a great use case for a priority queue. Priority queues are also used to implement an efficient version of Dijkstra's algorithm.

# Linear programming

Linear programming is used to optimize a function given some linear constraints. For example, consider a company that makes two products, shirts and bags.
* Shirts: Require 1m of fabric and 5 buttons, and sell for \$2
* Bags: Require 2m of fabric and 2 buttons, and sell for \$3.

If we have 11 meters of fabric and 20 buttons, how many shirts and totes should we make to maximize our profit?

All the graph algorithms that we have discussed in this book can be done through linear programming, which is a more general framework. Linear programming uses the Simplex algorithm, which works by iteratively walking along the corners of the feasible region to find the optimal solution.
