+++
title = "Building a simple REST API with the Rocket Web Framework"
date = 2020-03-29
hide_word_count = true
hide_reading_time = true
+++
# Building a simple web server with the Rocket Web Framework

## Introduction

Rocket is a web framework written in and designed to be used for the Rust programming language. It has many similarities to web frameworks in other languages, like Flask, Rails, Node.js, and others. Rocket makes it easy to create a simple web server application, while keeping the guaranteed memory safety and security of the Rust language. It abstracts some web server details about routing and HTTP away from the user so that you can get an application up and running quickly. In this post, I am going to show you how to create a simple request/response server that you can interact with through your web browser. Let's begin!

##  Create a new Rust project

First, we need to create a new Rust project. The project will be named 'basic-web'. Navigate the directory where you want the project to be and enter the following two commands in your terminal:

```shell script
cargo new basic-web --bin
cd basic-web
``` 

Next, add the following dependency to the Cargo.toml file in the root of the project directory:

```toml
[dependencies]
rocket = "0.4.4"
```

Rocket requires the nightly version of Rust, which contains more experimental features, so we are going to set it up so that the nightly version is only used on this specific project. Enter the following command in the terminal to do so:

```shell script
rustup override set nightly
```

Once Rust nightly finishes installing, we can start writing the web server!

## Routes and Requests 

Rocket accepts requests by matching the route requested by the client application to the request handler with the matching route. In the main.rs file, copy the following code in:

```rust
#[get("/hello")]              // route attribute
fn world() -> &'static str {  // request handler function
    "hello, world!"
}
```

The line `#[get("/hello")]` defines a route that will be invoked on any HTTP GET request to `http://localhost:8000/hello`. It must be that exact URL, or the server will return an HTTP 404 response.
The function defined will return "hello world" to the client in the response when the request is received.

To make the route handler a little more interesting, change it so that it looks like the following:

```rust
#[get("/hello/<name>")]
fn hello(name: &RawStr) -> String {
    format!("Hello, {}!", name.as_str())
}
```

Now, the route has a dynamic path. The angle brackets in `"/hello/<name>"` mean that any string you place inside of that path on the client will be extracted and passed into the request handler function. In this case, we are displaying the passed in string next to the word "Hello". So the response will change based on the URL path! Once the server is running you will be able to see how it works.

## Serving a file

To make the server more realistic, we are going to add a route handler that will serve an image file from the server, to the client. 
First, place any kind of image file into a directory named `static`. This directory must be at the root of the project.
Next, add the following function to the main.rs file:

```rust

use rocket::response::NamedFile;
use std::path::{PathBuf, Path};

#[get("/<file..>")]
fn files(file: PathBuf) -> Option<NamedFile> {
    NamedFile::open(Path::new("static/").join(file)).ok()
}
```

The first two lines are some necessary import statements. The function `files` will serve a static file hosted in the 'static' folder on the server. The route handler will match any URL at the index of the server, with any file name added on. For example, this URL will serve the file `ferris.jpg` and display it in the browser: `http://localhost:8000/ferris.jpg`.

The server can now serve text and files, let's get it mounted and running!
 
## Mounting

The last thing that needs to happen in order for the web application to function is the request routes need to be mounted to the paths created by Rocket.
Copy the following code into the main.rs file, replacing the main function that was in there:

```rust
fn main() {
    rocket::ignite().mount("/", routes![files])
                    .mount("/hello", routes![world])
                    .launch();
}
```

This code will create an new instance of Rocket with `ignite()`, and mount all of the routes in the supplied Vector at the given base path. The Vector is the `routes![world]` part, which accepts any number of route handling function. We are matching two different routes, the base path of the server at `/`, and the path at `/hello`. `/` will be used to serve the image, and `/hello` will be used to serve a text response. 
The `launch()` function will start the Rocket application server and begin listening for and dispatching requests to the mounted routes.

Finally, add these lines to the main.rs file:

```rust
#![feature(proc_macro_hygiene, decl_macro)]
use rocket::response::NamedFile;

#[macro_use]
extern crate rocket;
```

The `#![feature]` line tells Rust to use feature from the Nightly version of Rust.
The `extern crate rocket` imports the Rocket crate and all of it's macros that are needed to run the server. 

## Starting the server

Now we can start the server! Start the application by typing `cargo run` into the terminal, and you should see something similar to this:

```shell script
🔧  Configured for development.
    => address: localhost
    => port: 8000
    => log: normal
    => workers: [logical cores * 2]
    => secret key: generated
    => limits: forms = 32KiB
    => keep-alive: 5s
    => tls: disabled
🛰  Mounting '/hello':
    => GET /hello/world (world)
🚀  Rocket has launched from http://localhost:8000
```
You can now visit `http://localhost:8000/<name>`, where `<name>` is any name, and the browser should display `Hello <name>`. 
Visit `http://localhost:8000/<image>` where <image> is the file name of the image in the `static` folder. The image should display in the browser, like so:

![screenshot](/images/screen.png "Image being served from localhost:8000/ferris.gif")

## Conclusion
Other things that could be implemented in the server are:
- Streams: Rocket can stream a response to the client (for things such as videos)
- Serializing responses into JSON and other types
- Error handling, with custom responders for 400 and 500 status codes
- And much more...

Thank you for reading!

#### See more at the [Official Rocket homepage](https://rocket.rs/)

![Ferris](/images/dancing-ferris.gif "Ferris the crab")

### Always be rusty!
