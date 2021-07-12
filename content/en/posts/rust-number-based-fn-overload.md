---
title: "Function overloading in rust based on number of argument"
keywords: ["rust", "sudip"]
date: 2021-07-12T12:00:00+05:45
tags: ["rust", "rust-macro", "function-overloading"]
categories: ["technical"]
description: "Function overloading in rust using declarative macro"
series: ["learning-rust"]
slug: "rust-number-based-fn-overload"
aliases: []
images: ["images/featured-images/featured-images/happy-rust-crab.png"]
toc: true
comment: true

---
Explore the basic idea of function overloading based on number of arguments in rust. For this purpose we make use of declarative macros to implement a basic overloaded add function.
<!--more-->
![Rust crab looks happy](/images/featured-images/happy-rust-crab.png)

## # Function overloading in general
In very simple terms, function overloading is the facility provided by programming language to define and declare the function with same name but differing on type signature or number of argument.

### Function overloading based on number of argument
This type of overloading simply means there exist multiple function of same name but they recieve different number of argument. Below is the example of such overloading in `C`
```c
int add(int a, int b){
	return a + b;
}
int add(int a, int b, int c){
	return a + b + c;	
}
```
Here we defined two function with same name still this is a valid c program and we can do something like
```c
printf("Sum is: %d", add(10, 20));
printf("Sum is: %d", add(20, 30, 40));
```
Here, we are going to look at the function overloading based on number of argument only.

## # First attempt in rust
Lets just fire-up the editor and try to achieve same in in Rust. First attempt of mine looked something like this:
```rust
fn add(a: i32, b: i32) -> i32 {
	a + b
}
fn add(a: i32, b: i32, c: i32) -> i32 {
	a + b + c
}

fn main(){
	println!("Sum is: ", add(10, 20));
	println!("Sum is :", add(20, 30));
}
```
It is just same as above `C` program where we define two `add` function that takes 2 and 3 `i32` as argument. Tried to compile above program. Yes I got the same error message which at core states:
```
note: `add` must be defined only once in the value namespace of this module
```
Rust simply don't allow such function overloading yet (well at least in that simple way). Rust require every function in a scope must be of unique name and above we are straightly not following this.
## # Get hands dirty with declarative macros
Macros in rust are awesome? yes it is. The idea to use macros to achieve argument number based function overloading is to define pattern that has varying number of argument and assign it to the block expression that performs the required operation. General idea to do such is
```rust
macro_rules! function_name{
	($a: expr, $b: expr) => {{
			// operation to do when
			// 2 argument are recived
	}};
	($a: expr, $b: expr, $c: expr) => {
		{
			// Operation to do when
			// 3 arguments are recived
		}
	}
}
```
Keeping above pattern in mind let's overload our `add` function using macros
```rust
macro_rules! add{
	($a: expr, $b: expr) => {
		{ $a + $b }
	};
	($a: expr, $b: expr) => {
		{ $a + $b + $c }
	}
}

fn main(){
	println!("Sum: {}", add!(10, 20));
	println!("Sum: {}", add!(20, 30));
}
```
Now just run the above program and everything works fine as expected.

### What we did?
* Firstly, We defined a macro named  `add`. Notice that as this is macro so when we are calling it we used `add!()` instead of `add()`. Simply, `!` indicated that we are calling (more precisely expanding) the macro.
* Inside macro definition, We added to arm which roughly matches the `match` pattern as
```rust
pattern_to_match => code_to_execute
```
* In pattern matching side we did `($a: expr, $b: expr). `
	* `$a` indicates the variable name`just like argument name in function definition.
	* `: expr` means that the variable is of type expression (that yields some value). More info: [expression - rust reference](https://)
	* then we added `,`. We could have even replaced the comma with any other valid rust token like `=>` or `;` because well macro (at least declarative) are matching the syntax and replacing it with per defined code. then our macro call would look like `add!(10 => 20)` and `add!(10; 20)` respectively. We choose `,` because we want it to match function call syntax.
* Then the `{ }` simply wrap the operation we want to execute
	* Yet there is another `{}` which means we want to wrap this to where it is to be replaced. If we omit the second `{}` then in this case the code inside first `{}` should be single expression.
* Statement `$a + $b` is the operation that we wanted to execute. And same goes for arm with 3 argument `($a: expr, $b: expr, $c: expr)`

This is the simplest example I came up with to show that overloading of function based on number of argument can be achieved by using macro. Note that there are many rooms of improvement in above implementation.

##  # Improved implementation
One most notably is that we have to define several arm based on no of parameter which is tedious if we want to overload for parameter count `1` to let's say `100` then it is of course very much tedious, ugly and repeating. For that we can use reputation expatiation feature of macro which may goes like:
```rust
macro_rules! add{
	($($a: expr),+) => {{
		[$($a),+].iter().sum()::<i32>()
	}}
}
fn main(){
	println!("{}", add!(10, 20));
	println!("{}", add!(10, 20, 30, 40));
}
```
This implementation is rather short but can accept any number of argument which maybe bit tricky and complicated. This use the repetition `($($a),+)` feature of macro and create a array of all argument and return `.sum()` which is exactly what we wanted. If you are not much familiar with macro or above syntax in general I recommend exploring and playing a bit with macros and again come back here which will then make a lot of sense.

## # Final words
In this article we have seen that although rust naively does not support function overloading, we can still achieve the functionality using macros pretty easily.
