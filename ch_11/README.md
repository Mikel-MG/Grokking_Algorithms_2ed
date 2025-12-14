# The knapsack problem (revisited)

Let us come back to the problem that we tackled in the previous chapter. Suppose you are a thief with a knapsack that can carry 4 pounds of goods. What items do you steal to maximize your gain? The items are
* Stereo (3000\$, 4 lbs)
* Laptop (2000\$, 3 lbs)
* Guitar (1500\$, 1 lbs)

## The simple solution

The simplest solution would be to brute-force this problem: calculate the weight and value of each possible sub-set of items, and select the subset with the highest value which is below the weight limit.

The problem with this approach is that computing every possible sub-set is expensive. Performing this computation scales as $O(2^n)$, and quickly becomes prohibitively expensive (check previous chapter). In chapter 10, we came up with a *greedy algorithm* to produce a good approximation of the solution, but what can we do if we **really** care about finding the optimal solution?

## Dynamic programming

Dynamic programming algorithms are a type of algorithm characterized by solving smaller, simpler sub-problems, as a way to build up to solving a bigger problem. We can frame the knapsack problem as follows: if we knew the optimal way to solve the problem for two smaller knapsacks, of 1 lb and 3 lb capacity each, we could simply add these two solutions to come up the optimal way to fill a 4 lb knapsack! (as long as there is no 4 lb optimal item, obviously).

We start the algorithm by visualizing a table of partial solutions. The rows indicate whether we take or leave a given item, and the columns indicate the partial weight limit we are trying to solve. During the course of the algorithm, we will fill the table.

|     |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
| Guitar    |     |     |     |     |
| Stereo    |     |     |     |     |
| Laptop    |     |     |     |     |


**The guitar row**

In this row we will only be concerned with whether to take or not the guitar. The guitar is worth 1500\$ and it weighs 1 lb, so we can fit it in any of the 1-4 lb sized knapsacks. We can indicate this information as follows

|     |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
| Guitar  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$1500<br>G |
| Stereo  |     |     |     |     |
| Laptop  |     |     |     |     |

**The stereo row**

Now we will concern ourselves with whether to steal the stereo and the guitar. We have pre-computed information though: we know what the value of stealing the guitar is.

The stereo is worth 3000 \$ and weighs 4 lb. Thus, for the sub-knapsacks with 1-3 lb limits, we cannot take it; the optimal choice for these will be to steal the guitar instead. For the case of the 4 lb column though, the optimal choice would be to leave the guitar and take the stereo. Let us now populate the table with this information.

|     |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
| Guitar  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$1500<br>G |
| Stereo  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$3000<br>S|
| Laptop  |     |     |     |     |

Notice how we *update* our solution as we traverse into lower rows.

**The laptop row**

The laptop is worth 2000 \$ and weights 3 lb. The two first columns (1 and 2 lb limits) cannot accommodate this weight, so the optimal choice will still be the guitar. For the 3 lb column, we now see that it is more optimal to take the laptop instead of the guitar.

|     |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
| Guitar  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$1500<br>G |
| Stereo  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$3000<br>S|
| Laptop  | \$1500<br>G | \$1500<br>G | \$2000<br>L|     |

The last row of the last column illustrates the genius of dynamic programming. Instead of having to think of all the possible combinations, we have simplified our large problem down to a straightforward comparison: we know that the optimal set of items for weight 3 is the laptop, and that the optimal set of items for weight 1 is the guitar. What is more, the 3000 dollar stereo (fourth column, second ro ) or the 3500 dollar laptop+guitar set?

The answer is: The laptop and the guitar for 3500\$!

|     |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
| Guitar  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$1500<br>G |
| Stereo  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$3000<br>S|
| Laptop  | \$1500<br>G | \$1500<br>G | \$2000<br>L| \$3500<br>LG |

**Code implementation**

Although we have filled the table intuitively, the formula we were using was

