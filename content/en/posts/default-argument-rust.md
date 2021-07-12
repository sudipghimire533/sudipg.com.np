---
title: "Default argument in Rust"
keywords: ["rust", "function", "function call", "default argument"]
date: 2021-07-14T11:59:35+05:45
tags: ["rust", "programming", "default-argument", "rust-macro"]
categories: ["technical"]
description: "Function with default argument in rust. Default argument allows us to call function omitting some argumnt."
series: ["learning-rust"]
slug: "default-argument-rust-using-macro"
aliases: []
images: ["images/featured-images/happy-rust-crab.png"]
toc: true
comment: true

---
Default argument allows us to call function omitting some paramater. When a argument is omitted provided default paramater is used. While rust does not support default argument we can make use of macro to achive similar functionality.
## # Default parameter in general
Default parameter are the implementation in function where default provided value is used when the caller omit the argument. For simple reference, below is the C++ program with default argument
```c++
#include <iostream>
using namespace std;
int add(int a, int b = 0, int c = 0){
	return a + b + c;
}
int main(){
	cout << "add(10) is " << add(10) << endl;
	cout << "add(10, 20) is " << add(10, 20) << endl;
	cout << "add(10, 20, 30) is " << add(10, 20, 30) << endl;
}
````
In above program we were able to omit passing of `b` and `c` and when omitted their value is default to 0.

 ## # First attempt in rust
But such syntax is not available in rust. If we wanted to do something like above then first attempt maybe as
 ```rust
 // We will be using 0 as default argument
 fn add(a: i32, b: i32, c: i32){
	a + b + c
}
fn add_single(a: i32){
	add(a, 0, 0)
}
fn add_double(a: i32, b: i32){
	add(a, b, 0)
}
fn main(){
	println!("add(10) is {}", add_single(10));
	println!("add(10, 20) is {}", add_double(10, 20));
	println!("add(10, 20, 30) is {}", add(10, 20, 30));
}
 ```
 
 While above program works as expected. The default argument in above call are set to `0` as written in macro block. however, we have to name our function differently thus need to call function of different name for same operation. You may also define all function with same name as described in [Function overloading based on argument count](../rust-number-based-fn-overload) but still we can do it in another way although we will still be using macro.
 
 ## # Solution
 ```rust
 macro_rules! print_this{
	($a: expr) => {
		print_this_raw($a, 0, 0);
	};
	($a: expr, $b: expr) => {
		print_this_raw($a, $b, 0);
	};
	($a: expr, $b: expr, $c: expr) => {
		print_this_raw($a, $b, $c);
	}
}
fn print_this_raw(a: i32, b: i32, c: i32){
	println!("a = {} >> b = {} >> c = {}", a, b, c);
}
fn main(){
	// a = 10, b = 0, c = 0
	print_this!(10);
	// a = 10, b = 20, c = 0
	print_this!(10, 20);
	// a = 10, b = 20, c = 30
	print_this!(10, 20, 30);
}
 ```
 ## # Limitation
 Although above implementation looks more clean than previous one someone may expect to call function with something like
 ```
 // a = 0, b = 10, c = 0
 print_this!(, 10, )
 ```
 Here we are omitting `a` & `c` but passing `b = 10`. With above macro this is not possible. Also in general it is only recommended to omit the trailing parameter but not of middle one. However we can still do such by adding branch in macro as
 ```rust
 macro_rules! print_this{
	// --- skipped code as above
	//
	(, $b: expr, ) => {
		print_this_raw(0, $b, 0)
	}
}
 ```
 Similarly we could add arm to enable calls like
```rust
// Exercise: Write macro arm to allow calls like below

// a = 0. b = 10, c = 20
print_this!(, 10, 20);
// a = 0, b = 0, c = 10
print_this!(, , 10);
```
But this will be tedious so it is usually not recommended nor desired to enable omitting parameter and calling function in every possible permutation. Instead just add few arms that makes sense and is common.
