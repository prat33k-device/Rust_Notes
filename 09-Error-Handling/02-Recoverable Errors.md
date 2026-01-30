# Recoverable Errors

1. When something goes wrong but you can handle it and continue the program to running
2. these errors are handled using `Result<T,E>` and `Option<T>` 

## `Result<T, E>` vs `Option<T>`
result returns Ok(T) and Err(T)
Option return Some(T) and None

## The ? Operator for propagating errors

When a function’s implementation calls something that might fail, instead of handling the error within the function itself, you can return the error to the calling code so that it can decide what to do. This is known as _propagating_ the error and gives more control to the calling code, where there might be more information or logic that dictates how the error should be handled than what you have available in the context of your code.

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");

    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();

    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

this can be shortened with ? operator

```rust
use std::fs::File;
use std::io::{self, Read};
// If the value of the `Result` is an `Ok`, the value inside the `Ok` will get returned from this expression, and the program will continue. If the value is an `Err`, the `Err` will be returned from the whole function as if we had used the `return` keyword so that the error value gets propagated to the calling code.
fn read_username_from_file() -> Result<String, io::Error> {
    let mut username_file = File::open("hello.txt")?;   // move ahead of Ok otherwise return Err at this line
    let mut username = String::new();
    username_file.read_to_string(&mut username)?;   // move ahead of Ok otherwise return Err at this line
    Ok(username)  // in the end return the Ok with value
}
```

could even shorten this code further by chaining method calls immediately after the `?`
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username = String::new();
    File::open("hello.txt")?.read_to_string(&mut username)?; // works the same way
    Ok(username)
}
```

>We’re only allowed to use the `?` operator in a function that returns `Result`, `Option`, or another type that implements `FromResidual`.

> Note that you can use the `?` operator on a `Result` in a function that returns `Result`, and you can use the `?` operator on an `Option` in a function that returns `Option`, but you can’t mix and match.


### unwrap_or_else and closures instead of result and match

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
// store the result if Ok, call the closure if Err
    let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {error:?}");
            })
        } else {
            panic!("Problem opening the file: {error:?}");
        }
    });
}
```

### unwrap
```rust
use std::fs::File;

fn main() {
// return Ok or if err than call panic!
    let greeting_file = File::open("hello.txt").unwrap();
}
```

### except
```rust
use std::fs::File;
fn main() {
// works same as unwrap butyou can provide the panic! message
	let greeting_file = File::open("hello.txt").expect("hello.txt should be included in this project");
}
```