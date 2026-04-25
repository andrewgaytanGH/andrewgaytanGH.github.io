---
layout: post
title: Finding Prime Numbers with Python
image: "/posts/primes_image.jpeg"
tags: [Python, Primes]
---

This post demonstrates an efficient approach in Python for identifying all prime numbers below a given value. For example, passing a value of 100 will return all prime numbers less than or equal to 100.

A prime number is defined as a number that is divisible only by 1 and itself. For instance, 7 is prime, while 8 is not, since it is divisible by 2 and 4 in addition to 1 and itself.

---

We begin by defining an upper bound for our search. In this example, we use 20.

```python
n = 20
```

The smallest prime number is 2. We construct a set containing all integers from 2 through the upper bound. A set is used instead of a list because it provides efficient operations for removing elements during iteration.

```python
number_range = set(range(2, n + 1))
```

We also initialize a list to store identified prime numbers.

```python
primes_list = []
```

Before implementing the full loop, it is useful to walk through the logic step-by-step to validate correctness.

We extract a value from the set using the `pop()` method:

```python
prime = number_range.pop()
```

Since 2 is the smallest value and cannot be divided by any smaller number, it is prime:

```python
primes_list.append(prime)
```

Next, we generate all multiples of this prime within the range:

```python
multiples = set(range(prime * 2, n + 1, prime))
```

This uses the `range(start, stop, step)` pattern, where:
- `start = prime * 2`
- `stop = n + 1`
- `step = prime`

We then remove all multiples from the candidate set:

```python
number_range.difference_update(multiples)
```

At this point, the smallest remaining value in `number_range` must be prime. This process can now be repeated.

---

### Full Implementation

```python
n = 1000

number_range = set(range(2, n + 1))
primes_list = []

while number_range:
    prime = number_range.pop()
    primes_list.append(prime)
    multiples = set(range(prime * 2, n + 1, prime))
    number_range.difference_update(multiples)
```

To summarize results:

```python
prime_count = len(primes_list)
largest_prime = max(primes_list)

print(f"There are {prime_count} prime numbers between 1 and {n}, the largest of which is {largest_prime}")
```

---

### Function Implementation

```python
def primes_finder(n):

    number_range = set(range(2, n + 1))
    primes_list = []

    while number_range:
        prime = number_range.pop()
        primes_list.append(prime)
        multiples = set(range(prime * 2, n + 1, prime))
        number_range.difference_update(multiples)

    prime_count = len(primes_list)
    largest_prime = max(primes_list)

    print(f"There are {prime_count} prime numbers between 1 and {n}, the largest of which is {largest_prime}")
```

---

### Important Note

To ensure deterministic behavior, replace:

```python
prime = number_range.pop()
```

with:

```python
prime = min(number_range)
number_range.remove(prime)
```
