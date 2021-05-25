---
title: "Finding Trott Constants"
date: 2020-07-18T15:52:28-05:00
description: My work in finding possible Trott constants.
summary: My work in finding possible Trott constants.
tags: ["math", "research"]
---

So I recently had the pleasure of joining a research group in the field of measure theory and an interesting problem was brought up. The problem was whether Trott constants existed if you used to typical notion of a continued fraction.

> A Trott Constant is "a real number whose decimal digits are equal to the terms of its continued fraction."

> A continued fraction (CF) is a number in the form `[a; x, y, z ...]` which equals `a + 1/(x+1/(y+1/(z...)))`. 


But Trott in his work used numbers from 0-9 for each term (x,y,z...) in the continued fraction which is not typical. Usually you would only use positive integers from 1 onward.

The goal of this work is to try and find as many "pre-trott" constants as possible.

A pre-trott constant is a continued fraction and decimal pair whose values match in decimal for all the digits in the terms of the continued fraction.

Ex: `[0; 3, 29, 5, 7]` which matches `0.32957039824396362`. It matches the first 5 digits which is all it needs to match for since that is the number of digits in the CF.

So our first step is to construct a function that takes a continued fraction and computes it as a real number with a given amount or precision (decimal places accurate).

We are using `mpmath` for arbitrary precision floating point arithmetic. Essentially, we can set the precision of the computation to be a certan number of decimal places but we shall set it at the minimum necessary as the larger the precision, the longer the computation.

Additionally, note that our CF representation will just be a simple list of numbers. We will not store the zero as the beginning number does not really matter in either representation.

### Count Digits

This is used to count the number of digits in our CF representation which will allow us to figure out how many decimal places to check for equivalence.


```python
from mpmath import mp, mpf


def count_digits(cf_list):
    """A Function to count amount of digits in a list of numbers
    We will use this to figure out the amount of precision we want"""
    total = 0
    for num in cf_list:
        while num > 0: # note: numbers are guaranteed to be positive
            num = num // 10
            total = total + 1
    return total
```

We shall test the our count digits function.


```python
a = [100, 5, 7, 99]
print(count_digits(a))
print(a)
```

output:
```commandline
7
[100, 5, 7, 99]
```


## Continued Fraction to Real Number conversion

Here we perform the conversion from CF to real number via repeated divisions.


```python
def cf_to_real(cf_list, precision):
    with mp.extradps(precision+1):
        curr = 0 # 0 is previous term
        for item in cf_list[::-1]: # loop through list backwards
            curr = 1 / mpf(item + curr)
        return curr
```


```python
cf = [3, 29, 5, 7]

cf_to_real(cf, count_digits(cf) + 1)
```

output:



    mpf('0.32957039824396362')



## Continued Fraction to Expected Trott Constant approximation

Here we are calculating what the real number needs to equal if this CF is a trott constant approximate.


```python
def cf_to_trott_approx(cf_list):
    return "0.{}".format("".join(map(str,cf_list)))
```


```python
cf = [3, 29, 5, 7]
cf_to_trott_approx(cf)
```

output:



    '0.32957'



## Check if CF form has equal real and expected values

Here we are checking if the real number representation of the CF matches the digits in the terms of the CF.


```python
def valid_trott(cf_list):
    precision = count_digits(cf_list) + 2
    with mp.extradps(precision+1):
        real = str(cf_to_real(cf_list, precision))[:precision]
        want = cf_to_trott_approx(cf_list)
        # print("{} {}".format(real, want)) # for debugging
        return real == want
```

Testing the `valid_trott` function.


```python
print(valid_trott([3,29,5,7])) # true
print(valid_trott([3,29,5,8])) # false
print(valid_trott([3])) # true
print(valid_trott([3,30])) # false
print(valid_trott([3,29])) # true
print(valid_trott([3, 333329])) # true
print(valid_trott([3, 3333329])) # true
print(valid_trott([3, 33333329])) # true
```

output:

    True
    False
    True
    False
    True
    True
    True
    True


## Testing for any possible Pre Trott constant 3 terms long

