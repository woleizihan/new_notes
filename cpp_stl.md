# Predicates on ranges with the STL

Algorithms that return a boolean characteristic of one or several ranges.

## The `*_of` series
Three algorithms that indicates whether all, some or not onf the elements of a range satisfy a given condition. The condition is expressed by a function pointer that take an element of the range and returns a `bool`.

- `std::all_of`: check whether all of the elements in the range satisfy the given condition.
- `std::any_of`: check whether any one of the elements inthe range satisfies the given condition.
- `std::none_of`: check whether no element in the range satisfy the given condition
- `boost::algorithm::one_of`: check whether exactly on element in the range satisfies the given condition.

Boost also provides `*-equal` version of the above algorithms, that accept a range and a value, and comparison is done with `operator==`.

- `boost::algorithm::all_of_equal`
- `boost::algorithm::any_of_equal`
- `boost::algorithm::none_of_equal`
- `boost::algorithm::one_of_equal`

## `std::equal`

`std::equal` can be used to compare two ranges, checking if elements are respectively equal. 
- It used to take in 1.5-Range: `first.begin(), first.end(), second.begin()`
- Now it is overloaded to take two full ranges

## Checking for permutations
`std::is_permutation(v1.begin(), v1.end(), v2.begin(),v2.end())` with complexity at most $O(n^2)$.

## `std::mismatch` and `std::lexicographical_compare`
These two algos let you implement some sort of ordering on ranges, which you can use to compare two ranges.
- `std::mismatch`: compare two input ranges from the beginning and returns the first palce where they differ in the form of `std::pair` of iterators
- `std::lexicographical_compare`: construct a dictionary ordering on ranges and compare two by two

# How to check if a string is a prefex of another one in C++
- Designing a clear interface
- Reusing standard code, with standard algos of the STL
- Respecting levels of abstraction

## A "strong" interface
`bool isPrefiex(string prefix, string text)` is not so good in the sense that it's not clear which is the prefix of which. We can improve this by defining structs:
- `struct Prefix`
- `struct Text`
- `bool isPrefix(Prefix prefix, Text text)`
- can also use constant references: `struct PrefixConstRef {std::string const& value;}`

## Reusing code for implementation
`std::mismatch` on the strings

## Raising the level of abstraction to ranges
We can wrap `std::mismatch` in an interface that expect the strings themselves.

## The problem of 1.5 ranges
We can manually check the size in wrappers: `if (range1.size() <= range2.size())...`

## Checking for prefix in any sequence
Just need to make the Prefix/MainSequence structs generic: `template<typename T> Prefix<T>`

# Understanding the implementation of $std::is_permutation$
`is_permutation` takes two collections and then returns a `bool`, which indicates whether the two collections ahve the same contents with possibly different order.

## Naive by wrong implementation
For each element in the first collection, check if it is part of the second collection. But this will not work if there are repeated elements.

## Implementing a correct version of `is_permutation`
We want to check if each value in the first collectionappears the same number of times in both collections.
- For each element in the first collection check the number of occurence in both collections using `std::count`

## Counting each value only once

## Not counting a value that is not in the second collection

## Not counting the beginning of the first collection

# How to use `is_permutation` on collections of different types
For `std::permutation` both collections have to contain values of the same type

## The reason of the requirement
- No such rule for `std::equal`
- Maybe the requirement comes from the fact that the comparison function needs to also be able to compare elements from the first collection together.

## What having the same value types prevents us to do
We can't use custom compare functions

## Checking for a permutation on different types
- We can use custom `is_permutation` function
- We can overload comparison operator