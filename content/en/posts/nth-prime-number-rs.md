---
title: "Finding nth Prime number"
keywords: ["rust", "slive of eratosthenes", "prime numbers"]
date: 2021-07-17T21:45:35+05:45
tags: ["programming", "numerical"]
categories: ["technical"]
description: "Algorithm to find nth prime number using sieve of eratoshenes in rust"
series: ["algorithm"]
slug: "nth-prime-number-rs"
aliases: []
toc: true

comment: true
math: true

---

Sieve of Eratosthenes is one of the most adopted algorithm to extract the list of prime number. To get the nth prime we can do better than just to return the nth from Eratosthenes list instead we will modify the algorithm.
<!-- more -->
# TL:DR
In hurry? Directly [Goto solution](#-solution)

## # Problem
Given a number `n` define a function `nth_prime` find the prime number that is at index `n` (counted from 1) in the prime number list.
Eg:
```
nth_prime(1)    == 2;
nth_prime(4)    == 7;
nth_prime(261)  == 1663;
nth_prime(10000) == 104729;
```

## # Why Not classic Eratosthenes
While the algorithm has proven itself to be one of the best algorithm to find the prime number when provided the upper limit but providing the index of the prime number instead of limit invites following bottelneck. Just to remember this is what function signature of Eratosthenes alogrithm looks like
```rust
fn (upper_limit: u64) -> Vec<u64>
```
* The resulting type is the vector (although we can easily index to `n`)
* We have nothing to pass as `upper_limit` because what we have is the index of prime number
* Even if we calculated the upper bound we will be using `n*8` bytes for `u64`  or  `n*16` bytes of memeory if used `u128` just to store the number  whereas this modification use  `n*1` because `sizeof::<bool>()` is always 1 byte so we wil be saving `n*7` or `n*15` bytes
## # Solution
Given program is written using [rust](/tags/rust) language although implementing in other language is not a big deal
```rust
pub fn nth_prime(index: u64) -> u64 {
    if index == 0 {
		panic!("Invalid index...");
	} else if index == 1 {
        return 2;
    } else if index == 2 {
        return 3;
    }
    let floating_index = index as f64;
    let factor = floating_index.log(E) + floating_index.log(E).log(E);
    let mut primes: Vec<bool> = vec![true; (floating_index * factor) as usize];

    let mut prime = (0, 0);
    for i in 2_u64.. {
        if primes[i as usize] {
            prime = (prime.0 + 1, i);
            if prime.0 == index {
                return i;
            }
            (i * i..primes.len() as u64)
                .into_iter()
                .step_by(i as usize)
                .for_each(|j| {
                    primes[j as usize] = false;
                })
        }
    }
    unreachable!();
}
```
###  Validation
Above alogirthm is to be verified writing unit tests. The actual value that is tested is brought from several online sources which are proven to be valid. Also that the test `prime_above_1000` is marked as `#[ignore]` because large indexing may take time so to manually run ignore test use `cargo test testing::prime_above_100`
```rust
#[cfg(test)]
mod testing{
    use super::*;
    #[test]
    #[should_panic]
    fn test_valid_index(){
        assert_eq!(nth_prime(0), 1);
    }

    #[test]
    fn prime_upto_100(){
        assert_eq!(2, nth_prime(1));
        assert_eq!(3, nth_prime(2));
        assert_eq!(71, nth_prime(20));
        assert_eq!(283, nth_prime(61));
        assert_eq!(541, nth_prime(100));
    }

    #[test]
    fn prime_upto_1000(){
        assert_eq!(2_221, nth_prime(331));
        assert_eq!(2_293, nth_prime(341));
        assert_eq!(3_571, nth_prime(500));
    }

    #[test]
    #[ignore]
    fn prime_above_1000(){
        assert_eq!(19_841, nth_prime(2_244));
        assert_eq!(15_485_863, nth_prime(1_000_000));
        assert_eq!(946_397, nth_prime(74_654));
    }
}
```
### Possible obtimization
* __More precise `factor`__:  Calculation. Factor `f` defines such that `nth_prime(n) < k`.  So calculating `f` as near as possible to `nth_prime(n)` will save space as well as avoid quite noticeable number of loops while indexing large primes. Possibly can be:
<!-- n (log n + log log n - 1 + (log log (n) - 2)/log n - ((log log (n))2 - 6 log log (n) + 11)/(2 log2 n)) -->
$$ n\times \left( {\log n + \log \log n - 1 + {\frac {\log \log n - 2} {\log n} } - { \frac {(\log \log n)^2 - 6\log \log n + 10.573} {2 \log^2 n} } } \right)$$

* __Caching__: If used in program that request frequent call to `nth_prime` caching the `nth` prime as well as calculated table will save time sacrificing storage (memory)
## # Reference
https://en.wikipedia.org/wiki/List_of_prime_numbers

https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes
