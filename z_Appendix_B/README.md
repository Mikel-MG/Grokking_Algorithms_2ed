Both the set-covering and traveling salesperson problems are hard to solve. We have to check every possible iteration to find the smallest set cover or the shortest route. Both of these problems are NP-hard. The terms *NP*, *NP-hard*, and *NP-complete* can cause confusion, and are discussed in this appendix. Here is a roadmap and dependency tree of the concepts that will be introduced:
```
      The satisfiability problem
      Reductions
      NP-hard
NP -> NP-complete
```

# Decision problems

**NP-complete** problems are always decision problems, such as a yes/no answer. The travelling salesperson problem is not a decision problem, is an optimization problem. However, we can frame it as a decision problem. Instead of asking, "what is the shortest path?", we could have asked "is there a path of length 5?". Every problem discussed in this appendix will be a decision problem, so when we refer to the "traveling salesperson", we refer to its *decision* version.

# The satisfiability problem

Consider, a set of group of friends who have the following topping requirements to order a pizza:
* Pepperoni (Anthony)
* Pepperoni or sausage (Bastian)
* Olivers or onions (Clint)
* No onions (Daryl)
A pepperoni and olives pizza should make everyone happy, and satisfy all the requirements. This is an example of a SAT problem.

We can frame the problem as follows: we have four boolean variables

```python
pepperoni = ?
sausage = ?
olives = ?
onions = ?
```

and then we have to come up with True/False assignments such as the expression

```python
(pepperoni) and (pepperoni or sausage) and (olives or onions) and (not onions)
```
evaluates to `True`.


```python
# this is not the only possible solution!
pepperoni = True
sausage = False
olives = True
onions = False

SAT_solved = (pepperoni) and (pepperoni or sausage) and (olives or onions) and (not onions)
print(f"SAT problem solved: {SAT_solved}")
```

    SAT problem solved: True


The SAT problem is famous because it is the first NP-complete problem, described in 1971. Before this, the concept of an NP-complete problem did not exist. More generally, such a problem starts with a boolean formula, such as
```python
if (pepperoni) and (olives or onions):
    print("Pizza time")
```
and then try to assign boolean variables so that the formula is evaluated as `True`. The example is trivial, so we can solve it with `pepperoni=True` and `olives=True`.

Some SAT problems might be impossible to solve! Consider this:

```python
if (olives or onions) and (not olives) and (not onions):
    print("Pizza time")
```
there is no combination of boolean variables which will evaluate this expression as `True`.

SAT problems are hard to solve, since we can have any numbers of variables and clauses, and the problems scale in complexity quite quickly. We could try to solve it by listing every combination, but it has a big O run time of $O(2^{n})$ for $n$ toppings (due to the fact that this results in $2^{n}$ possible pizzas).

For this small problem, we can list every combination, and generate something called a `truth table`.


```python
import itertools

print(f"Pepper.\tOlives\tOnions\tSolved")
for pepperoni, olives, onions in itertools.product([True, False], repeat=3):
    SAT_result = (pepperoni) and (olives or onions)
    print(f"{pepperoni}\t{olives}\t{onions}\t{SAT_result}")
```

    Pepper.	Olives	Onions	Solved
    True	True	True	True
    True	True	False	True
    True	False	True	True
    True	False	False	False
    False	True	True	False
    False	True	False	False
    False	False	True	False
    False	False	False	False


# Hard to solve, quick to verify

There are problems which are hard to solve, but quick to verify. Take for instance, generating a sentence which is a palindrome and that includes the words *cat* and *car*. This may take some time, but if given a potential answer, such as "Was it a car or a cat I saw?", we can quickly verify that it is, in fact, a solution.

A SAT problem is hard to solve, like the set-covering problem or the traveling salesperson problem, but unlike those problems, verifying a solution is easy; this is due to the framing of the problem as a boolean expression.

The SAT problem is *quick to verify*, so it is in NP. NP is the class of problems that can be *verified* in polynomial time. NP problems may or may not be easy to solve, their categorization depends exclusively on being easy to verify.

>Polynomic functions are expressions which consist of a sum of terms, where each term has a constant coefficient and a variable (x) with a non-negative integer exponent. $3x^4+5$ is a polynomial, while $n!$ or $2^n$ are not.

P, however, is the class of problems which are *quick to solve* as well as *quick to verify*; here, quick also means in polynomial time. Polynomial time means its big O is not bigger than a polynomial.

P is a subset of NP. In other words, NP contains all the problems in P, plus others.

***
**P vs NP** is a famous problem. While problems in P are quick to verify and to solve, the problems in NP are quick to verify but may or may not be quick to solve. The P vs NP problem asks whether every problem that is quick to verify is also quick to solve. If that is the case, P would not be a subset of NP; P would equal NP (P = NP).
***

# Reductions

When we are dealing with a hard problem, changing the problem to an easier one is a valid strategy. For example, multiplying two binary numbers. We could just convert these into decimal numbers, and then proceed with the multiplication.

This is called a reduction, since we reduce a problem that we do not know how to solve to a problem which we DO know how to solve. This is a common strategy in computer science.

# NP-hard

We consider a problem as **NP-hard** *if any problem in NP can be reduced to that problem*. We can also reduce all NP problems to any NP-hard problem. For example, we can reduce all NP problems to SAT.

An additional requirement is that we need to be able to reduce all these problems in *polynomial time*. This is important because we do not want the reducing part to be the bottleneck. Any NP problem can be reduce to SAT in polynomial time, so it is NP-hard.

Since any problem in NP can be reduced to any NP-hard problem, a polynomial time solution for any one NP-hard problem gives us a polynomial time solution for every problem in NP!

# NP-complete

We have seen two definitions:
* Problems in NP are quick to verify and may or may not be quick to solve
* Problems that are NP-hard are at least as hard (computationally complex/difficult/expensive) as the hardest problems in NP, and any problem in NP can be reduced to a problem in NP-hard.

In this framework, we will define a problem as **NP-complete** *if it is both NP and NP-hard*.

NP-complete problems are 
* Hard to solve (for now; if P = NP is proven, they would not be)
* Easy to verify

And any problem in NP can be reduced to a problem that is NP-complete. NP-complete is the set intersection between NP (quick to verify) and NP-hard (any problem in NP can be reduced to this).

The key difference is that NP-hard problems are at least as hard as the hardest problems in NP, but they could be even "harder" in the sense that solutions may be impossible to check efficiently.

In other words, NP-hard problems could be in NP, NP-complete problems must be.

# Recap

* A problem is in P if it is both quick to solve and quick to verify

* A problem is in NP if it is quick to verify; it may or may not be quick to solve

* If we find a fast (polynomial time) algorithm for every problem in NP, then P=NP (and if we demonstrate the identiy, there must be a fast solution to every problem!)

* A problem is NP-hard if any problem in NP can be reduced to that problem

* If a problem is in both NP and NP-hard, it is NP-complete
