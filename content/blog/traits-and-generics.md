+++
title = "Traits and Generics in Rust"
date = 2020-02-22
hide_word_count = true
hide_reading_time = true
+++
# Traits and Generics in Rust

## Introduction

Many languages, such as Java, C++, and Python have ways of abstracting types so that you can minimize the duplication of your code and make it easier to read. Traits and Generics in Rust allow you to achieve these goals. I will explain these concepts by showing code examples that are implemented with and without Generics and Traits, but achieve the same things.

<!-- more -->
## Adding functionality without Generics 

In the following code example, I created a function that returns the average of a group of numbers that has been passed in as a parameter. 

```rust
fn calculate_average() -> f64{
    let float_list = vec![1.0, 2.3, 4.5, 5.9, 7.2];
    let mut total = 0.0;

    for &item in float_list.iter(){
        total += *&item;
    }

    let mut average = total / float_list.len() as f64;

    average
}
```

Going line-by-line, here is what that function does:
- `fn calculate_average() -> f64{` -- A function is declared with the return value of f64 