```
cell[i_row][j_col] = max of{
    * the previous max (value at cell[i_row-1][j_col]
    * value of current item (row) + value of the remaining space (cell[i_row-1][j_col-item_weight]
    }


```python
def run_DP_algorithm(list_items, max_capacity,
                     item_values, item_weights):

    # dp[i][w] = best value using items[:i+1] with capacity w
    dp = [[0] * (max_capacity + 1) for _ in range(len(list_items))]
    
    # choices[i][w] = best item list achieving dp[i][w]
    choices = [[[] for _ in range(max_capacity + 1)] for _ in range(len(list_items))]
    
    for i, item_name in enumerate(list_items):
        weight = item_weights[item_name]
        value  = item_values[item_name]
        
        for cap in range(1, max_capacity + 1):
    
            # oOption 1: skip the item (default choice)
            if i > 0: # any row after the first
                best_value = dp[i-1][cap]
                best_choice = choices[i-1][cap][:]
            else: # first row (we'll take any item)
                best_value = 0
                best_choice = []
    
            # option 2: include the item (if it fits)
            if weight <= cap:
                include_value = value
                include_choice = [item_name]
    
                if i > 0:
                    include_value += dp[i-1][cap - weight]
                    include_choice = choices[i-1][cap - weight] + [item_name]
    
                if include_value > best_value:
                    best_value = include_value
                    best_choice = include_choice
    
            # store state
            dp[i][cap] = best_value
            choices[i][cap] = best_choice
            
    # print DP table
    print("DP Table (values):")
    for row in dp:
        print(row)
    
    print("\nChoices Table (items at each state):")
    for row in choices:
        print(row)
    
    # final answer
    print("\nOptimal value =", dp[-1][max_capacity])
    print("Optimal items =", choices[-1][max_capacity])
```


```python
# DP parameters
list_items = ['G', 'S', 'L']
max_capacity = 4
item_values = {"G": 1500, "S": 3000, "L": 2000}
item_weights = {"G": 1, "S": 4, "L": 3}

run_DP_algorithm(list_items, max_capacity, item_values, item_weights)
```

    DP Table (values):
    [0, 1500, 1500, 1500, 1500]
    [0, 1500, 1500, 1500, 3000]
    [0, 1500, 1500, 2000, 3500]
    
    Choices Table (items at each state):
    [[], ['G'], ['G'], ['G'], ['G']]
    [[], ['G'], ['G'], ['G'], ['S']]
    [[], ['G'], ['G'], ['L'], ['G', 'L']]
    
    Optimal value = 3500
    Optimal items = ['G', 'L']


# Knapsack problem FAQ

## What happens if you add an item?

Dynamic programming keeps progressively building on our estimate, so we can simply add a new row and keep updating the answer. For example, consider that we suddenly realize that we can also steal a phone worth \$2000.

|     |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
| Guitar  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$1500<br>G |
| Stereo  | \$1500<br>G | \$1500<br>G | \$1500<br>G | \$3000<br>S|
| Laptop  | \$1500<br>G | \$1500<br>G | \$2000<br>L| \$3500<br>LG |
| Phone  | \$2000<br>P | \$3500<br>PG | \$3500<br>PG | \$4000<br>PL |

The new optimal solution is taking the phone and the laptop, for a value of \$4000!

Note: In any case, the value of a column cannot go down, only up. This is because the estimates are updated only when they are improved.


```python
# code example
list_items = ['G', 'S', 'L', 'P']
max_capacity = 4
item_values = {"G": 1500, "S": 3000, "L": 2000, "P": 2000}
item_weights = {"G": 1, "S": 4, "L": 3, "P": 1}

run_DP_algorithm(list_items, max_capacity, item_values, item_weights)
```

    DP Table (values):
    [0, 1500, 1500, 1500, 1500]
    [0, 1500, 1500, 1500, 3000]
    [0, 1500, 1500, 2000, 3500]
    [0, 2000, 3500, 3500, 4000]
    
    Choices Table (items at each state):
    [[], ['G'], ['G'], ['G'], ['G']]
    [[], ['G'], ['G'], ['G'], ['S']]
    [[], ['G'], ['G'], ['L'], ['G', 'L']]
    [[], ['P'], ['G', 'P'], ['G', 'P'], ['L', 'P']]
    
    Optimal value = 4000
    Optimal items = ['L', 'P']


## What happens if you change the order of the rows?

Similarly to the case of adding an item, due to the iterative nature of the algorithm, changing the order in which we consider the items (i.e., the row order) does not change the final answer. The constructed matrix is different though, since it reflects an essentially different line of reasoning (except the last row, which should be the same!).


```python
# code example
list_items = ['G', 'S', 'L']
max_capacity = 4
item_values = {"G": 1500, "S": 3000, "L": 2000}
item_weights = {"G": 1, "S": 4, "L": 3}

