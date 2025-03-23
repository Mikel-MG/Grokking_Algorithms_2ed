# Recursion

Suppose you want to find a key. The key is contained within a box, which itself is somewhere within a set of nested boxes. Each *level* of boxes contains more than one box. What do?

An algorithm to find the key could be something like:
1. Make a pile of boxes to look through.
2. Grab a box and look through it.
3. If you find a box, add it to the pile to look through later.
4. If you find a key, you are done!
5. Repeat.

Here is an alternate approach:
1. Look through the box
2. If you find a box, go to step 1.
3. If you find a key, you are done!

The first approach uses a `while` loop, as seen in the following pseudocode

```python
def look_for_key(main_box):
    pile = main_box.extract_pile()
    while pile is not empty:
        box = pile.grab_box()
        for item in box:
            if item.is_box():
                pile.append(item)
            elif item.is_key():
                return item
```

The second way uses recursion. *Recursion* is when a function calls itself, such as 

```python
def look_for_key(box):
    for item in box:
        if item.is_box():
            look_for_key(item)
        elif item.is_key():
            return item
```

Both approaches accomplish the same thing, but the second aproach is **conceptually** more clear and elegant. Recursion should be used when it makes the solution clearer, as there is no performance benefit from using it; in fact, loops can be often better for performance.

# Base case and recursive case

Since a recursive function calls itself, it is easy to write a function incorrectly and fall into an unintended infinite loop. For example, suppose you want to write a function that prints a countdown like this: 3, 2, 1.

If we only define the change per recursion level and neglect defining some kind of *stop condition*, the program will fall into an endless loop

```python
# for safety reasons, I will limit the allowed recursion limit
import sys
sys.setrecursionlimit(35)

def countdown(N):
    print(N)
    countdown(N-1)

countdown(3)
```

This will return

```
3
2
1
0
-1
-2
-3
-4
-5
-6
Unexpected exception formatting exception. Falling back to standard exception
Traceback (most recent call last):
 ...
RecursionError: maximum recursion depth exceeded
```

When you write a recursive function, you have to tell it when to stop recursing. This is why every recursion function has two parts: the `base case` and the `recursive case`. The recursive case is when the function calls itself, and the base case when the function does not call itself again. Let us add this part to the previous code


```python
def countdown(n):
    print(n)
    if n <= 1:
        # this is our base case
        return
    else:
        # this is the recursive case
        countdown(n-1)

countdown(3)
```

    3
    2
    1


# The stack

The call stack is an important concept in general programming, and it is particularly relevant to understand when using recursion. A stack can be understood fundamentally as a list with two possible actions, *push* (insert at the top) and *pop* (remove from top and read). Such a data structure is used, for example, in the *call stack* of computers, as illustrated in the code below.


```python
# here is a function that prints a message and calls two functions
def top_func(name):
    print(f"Hello {name}, this is the top function")
    
    print("Calling bot_func1...")
    bot_func1(name)

    print("Calling bot_func2...")
    bot_func2(name)

    print("Top function reporting, goodbye!")

# here are the two *called* functions
def bot_func1(name):
    print(f"Hi {name} this is bot_func1!")

def bot_func2(name):
    print(f"Howdy {name} this is bot_func2!")

top_func("Mikel")
```

    Hello Mikel, this is the top function
    Calling bot_func1...
    Hi Mikel this is bot_func1!
    Calling bot_func2...
    Howdy Mikel this is bot_func2!
    Top function reporting, goodbye!


This process can be rationalized in terms of the *call stack*.

1) `top_func` is called with the parameter `name`. The computer allocates memory for this function and its parameter(s), and the call stack is *initialized*.
2) `top_func` greets the user and calls `bot_func1` with the `name` parameter. Here, the calling function (`top_func`) is paused in a partially completed state, and the stack is extended with the called function (`bot_func1`).
3) `bot_func1` greets the user, and upon finishing its execution, it is removed from the top of the stack. Execution is returned to where we left it off (`top_func`).
4) `top_func` calls `bot_func2`. The execution of the calling function (`top_func`) is paused again, and the stack (whose size is equivalent to step 1) is extended with the called function (`bot_func2`).
5) `bot_func2` greets the user, and upon finishing its execution, it is removed from the top of the stack. Execution is returned to `top_func`.
6) `top_func` bids goodbye to the user, and terminates execution.

In terms of *call stack size*, each step would be something like

