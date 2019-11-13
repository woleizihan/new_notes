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
