---
title: "Using Rc in Rust - Simplified"
keywords: ["rust", "smart pointers", "std crate"]
date: 2021-07-02T11:59:35+05:45
tags: ["rust", "programming", "smart-pointers", "std-rc"]
categories: ["technical"]
description: "Here we will see why shall we use Rc with a simple example"
series: ["learning-rust"]
slug: "rc-in-rust-simplified"
aliases: []
images: []
toc: true

comment: true
# math: false
# draft: true
---

The type `Rc<T>` provides shared ownership of a value of type `T`, allocated in the heap. Here we are going to look why we need Rc in Rust and using it to write a very simple example.
<!--more-->

![Rust crab looks happy](/images/featured-images/happy-rust-crab.png)

## # Word of Horror :laughing:
If you found this article a bit lengthy, Easy. It is actually super easy It just happens that I can't do less wordy.


## # What are we solving
Consider an function named `print_it` that simply print whatever it gets. 
```rust
use std::fmt;

fn print_it<T: fmt::Display>(val: T) {
	println!("{}", val)
}

fn main(){
	let greet = "Hello, world!".to_string();
	print_it(greet);
}
```
Above program simply create a String `greet` pass it to `print_it` function which in turn just print the value. One thing worth to quite noticing is that, `print_it` function takes the ownership of whatever it gets. We are not implementing to avoid just for learning `Rc` thing shake.

Now let's add few more easy lines so the total code looks like
```rust
use std::fmt;

fn print_it<T: fmt::Display>(val: T) {
	println!("{}", val);
}

fn main(){
	let greet = "Hello, world".to_string();
	let borrowed = &greet;
	print_it(greet);
	print_it(borrowed);
}
```
Above program yields an error which is quite fair because we are violating the basic rule of rust ownership. Lets break down whats happening
1. we are creating a `String` on variable `greet`
2. We are creating a reference `borrowed` which reference to `greet`
3. Next, We are printing the `greet` variable. You might have already notice that, here `greet` is moved and it's ownership is transferred to function parameter `val`
4. In function `print_it`, we printed it and in the end of this function, ownership of `val` is dropped (which is originally the ownership of `greet`) 
5. As `greet` ownership is done so the borrowed is now pointing to a dropped variable which makes it a dangiling reference.
6. In line `print_it(borrowed)` we are now actually accessing a dangling reference which makes compiler to raise an error.

*Fair Enough?*
But Here we are pretty sure that we are not doing anything bad. Simply we just wanted to print but we can't yell at compiler and hope it will allow us to do so.
Fortunately, we got `Rc` (without yelling :wink:)

---
## # How are we solving
```rust
use std::fmt;
use std::rc::Rc;

fn print_it<T: fmt::Display>(val: T) {
	println!("{}", val);
}

fn main(){
	let greet = Rc::new("Hello, world".to_string());
	let borrowed = Rc::clone(&greet);
	print_it(greet);
	print_it(borrowed);
}
```
*Easy! Right?*
Let me quickly say what We have done although I would not rewrite everything that you can get from doc or The Book.
We brought `Rc` into scope using `use` statement. We created a Rc type with associated function `::new()` with our original string. Then, we created a reference using another associated function `::clone()`to original `greet: Rc` (which is not same as primitive reference like `&greet`). And we tried to print both original and referenced variable using `print_it` function.

Again, you may want to head to the documentation or book's chaper 15 (Basic information was easy to grab. It was program that I thought worth explaining).

But wait! I cheated there. If you updated the main function adding two more `print_it` statement like
```rust
fn main(){
	/* Everything else is same as above */
	print_it(greet);
	print_it(greet);
}
```
You will get the same error we were trying yo solve at very first place i.e using reference when original variable's ownership has ended.
It is because when we did `print_it(greet)` our greet was moved (In our first program the string itself was moved but unlike that here the container of type `Rc` is moved). It's a simple fix instead we would so something like
```rust
print_it(Rc::clone(&greet));
print_it(Rc::clone(&greet));
print_it(Rc::clone(&borrowed));
```
Not everything is fine.
What? Am I cheating again? Not really but it seems like I have cheated.
Let's recall our original first attempt which was
```rust
let greet = "Hello, world".to_string();
let borrowed = &greet;
print_it(greet);
print_it(borrowed);
```
If we had to call `clone` method anyway as we did in last program using `Rc` then why not do `clone` on string itself like
```rust
print_it(greet.clone());
print_it(borrowed);
```
This also does what we intended to and we will also get absolute same outcome.

But here's a thing that summarizes the whole point of this article, so let me bold this 

>Calling `clone` in `String` itself copies the data it's holding whereas, 
Calling `clone` in `Rc` does not copy the content of what it holds but instead increase the reference count

So yes, If we are dealing with large string (or something else on heap) and did `greet.clone()` It have to copy everything which results in expensive operation  of O(n) whereas `Rc::clone(&greet)` just increase the reference count and does that in O(1)

---
## # Takeaway
So at last Take our full program
```rust
use std::fmt;
use std::rc::Rc;

fn print_it<T: fmt::Display>(val: T) {
	println!("{}", val);
}
fn main(){
	let greet = Rc::new("Hello, world".to_string());
	let borrowed = Rc::clone(&greet);
	print_it(Rc::clone(greet));
	print_it(Rc::clone(borrowed));
}
```
---
### # If `Rc` is nice, Who created the Earth?
Woh. That's a big question. We are done.