This is the general structure of what we wish to do to check if there are n-item long valid trott constants. However doing this many nested for-loops gets tedious and it is actually not possible to general to an nth degree so we must use a recursive technique known as backtracking.


```python
ITEM_MAX = 1000000

# for-loop solution (NOT backtracking)
for i in range(1, ITEM_MAX):
    a = [i]
    if valid_trott(a):
        print(a)
        for j in range(1, ITEM_MAX):
            a = [i,j]
            if valid_trott(a):
                print(a)
                for k in range(1, ITEM_MAX):
                    a=[i,j,k]
                    if valid_trott(a):
                        print(a)
```


output:

    [3]
    [3, 29]
    [3, 29, 5]
    [3, 29, 54]
    [3, 29, 545]
    [3, 29, 5454]
    [3, 29, 54545]
    [3, 29, 545454]
    [3, 329]
    [3, 329, 9]
    [3, 329, 95]
    [3, 329, 959]
    [3, 329, 9595]
    [3, 329, 95951]
    [3, 329, 959514]
    [3, 3329]
    [3, 3329, 9]
    [3, 3329, 99]
    [3, 3329, 995]
    [3, 3329, 9959]
    [3, 3329, 99599]
    [3, 3329, 995995]
    [3, 33329]
    [3, 33329, 9]
    [3, 33329, 99]
    [3, 33329, 999]
    [3, 33329, 9995]
    [3, 33329, 99959]
    [3, 33329, 999599]
    [3, 333329]
    [3, 333329, 9]
    [3, 333329, 99]
    [3, 333329, 999]
    [3, 333329, 9999]
    [3, 333329, 99996]
    [3, 333329, 999960]


Here we can see that the first term must be a 3 to continue, 10 works at first but immediately dies out. The second term also is quite interesting as it looks like it must be 29, 329, 3329, and so on.

This is already a useful result in that we can generalize and say that we can find a pre trott constant that is equal up to any N. (If we wanted a pre trott that is equal to 20 digits we could just have `[0; 3, 333333333333333329]`.)


```python
prev = 29 
for i in range(2,20):
    a = [3, prev + (3 * 10 ** i)]
    prev = prev + (3 * 10 ** i)
    if valid_trott(a):
        print(a) # print only the valid approximations
```

output:

    [3, 329]
    [3, 3329]
    [3, 33329]
    [3, 333329]
    [3, 3333329]
    [3, 33333329]
    [3, 333333329]
    [3, 3333333329]
    [3, 33333333329]
    [3, 333333333329]
    [3, 3333333333329]
    [3, 33333333333329]
    [3, 333333333333329]
    [3, 3333333333333329]
    [3, 33333333333333329]
    [3, 333333333333333329]
    [3, 3333333333333333329]


The above is just a way to show that the repeating 3s at the beginning are valid pre trotts.

## Backtracking Solution
> backtracking is the best way to do a brute force for all permutations in a general case.

Here we will take what we do in each of the for-loops in the code above and call this function recursively. I will set more limiting restrictions on this version as the above code actually does take a bit of time to run just for 3 items of in the range of `[1, 1000000]`.


```python
def trott_backtracking(current_cf, count, max_items, max_size, file=None):
    if count < max_items:
        valid = []
        for a in range(1, max_size):
            new_cf = current_cf + [a]
            if valid_trott(new_cf):
                valid.append(new_cf)
        if valid:
            for v in valid:
                # These branches are still alive
                print("VALID: {}".format(str(v)), file=file)
                trott_backtracking(v, count+1, max_items, max_size, file=file)
```


```python
with open("output1.txt", "w+") as out:
    trott_backtracking([], 0, 1000, 50, out)
```

Since the output was long I moved it to another file but it can be seen in `output1.txt`.

Now I just need to run this for much larger possible values.


```python
with open("output3.txt", "w+") as out:
    trott_backtracking([], 0, 1000, 10000, out)
```

## TODO

There are a few improvements I want to add to the code soon. Currently it is only using one process and hence only one of my CPU cores.

- [ ] use `multiprocessing` module to parallelize code
- [ ] figure out a faster way to check for decimal equality than to convert to a string?

That's all for now. -- Pry (Pranoy Dutta)