for list_items in [list_items, list_items[::-1]]:
    print(f"Order of items to consider: {list_items}")
    run_DP_algorithm(list_items, max_capacity, item_values, item_weights)
    print('*'*40)
```

    Order of items to consider: ['G', 'S', 'L']
    DP Table (values):
    [0, 1500, 1500, 1500, 1500]
    [0, 1500, 1500, 1500, 3000]
    [0, 1500, 1500, 2000, 3500]
    
    Choices Table (items at each state):
    [[], ['G'], ['G'], ['G'], ['G']]
    [[], ['G'], ['G'], ['G'], ['S']]
    [[], ['G'], ['G'], ['L'], ['G', 'L']]
    
    Optimal value = 3500
    Optimal items = ['G', 'L']
    ****************************************
    Order of items to consider: ['L', 'S', 'G']
    DP Table (values):
    [0, 0, 0, 2000, 2000]
    [0, 0, 0, 2000, 3000]
    [0, 1500, 1500, 2000, 3500]
    
    Choices Table (items at each state):
    [[], [], [], ['L'], ['L']]
    [[], [], [], ['L'], ['S']]
    [[], ['G'], ['G'], ['L'], ['L', 'G']]
    
    Optimal value = 3500
    Optimal items = ['L', 'G']
    ****************************************


## Can you fill in the grid column-wise instead of row-wise?

For this problem, it does not make a difference, but this may not be a feature of every dynamic programming algorithm.

## What happens if you add a smaller item?

Suppose we steal a necklace, which weights 0.5 lb and is worth \$1000. To account for this fractional weight, we could simply consider sub-knapsacks in intervals of 0.5 lb (0.5 lb, 1.0 lb, 1.5 lb...). This would double the size of our grid by increasing the number of columns, but the algorithm would remain largely the same.

## Can you steal fractions of an item?

While we can account for item with non-integer costs (although they have to fit within a discrete set of bins), whether we take the item or not has to be a boolean value; either we take it or not. With the dynamic programming solution, we cannot account for the possibility of taking a fraction of an item.

This case is easily solved with a greedy algorithm though! We could simply take as much space as we can of the most valuable fractional item/material (grain, sand, gold dust...), and if we have some space left, continue with the second most valuable, and so on.

## Optimizing your travel itinerary

Suppose we are going to London for a vacation. We have a 4 days there, and a lot of things we want to do, so we make a list with everything we want to do. How can we come up with an optimal schedule?

| Attraction            | Time   | Rating |
|-----------------------|--------|--------|
| Westminster Abbey     | 1 day  | 7      |
| Globe Theater         | 1 day  | 7      |
| National Gallery      | 2 days | 9      |
| British Museum        | 4 days | 9      |
| St. Paul's Cathedral  | 1 day  | 8      |

It is the knapsack problem all over again! Instead of having a limited space we have to optimize, we have a limited set of time, which we want to fill with as much enjoyment as possible (the example requires us to accept that human enjoyment is an additive property, which has dangerous philosophical connotations, but never mind that).


```python
# code example
list_items = ['W', 'G', 'N', 'B', 'S']
max_capacity = 4
item_values = {"W": 7, "G": 7, "N": 9, "B": 9, "S": 8}
item_weights = {"W": 1, "G": 1, "N": 2, "B": 4, "S": 1}

