# The classroom scheduling problem

Consider the following class schedule

| Class | Start | End   |
|-------|--------|--------|
| ART   | 9:00   | 10:00 |
| ENG   | 9:30   | 10:30 |
| MATH  | 10:00  | 11:00 |
| CS    | 10:30  | 11:30 |
| MUSIC | 11:00  | 12:00 |

We cannot hold *all* the classes, since some of them overlap, but we want to hold as many as possible. How do we pick what set of classes to hold so that to get the largest set of classes possible?

We can greatly simplify the problem by noting two critical points:
* All classes take (proverbially, cost us) 1 hour each.
* Each class is staggered by 30 minutes from the previous class.

With this in mind, we can maximize how many classes we take by

while we have time to spend:
1) Pick the class the start/ends the soonest
2) move to the next pick

If we proceed with this algorithm, we will end up choosing

* Art: 9-10
* Math: 10-11
* Music: 11-12

This is an example of a *greedy* algorithm, in which at each step we pick the *locally optimal* solution, **hoping** that in the end we will arrive at a *globally optimal* solution. In this case, because of the characteristics of the problem, which we noted above, it worked, which is not always the case with greedy algorithms.

# The knapsack problem

Suppose we are a greedy thief. We have a knapsack, in which we want to store our loot, but we can only hold 35 pounds in it. How can we maximize the value of the items we steal?

We can attempt the greedy strategy of the previous section. While we have space in our knapsack:
1) Pick the most expensive thing that will fit in our knapsack
2) move to the next item

If we apply this algorithm to the following set of items:
* Stereo (3000\$, 30 lbs)
* Laptop (2000\$, 20 lbs)
* Guitar (1500\$, 15 lbs)
* Gym weights (10\$, 10 lbs)

we will leave the store with the stereo, and 3000\$ worth of stolen goods. The *greedy* approach clearly missed the optimal option, which was taking the other two items, for 3500\$ worth of goods. On the other hand, the algorithm clearly avoided the "worst" option, which was to steal the gym weights.

This is to say, *sometimes perfect is the enemy of good*, and for a variety of problems, having a "good enough" solution, is oftentimes "good enough".

# The set-covering problem

While the previous examples illustrate the strenghts and weaknessses of greedy algorithms, this example shows a problem where a greedy algorithm is **absolutely necessary**.

Suppose we are starting a radio show. We want to reach as many listeners as possible (this is, in as many states as possible). We have to decide which stations will play our radio show, since it costs money to be in each station, and we want to maximize coverage / expenditure ratio. For simplicity, let us assume each state has a comparable number of potential listeners.

How can we figure otu the smallest set of stations that cover all $n$ states?

We could approach this systematically: let us compute all potential subsets of $n$, and pick the set with the smallest number of stations that covers all $n$ states. The problem is that this computation takes $O(2^{n})$ time, because there are $2^{n}$ subsets.

> The reason for the $2^n$ is that we can conceptualize the construction of a set as a binary vector which encodes whether each set is included or not. How many such binary vectors are possible for $n$ sets?

If we can calculate 10 subsets per seconds, here is a table that summarizes how much time would it take us to compute the total number of subsets for a modest number of states.

| Number of Stations | Time Taken          |
|--------------------|----------------------|
| 5                  | $2^{5}$ = 3.2 s    |
| 10                 | $2^{10}$ = 102.4 s |
| 25                 | $2^{25}$ = 388 days |
| 50                 | $2^{50} = 1 \times 10^{10}$ days |
| 100                | $2^{100} = 1 \times 10^{25}$ days |

Clearly, this is not feasible. In fact, there is no known algorithm that solves this fast enough. What can we do?

## Approximations algorithms

Let us attempt a greedy algorithm:
1) Pick the station that covers the most states that have not been covered yet. It is okay if the station covers some states that have been covered already.
2) Repeat until all the states are covered.

This is called an *approximation algorithm*. It likely will not produce an optimal solution, but these types of algorithms are judged by
* how fast they are
* how close they are to the optimal solution

Greedy algorithms are a good choice because not only are they simple to come up with, but that simplicity means they usually run fast too. In this case, the greedy algorithm runs in $O(n^2)$ time, where $n$ is the number of radio stations.
> Why $O(n^2)$? Because we have to go through an outer loop $n$ times (once per station), and within each loop we have to check what is the next station, which under simplified conditions can be thought of as taking $n$ as well

