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
## Creating struct instances without Generics 

In the following code example, I will show how a struct can be created that holds a ZooAnimal. An enum for ZooAnimals has also been created in order to hold the different variants (types) of ZooAnimals:  

```rust
enum ZooAnimals{   //ZooAnimals enum that hold different varieties of animals.
    Tigers,
    Bears,
    Lions,
    Monkeys,
    Fish
}

struct ZooAnimal{        // This struct is the blueprint that holds one ZooAnimal variety.
    species: ZooAnimals, // The 'species' attribute will only accept values that exist in the ZooAnimals enum.
    weight: i32,         // The weight can be any i32 value.
}
```

Now, I can create an instance of a ZooAnimal like this:

```rust
let tigers = ZooAnimal{        // I create an instance of the ZooAnimal struct, and assign it to 'tigers'.
  species: ZooAnimals::Tigers, // I use the 'ZooAnimals::Tigers' variant of the ZooAnimals enum to create the instance. 
  weight: 600,
};
```
This code works fine if all you want to create are ZooAnimals, but what if you wanted to create an instance of ANY type of animal? The answer is to use Generics.
 
## Creating struct instances with Generics 

Generics allow us to create data structures that can accept any type, or subset of a type. Any name can be used to represent a generic type, but the convention in Rust is to use the letter 'T' (meaning type) for any piece of code that accepts a single generic type. If there is more than one generic type needed, other single letters should be used. In a struct, the type parameter for the generic type must be declared in angle brackets after the name of the struct:

```rust
struct MyStruct<T>{
}
```

Then, inside the code block you can use the generic type anywhere you want. In a struct, you would use it to assign a value to an attribute:

```rust
struct MyBetterStruct<T>{
    x: T                   // Whatever value is in 'T' will be assigned to x
}
```

Now that that is covered, we can convert our ZooAnimal struct into a struct that holds any type of animal:

```rust
struct AnimalGeneric<T, U>{  //Two generic parameters are used.
    species: T,
    weight: U,
}
```
This struct holds any type of animal, which could be a String name, a different enum, or anything else! It could also accept any number value for the weight (i32, f32, f64, etc.).

## Traits

Traits are a way to define a contract of behaviour for a struct without needing to provide an implementation. Following our zoo animal example, we can create a trait called Sound that defines the sound an animal will make when the method is called.

```rust
trait Sound{                    //Sound trait defined
    fn make_sound(&self){       //make_sound method defined with default behaviour.
        println!("silence...")
    } 
}
```

Now we have to implement the Sound trait on something... let's implement it on the AnimalGeneric struct. While we are at it, lets also add a third attribute to AnimalGeneric-- sound:

```rust

impl Sound for AnimalGeneric<T, U, Y>{     //The Sound trait has been implemented on the struct
    fn make_sound(&self) -> String {
        println!("The {} makes a sound of {}", self.species, self.sound)  //The parameters of the AnimalGeneric instance will be passed into the method when it is created.
    }
}

struct AnimalGeneric<T, U, Y>{
    species: T,
    weight: U,
    sound: Y    //'sound' attribute is added
}
```
All that's left to do now is create an instance of the AnimalGeneric struct so that we can call the method on it:

```rust
let dog = AnimalGeneric{species: "dog", weight: 60, sound: "bark"};
dog.make_sound(); //'bark' is printed to the screen!
```

 
If we call make_sound() on an instance of a generic giraffe with no sound supplied, the default implementation that we defined earlier on the trait is used:

```rust
let giraffe = AnimalGeneric{species: "giraffe", weight: 800, sound: ()}; 
giraffe.make_sound(); //'silence...' is outputted, because giraffes are inaudible to humans.
```  

And then finally, creating an instance of one of the ZooAnimals with a sound supplied will work as expected:

```rust
let lion = ZooAnimal{species: ZooAnimals::Lions, weight: 500, sound: "roar"};
lion.make_sound(); // 'roar' is outputted
```
## Conclusion
So as you can see, using Traits and Generics in your code will make it a lot more flexible and should lead to less code duplication. They can also be used to program in an Object-Oriented style, to make the transition easier from languages such as Java.
One thing I want to mention is that you *may* have issues with printing to the screen depending on the Traits and Structs you are using. Please check here if you are having problems: [display formatting](https://doc.rust-lang.org/rust-by-example/hello/print/print_display.html)  

Thank you for reading!

![Ferris](/images/ferris.gif "Ferris the crab")

### Stay Rusty!

#### See more at the [Official Rust documentation](https://doc.rust-lang.org/book/ch10-00-generics.html)