run_DP_algorithm(list_items, max_capacity, item_values, item_weights)
```

    DP Table (values):
    [0, 7, 7, 7, 7]
    [0, 7, 14, 14, 14]
    [0, 7, 14, 16, 23]
    [0, 7, 14, 16, 23]
    [0, 8, 15, 22, 24]
    
    Choices Table (items at each state):
    [[], ['W'], ['W'], ['W'], ['W']]
    [[], ['W'], ['W', 'G'], ['W', 'G'], ['W', 'G']]
    [[], ['W'], ['W', 'G'], ['W', 'N'], ['W', 'G', 'N']]
    [[], ['W'], ['W', 'G'], ['W', 'N'], ['W', 'G', 'N']]
    [[], ['S'], ['W', 'S'], ['W', 'G', 'S'], ['W', 'N', 'S']]
    
    Optimal value = 24
    Optimal items = ['W', 'N', 'S']


If we visit the Westminster abbey, the national gallery, and the St. Paul cathedral, we will optimize how much enjoyment we can get out of the trip, which amounts to 24 units of enjoyment.

## Handling items that depend on each other

If we reconsider the travel itinerary, but we add several cities, something interesting happens. Suppose we have an activity schedule similar to what we outlined above for London, but also for Rome and Paris. How would we define the problem? Clearly, whatever the solution is, does not include traveling more than once from- or to- the other cities, but rather, visiting each once. In essence, once we are in a city, the cost of seeing parts of it has to be "updated" to account for the fact that we are already there.

But this cannot be implemented in the dynamic programming algorithm! Dynamic programming only works when each subproblem is discrete, this is, when it does not depend on other subproblems.

This means that we cannot optimize a multi-city scheduling problem using the dynamic programming algorithm.

## Is it possible that the solution will require more than two sub-knapsacks?

The way the algorithm is set up, it conceptually optimizes two combined knapsacks at most, but each of those knapsacks can have their own sub-knapsacks.

## Is it possible that the best solution does not fill the knapsack completely?

Yes. If we could steal a very valuable diamond which weighs 3.5 lb, it would be worth to steal it and to "waste" the extra 0.5 lb of space in the 4 lb knapsack.

# Longest common substring

Here are the takeaways from our study of the dynamic programming algorithm applied to the knapsack problem:
* Dynamic programming is useful when we are trying to optimize a variable given a constraint. E.g., in the knapsack problem we optimize for the value of the goods, constrained by the size of the knapsack.
* Dynamic programming can be used when the problem can be broken into discrete subproblems, and when these are independent of each other.

It can be hard to come up with a dynamic programming solution. In this section we will explore different kinds of scenarios. Some general tips to follow:
* It is often useful to picture a dynamic programming problem as a grid
* The values in the cells are generally what we want to optimize
* Each cell represents a subproblem, so thinking about how we can divide the problem into subproblems will inform us of what the rows and columns are.

Now, a classic problem: we want to develop a program that can suggest plausible alternatives when a user mis-types a command or word. For this, we want to guess which word the user intended to write. For example, a user wanted to type "fish", but instead he typed "hish". We have list of similar words, which for the sake of simplicity will reduce to two possibilities: "fish", and "vista". Which word did the user mean to type?

## Making the grid

What does the grid for this problem look like? These are the questions that we need to answer:
* How do we divide the problem into subproblems?
* What are the values of the cells?
* What are the axes of the grid?

In dynamic programming, we are trying to maximize something. In this case, we could try to find the longest substring that two words have in common. What is the longest substring between *hish* and *fish*? [ish]. And between *hish* and *vista*? [is]. Intuitively, we could divide this problem into "what is the longest substring that two substrings of the two words have in common?". In its most simplified form, we simply compute whether two given characters are identical or not!

Now that we know how to divide the problem into subproblems, how are we going to evaluate the sub-problem solutions (cells)? Also intuitively, we could come up with a system that tracks the length of the longest common substring, so that each cell contains a value which represent just that (in other words, how many identical characters are found in a row, anywhere between the two substrings).

So, we want to recursively find what the longest sub-string is between two strings, as we consider more of their length, and we will store the value "length of longest sub-string" for each check. What are the axes of the grid? The indices of each word! Or, for a more intuitive representation, we could use the words themselves.

```
  H  I  S  H
F
I
S
H
```

## Filling in the grid

Now that we have a good idea of what the grid looks like, it is time to fill it. There is no general solution or formula for dynamic programming algorithms, except for the fact that the partial solutions are iteratively added or combined.

In this case, we want a program that "stacks" rewards for concatenating matching characters between the words. Below is an implementation of such program.


```python
def pprint(dp_grid, word_row, word_col):
    """
    Prints the grid and the words in a visually meaningful way
    """
    print('   ', end='')
    for char in word_row:
        print(char, end='  ')
    print()
    for i, row in enumerate(dp_grid):
        print(word_col[i], end=' ')
        print(row)

def run_dp_substring(word_row, word_col):
    # initialize grid as 0's
    dp_grid = []
    for i_row in range(len(word_col)):
        dp_grid.append([])
        for j_col in range(len(word_row)):
            dp_grid[i_row].append(0)
    
    # fill dp grid
    for i_row in range(len(word_col)):
        for j_col in range(len(word_row)):
            # check whether the pair of characters match
            match = word_col[i_row] == word_row[j_col]
            if match:
                # manage edge cases (no previous comparisons exist)
                if i_row == 0 or j_col == 0:
                    previous_best = 0
                else:
                    # assign contribution of previous comparison
                    previous_best = dp_grid[i_row-1][j_col-1]
                # update values of the DP matrix
                dp_grid[i_row][j_col] = previous_best + 1

    pprint(dp_grid, word_row, word_col)