## Code for setup

We need to store the states we want to cover, the stations that we are choosing from, as well as the final selected stations


```python
# sets can be thought of us unordered, non-repeating lists of elements
states_needed = set(['mt', 'wa', 'or', 'id', 'nv', 'ut', 'ca', 'az'])

# we will store station in a dictionary(station_name) = set(covered_states) form
stations = {}
stations['kone'] = set(['id', 'nv', 'ut'])
stations['ktwo'] = set(['wa', 'id', 'mt'])
stations['kthree'] = set(['or', 'nv', 'ca'])
stations['kfour'] = set(['nv', 'ut'])
stations['kfive'] = set(['ca', 'az'])

# finally, we also need a set to hold the set of chosen stations
chosen_stations = set()
```

## Calculating the answer


```python
# run loop until we achieve our objective
# (nore more additional needed states)
while states_needed:
    # this will store the best choice for the next station
    best_station = None
    states_covered = set()
    # for each station and the states it covers
    for station, states_for_station in stations.items():
        # check how many states that we want are covered
        covered = states_needed & states_for_station
        # if this stations covers increases the coverage
        # (compared to the previously chosen best station)
        if len(covered) > len(states_covered):
            # we update the best possible station
            best_station = station
            states_covered = covered

    # add our best choice to the set of chosen stations
    chosen_stations.add(best_station)
    # remove the states that we are newly covering
    states_needed -= states_covered

```


```python
print(chosen_stations)
```

    {'kthree', 'ktwo', 'kfive', 'kone'}


Instead of stations 1, 2, 3, and 5, we could have chosen stations 2, 3, 4, and 5. Let us now compare the run time of the greedy algorithm to the exact algorithm (as before, we are assuming we can compute 10 sets in per second)

| Number of Stations | Exact algorithm ($O(2^n)$) | Greedy algorithm ($O(n^2)$) |
|--------------------|----------------------|----------------------|
| 5                  | $2^{5}$ = 3.2 s    | $5^{2}$ = 2.5 s |
| 10                 | $2^{10}$ = 102.4 s | $10^{2}$ = 10 s |
| 25                 | $2^{25}$ = 388 days |  $25^{2}$ = 62.5 s |
| 50                 | $2^{50} = 1 \times 10^{10}$ days |  $50^{2}$ = 250 s |
| 100                | $2^{100} = 1 \times 10^{25}$ days |  $100^{2}$ = 16.67 min |

The greedy algorithm will not give us an exact answer, but it runs much faster. The set-covering problem is known as an **NP-hard problem**. This is expanded in **appendix B**.

In summary
* Greedy algorithms optimize locally, hoping to end up with a global optimum.
* If you have an NP-hard problem, your best bet is to use an approximation algorithm.
* Greedy algorithms are easy to write and fast to run, so they make good approximation algorithms.

# Exercises

**10.1** We want to pack a truck with boxes. All the boxes are of different sizes, and we are trying to maximize the space we can use in each truck. How can we pick boxes to maximize space? Come up with a greedy strategy. Will that give us the optimal solution?

**Answer**: We can approach this problem with a greedy algorithm, such as, while we have space in the truck:
* pick the box with the **smallest** space footprint, and load it in a way that maximizes the remaining space
* move to the next box

By prioritizing small boxes first, we are likely going to miss some of the largest boxes, but those are the ones that will likely generate the most "unused" space in the truck. It may not give us the optimal solution, since there are probably situations in which we can remove a box and put another one that makes better use of space.

**10.2** Suppose we are going on holiday, and we have seven days to see everything we can. We assign a point value to each item (how much we want to see it) and estimate how long it takes. How can we maximize the point total (seeing all the things we really want to see) during the holiday? Come up with a greedy strategy. Will what give us the optimal solution?

**Answer**: we can implement a greedy strategy, in which, while we still have available time for sightseeing:
* we pick the activity with the highest assigned points
* move on to the next activity

We cannot guarantee that this algorithm will find the optimal solution, but we can be confident that the resulting plan will make us reasonably happy.
