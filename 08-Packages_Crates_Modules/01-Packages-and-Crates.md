# The Module System in Rust

1. **Packages**: A cargo feature that lets you build, test and share *crates*
2. **Crates**: A tree of modules that produces a library or executable
3. **Modules and use**: Let you control the organization, scope and privacy of paths
4. **Paths**: A way of naming a item, such as a struct, function, or module

# 1. Crates

1. A Crates is the smallest amount of code that rust compiler considers at a time, when you run a .rs script the compiler consider it a a crate 
2. Crates can contain modules and the modules may be defined in other files that get compiled with the crate
3. A crate can come in 2 forms: a *binary crate* or a *library crate* 
4. **Binary Crate** are programs you can compile to an executable that you can run, such as a command line program or a server. Each **must have a main function** that defines what happens when the executable runs. All the crates we have created so far are binary crates
5. **Library Crate** don't have a main function, and they don't compile to an executable. Instead, they define functionality intended to be shared with multiple projects. for example rand crate to generate random numbers
6. Most of the time when Rustaceans say crate, they mean library create
7. The **Crate Root** is a source file that Rust compiler starts up and makes up the root module

```bash
cargo new my-project
     Created binary (application) `my-project` package
ls my-project
Cargo.toml
src
ls my-project/src
main.rs

```
> Cargo follows a convention that `src/main.rs` is the crate root of binary crate with the same name as package
> Cargo knows if the package dir contains `src/lib.rs` te package contains a library crate with the same name as package and `src/lib.rs` will be crate root
> Cargo passes the crate root files to rustc to build library and binary

## binary crate example
```bash
cargo new example-project
```
```rust
// src/main.rs file
fn main() {
    println!("This project is created by cargo new <projet-name> command, and it's a binary crate project");
}
```

## library binary crate example
```bash
cargo new --lib text-magic
```
```rust
// src/lib.rs file
// This project is created by cargo new --lib <projet-name> command, and it's a library crate project

pub fn reverse(input_string: &str) -> String {
    input_string.chars().rev().collect()
}
pub fn is_palindrome(input_string: &str) -> bool {
    let cleaned_input_string: String = input_string
    .chars()
    .filter(|c| c.is_alphabetic())
    .collect::<String>();

    cleaned_input_string == reverse(&cleaned_input_string)
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_reverse() {
        assert_eq!(reverse("wizard"), "draziw");
    }
    #[test]
    fn test_is_palindrome() {
        assert!(is_palindrome("racecar"));
        assert!(!is_palindrome("false"));
    }
}
```

## Working with dependencies

Either run
```
cargo add regex
```
or add following to Cargo.toml file
```
[dependencies]
regex = "1.11.0"
```

```rust
// src/main.rs
use regex::Regex;
fn main() {
    let example_email: &str = "example@example.com";
    let email_pattern: &str = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$";
    let re: Regex = Regex::new(email_pattern).unwrap();
    if re.is_match(example_email) {
        println!("{} is a valid email address ✅", example_email);
    } else {
        println!("{} is not a valid email address ❌", example_email);
    }
}
```


# 2. Packages

1. Helps manage larger rust projects effectively
2. A package is a bundle of of one or more crates that provides a set of functionality
3. Every package contains `Cargo.toml` which is the manifest file that describes how to build those crates, manages dependencies and contains metadata
4. A package can contain as many Binary crates you like, but at most only 1 library crate
5. A package must contain at least 1 crate, either binary or library

## 