```


```python
run_dp_substring('HISH', 'FISH')
```

       H  I  S  H  
    F [0, 0, 0, 0]
    I [0, 1, 0, 0]
    S [0, 0, 2, 0]
    H [1, 0, 0, 3]



```python
run_dp_substring('VISTA', 'HISH')
```

       V  I  S  T  A  
    H [0, 0, 0, 0, 0]
    I [0, 1, 0, 0, 0]
    S [0, 0, 2, 0, 0]
    H [0, 0, 0, 0, 0]


For this problem, the final solution is not in the last cell. For the knapsack problem, the last cell always had the final solution (since we were trying to pack *as much* as possible), but in this problem, the solution is the largest number in the grid, irrespective of its location.

Which string has more in common with *hish*? The asnwer is *fish*, since both share a substring consisting of three letters, as opposed to *vista*, which only shares two with *fish*.

## Longest common subsequence

Suppose the user typed *fosh*. Did they mean *fish* or *fort*? Let us run our DP algorithm


```python
run_dp_substring('FOSH', 'FORT')
```

       F  O  S  H  
    F [1, 0, 0, 0]
    O [0, 2, 0, 0]
    R [0, 0, 0, 0]
    T [0, 0, 0, 0]



```python
run_dp_substring('FOSH', 'FISH')
```

       F  O  S  H  
    F [1, 0, 0, 0]
    I [0, 0, 0, 0]
    S [0, 0, 1, 0]
    H [0, 0, 0, 2]


Both FORT and FISH have a longest common substring of length 2, so this is not very useful. What we really need to compare is the longest common *subsequence*: the number of letters in a sequence that the two words have in common (in order, but not necessarily contiguously). How can we calculate the longest common subsequence?

We can slightly alter the 'largest common substring' algorithm, in order to not only remember the "best" contiguous match, but also to "propagate" the current largest subsequence forward in the comparison.

Below there is a possible implementation


```python
def run_dp_subsequence(word_row, word_col):
    # initialize grid as 0's
    dp_grid = []
    for i_row in range(len(word_col)):
        dp_grid.append([])
        for j_col in range(len(word_row)):
            dp_grid[i_row].append(0)
    
    # fill dp grid
    for i_row in range(len(word_col)):
        for j_col in range(len(word_row)):
            # check whether the pair of characters match
            match = word_col[i_row] == word_row[j_col]
            if match:
                # manage edge cases (no previous comparisons exist)
                if i_row == 0 or j_col == 0:
                    previous_best = 0
                else:
                    # assign contribution of previous comparison
                    previous_best = dp_grid[i_row-1][j_col-1]
                # update values of the DP matrix
                dp_grid[i_row][j_col] = previous_best + 1
            # "propagate" current best to future comparisons
            else:
                previous_best = max(dp_grid[i_row-1][j_col],
                                    dp_grid[i_row][j_col-1])
                dp_grid[i_row][j_col] = previous_best

    pprint(dp_grid, word_row, word_col)
