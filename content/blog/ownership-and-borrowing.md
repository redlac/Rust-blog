+++
title = "Ownership and Borrowing in Rust"
date = 2020-01-26
hide_word_count = true
hide_reading_time = true
+++
# Ownership and Borrowing in Rust

## Introduction

Rust is a very unique programming language. It gives you low-level control over your code and the ability to manage 
memory without allocating it or de-allocating it with specific commands (although that is also possible with the 
[unsafe](https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html?highlight=unsafe#unsafe-rust)
keyword). One of the ways that Rust can guarantee memory safety without a garbage collector is by implementing the 
concepts of *ownership* and *borrowing* into the language.

<!-- more -->
## What is Ownership?

Ownership is the way that Rust manages memory. It is a set of rules that the compiler says must be followed in for your 
code to compile.

The very basic ownership rules in Rust are:
* Every value is assigned to a variable, and that variable is the value's owner.
* Every value can only have ONE owner at any given time.
* When the owner variable goes out of scope, a special function named `drop` is called and its value is dropped.

Let's define a couple of those terms mentioned:

**_scope_** refers to the lines in the program where a variable is valid.

**_drop_** is a function that Rust calls on variables allocated to the heap, that frees up memory for that variable when 
it goes out of scope. 

In this very basic example, the variable `dog_owner` is the owner of the String `"dog"`:
 
```rust
{
    let dog_owner = String::from("dog"); //'let' is the keyword used to declare variables in Rust
}
```

When the program reached the `}`, the variable `dog_owner` goes out of scope and `drop` is called on it to free the memory 
it had allocated to it.  

In the next example, the ownership of the String `"dog"` is moved from `dog_owner` to `dog_trainer`: 

```rust
{
    let dog_owner = String::from("dog"); //This line creates a String type from the string primitive "dog"
    let dog_trainer = dog_owner;
}
```

At this point, the variable `dog_owner` is no longer valid. We can verify this by trying to print out the value of 
`dog_owner`: 

```rust
{
    let dog_owner = String::from("dog");
    let dog_trainer = dog_owner;
    println!("{}", dog_owner); //println!() is used to print values to the system output
}
```

In other languages, you would expect that `dog` would be printed to the console, but in Rust this creates an error:
```rust
error[E0382]: borrow of moved value: `dog_owner`
  --> src\main.rs:14:24
   |
12 |         let dog_owner = String::from("dog");
   |             --------- move occurs because `dog_owner` has type `std::string::String`, which does not implement the `Copy` trait
13 |         let dog_trainer = dog_owner;
   |                           --------- value moved here
14 |         println!("{}", dog_owner); //println!() is used to print values to the system output
   |                        ^^^^^^^^^ value borrowed here after move

error: aborting due to previous error
```

In the console, the error line `value borrowed here after move` means that we tried to **_borrow_** the variable 
`dog_owner` by passing it into the `println!()` macro, but it was already moved to `dog_trainer` so this is not allowed. 
**_Borrowing_** will be explained in the next section.

## What is Borrowing?

To start, let us define what **_references_** are: a reference is a variable that refers to another value, but does not 
have ownership of it. When we pass a reference variable into a function, it is said that the function is **_borrowing_** 
that variable.

For example, the function `rent_movie()` is borrowing a reference to the `movie` variable:

```rust
fn main(){
    let movie = String::from("Star Wars");
    rent_movie(&movie);
}

fn rent_movie(movie: &String) -> &String{ //Take a String reference as a parameter, and return a String reference
    String::from("rented") //The String "rented" is returned from the function. 
}
```

So far, so good. But what if we try to modify the `movie` in `rent_movie()`?

```rust
fn main(){
    let movie = String::from("Star Wars");
    rent_movie(&movie);
}

fn rent_movie(movie: &String) -> &String{ //Take a String reference as a parameter, and return a String reference
    movie.push_str(": Director's Cut"); //attempting to create the String "Star Wars: Director's Cut"
    String::from("rented") //return "rented" 
}
```

This will not compile! As shown in the following error message, references are immutable in Rust:

```rust
error[E0596]: cannot borrow `*movie` as mutable, as it is behind a `&` reference
  --> src\main.rs:34:5
   |
33 | fn rent_movie(movie: &String){
   |                      ------- help: consider changing this to be a mutable reference: `&mut std::string::String`
34 |     movie.push_str(": Director's Cut"); //attempting to create the String "Star Wars: Director's Cut"
   |     ^^^^^ `movie` is a `&` reference, so the data it refers to cannot be borrowed as mutable

error: aborting due to previous error
```

This error illustrates something very important about Rust-- all variable are immutable by default! Typically, you would use a reference variable if you just wanted to read its value and not modify it.
However, this error can be fixed by making `movie` mutable. That is, make it so that its value can be modified. We can do this by adding the `mut` 
keyword to the variable declaration and also make it so that `rent_movie()` accepts a mutable String as a parameter:

```rust
fn main(){
    let mut movie = String::from("Star Wars");
    rent_movie(&mut movie);
}

fn rent_movie(movie: &mut String) -> &mut String{ //Take a mutable String reference as a parameter, and return a mutable String reference
    movie.push_str(": Director's Cut"); //attempting to create the String "Star Wars: Director's Cut"
    movie //return the String "Star Wars: Director's Cut"
}
```

## Conclusion

The main takeaways from this post are: values can be moved to different variables, but can only belong to ONE
 variable at any given time. If you want to temporarily pass the value of a variable to a function, you can pass a reference 
 to that variable to the function, and the function will **_borrow_** the variable and then give it back after the function 
returns. There is a lot more to this topic, but I will leave it there for now!

![Ferris](https://www.rustacean.net/assets/rustacean-orig-noshadow.png "Ferris the crab")

### Get Rusty!

#### See more at the [Official Rust documentation](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)