1) 0 $\rightarrow$ 1
2) 1 $\rightarrow$ 2
3) 2 $\rightarrow$ 1
4) 1 $\rightarrow$ 2
5) 2 $\rightarrow$ 1
6) 1 $\rightarrow$ 0

# The call stack in the context of recursion

The factorial is a mathematical operation which can be defined as the product of all integer numbers between 1 and N, and it is written as $N!$.

$5! = 1 \times 2 \times 3 \times 4 \times 5 = 120$

$ 3! = 1 \times 2 \times 3 = 6$

This kind of process lends itself very well to be implemented using recursion.


```python
def factorial(N):
    if N == 1:
        return 1
    else:
        return N * factorial(N-1)

factorial(5)
```




    120



It works! Magic. To better understand what is going on with the call stack, we can use a special function to visualize it during this program's execution.


```python
import traceback

def show_stack_summary():
    stack = traceback.format_stack()
    for line in stack:
        if 'factorial' in line:
            print(line)
    print()

def factorial(N):
    # debug code
    print(f"{'*'*40}")
    print(f"We called factorial({N})")
    print(f"Here is the call stack")
    print(f"{'*'*40}")
    show_stack_summary()
    
    if N == 1:
        return 1
    else:
        return N * factorial(N-1)

factorial(5)
```

    ****************************************
    We called factorial(5)
    Here is the call stack
    ****************************************
      File "/tmp/ipykernel_30508/4091275083.py", line 23, in <module>
        factorial(5)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 16, in factorial
        show_stack_summary()
    
    
    ****************************************
    We called factorial(4)
    Here is the call stack
    ****************************************
      File "/tmp/ipykernel_30508/4091275083.py", line 23, in <module>
        factorial(5)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 16, in factorial
        show_stack_summary()
    
    
    ****************************************
    We called factorial(3)
    Here is the call stack
    ****************************************
      File "/tmp/ipykernel_30508/4091275083.py", line 23, in <module>
        factorial(5)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 16, in factorial
        show_stack_summary()
    
    
    ****************************************
    We called factorial(2)
    Here is the call stack
    ****************************************
      File "/tmp/ipykernel_30508/4091275083.py", line 23, in <module>
        factorial(5)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 16, in factorial
        show_stack_summary()
    
    
    ****************************************
    We called factorial(1)
    Here is the call stack
    ****************************************
      File "/tmp/ipykernel_30508/4091275083.py", line 23, in <module>
        factorial(5)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 21, in factorial
        return N * factorial(N-1)
    
      File "/tmp/ipykernel_30508/4091275083.py", line 16, in factorial
        show_stack_summary()
    
    





    120



Note how each call to `factorial()` adds an item to the call stack (in reality, several items are added, but I have trimmed the output for simplicity), except `factorial(1)`, which directly returns 1. More generally, the *base case* will not make an additional recursive call, and will instead *typically* return a preset value.

Although we did not show this, one can imagine how each recursive call makes the call stack taller, up until the base case is reached. Then, `factorial(1)` will return 1 and be removed from the stack, `factorial(2)` will return 2 and be removed from the stack, and so on.

In summary, recursive functions will add items to the call stack until the base case is reached, and then they will terminate (and return their corresponding values) one by one, from most recent call to oldest.

Finally, let us reflect on the differences between the *non-recursive* (while loop) and the *recursive* implementations of our key-searching algorithm (check beginning of chapter). While the loop required a list to store the boxes to be checked, the recursive solution did not. How is this possible? Well, the list was implicitly included in the call stack! While using the stack is convenient, we have to keep in mind that each function in the stack takes some memory, and if our program goes into a very deep recursion, the call stack will be very tall and it will require too much memory.

At this point, we have two options:
* We could rewrite the code to use a loop instead of recursion
* We could use something called *tail recursion*. This is an advanced recursion topic that is out of the scope of this book, and it is only supported by some programming languages.

# Exercises

**3.1** Suppose you see a call stack like
```
Bot_func1(name='Mikel')
Top_function(name='Mikel')
```
What can you say, just based on this call stack?

**Answer**: The running program initially called `Top_function` with parameter `name="Mikel"`. Then, that function called `Bot_func1` with the same parameter. Currently, the program is executing `Bot_func1`.

**3.2** Suppose you accidentally write a recursive function that runs forever. As you saw, your computer allocates memory on the stack for each function call. What happens to the stack when your recursive function runs forever?


**Answer**: The memory will be slowly consumed until it runs out, and then the program will end with an error or the computer will freeze.