```


```python
run_dp_subsequence('FOSH', 'FORT')
```

       F  O  S  H  
    F [1, 1, 1, 1]
    O [1, 2, 2, 2]
    R [1, 2, 2, 2]
    T [1, 2, 2, 2]



```python
run_dp_subsequence('FOSH', 'FISH')
```

       F  O  S  H  
    F [1, 1, 1, 1]
    I [1, 1, 1, 1]
    S [1, 1, 2, 2]
    H [1, 1, 2, 3]


Although both FISH and FORT have a largest common substring of length 2 when compared to FOSH, FISH has a largest common subsequence of 3, which we interpret as being closer to what the user intended to write.

Dynamic programming algorithms are used in many different fields:
* In biology, dynamic programming can be used to produce optimal local or global alignments between two biological sequences, be it nucleic acids or proteins. This is very useful in order to assess how similar the genomic material of two species are.
* `git diff` uses dynamic programing in order to determine the differences between two files (they have to be aligned to point out the differences!)
* The *Levenshtein distance* measures how similar two strings are, by means of a dynamic programming algorithm. This is used for everything from spell-check to comparing whole documents to reference libraries of copyrighted data.

**Recap**
* Dynamic programming is useful when we are trying to optimize a problem, which can be broken into discrete and independent problems, given a constraint.
* Every dynamic programming solution involves a grid, in which each cell stores a value we want to optimize.
* Each cell represent a subproblem, so the way we divide the problem into discrete (and independent!) subproblems informs us of the meaning of the axes.
* There is no general formula for calculating a dynamic programming solution.

# Exercises

**11.1** Regarding the knapsack problem (Guitar, stereo, laptop, phone), suppose you can steal another item: a mechanical keyboard player. It weighs 1 lb and is worth \$1000. Should you steal it?


```python
# code example
list_items = ['G', 'S', 'L', 'P', 'K']
max_capacity = 4
item_values = {"G": 1500, "S": 3000, "L": 2000, "P": 2000, "K": 1000}
item_weights = {"G": 1, "S": 4, "L": 3, "P": 1, "K": 1}

run_DP_algorithm(list_items, max_capacity, item_values, item_weights)
```

    DP Table (values):
    [0, 1500, 1500, 1500, 1500]
    [0, 1500, 1500, 1500, 3000]
    [0, 1500, 1500, 2000, 3500]
    [0, 2000, 3500, 3500, 4000]
    [0, 2000, 3500, 4500, 4500]
    
    Choices Table (items at each state):
    [[], ['G'], ['G'], ['G'], ['G']]
    [[], ['G'], ['G'], ['G'], ['S']]
    [[], ['G'], ['G'], ['L'], ['G', 'L']]
    [[], ['P'], ['G', 'P'], ['G', 'P'], ['L', 'P']]
    [[], ['P'], ['G', 'P'], ['G', 'P', 'K'], ['G', 'P', 'K']]
    
    Optimal value = 4500
    Optimal items = ['G', 'P', 'K']


**Answer**: Yes, if we stole the guitar, the phone, and the keyboard, we could get away with \$4500.

**11.2** Suppose we are going camping. We have a knapsack that will hold 6 lb and we can take the following items, listed together with their value and weight. The higher the value, the more important the item is.
* Water, 3 lb, 10
* Book, 1 lb, 3
* Food, 2 lb, 9
* Jacket, 2 lb, 5
* Camera, 1 lb, 6

What is the optimal set of items to take on our camping trip?


```python
# code example
list_items = ['W', 'B', 'F', 'J', 'C']
max_capacity = 6
item_values = {"W": 10, "B": 3, "F": 9, "J": 5, "C": 6}
item_weights = {"W": 3, "B": 1, "F": 2, "J": 2, "C": 1}

run_DP_algorithm(list_items, max_capacity, item_values, item_weights)
```

    DP Table (values):
    [0, 0, 0, 10, 10, 10, 10]
    [0, 3, 3, 10, 13, 13, 13]
    [0, 3, 9, 12, 13, 19, 22]
    [0, 3, 9, 12, 14, 19, 22]
    [0, 6, 9, 15, 18, 20, 25]
    
    Choices Table (items at each state):
    [[], [], [], ['W'], ['W'], ['W'], ['W']]
    [[], ['B'], ['B'], ['W'], ['W', 'B'], ['W', 'B'], ['W', 'B']]
    [[], ['B'], ['F'], ['B', 'F'], ['W', 'B'], ['W', 'F'], ['W', 'B', 'F']]
    [[], ['B'], ['F'], ['B', 'F'], ['F', 'J'], ['W', 'F'], ['W', 'B', 'F']]
    [[], ['C'], ['F'], ['F', 'C'], ['B', 'F', 'C'], ['F', 'J', 'C'], ['W', 'F', 'C']]
    
    Optimal value = 25
    Optimal items = ['W', 'F', 'C']


**Answer**: The optimal set of items to take is the water, the food, and the camera.

**11.3** Draw and fill in the grid to calculate the longest common substring between *blue* and *clues*.

**Answer**:


```python
run_dp_substring('BLUE', 'CLUES')
```

       B  L  U  E  
    C [0, 0, 0, 0]
    L [0, 1, 0, 0]
    U [0, 0, 2, 0]
    E [0, 0, 0, 3]
    S [0, 0, 0, 0]


The longest common substring is LUE
