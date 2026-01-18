# Enums

1. Enums or enumerations are a powerful feature in Rust that allow you to define a type by enumerating its possible vales
2. Makes your code more expressive and type safe

## Basic Concept

A enum is like a list of possible options that a value can be.   
Each option is called a **variant** 
### Some important enums in Rust's standard library

#### 1. `Option <T>`
1. Represents an optional value
2. Two Variants
	1.  `Some(value)` Contains a value
	2. `None` No value is present 
3. Option type is used to represent a value that can either be present (Some(T)) or absent (None)
4. Helps avoid runtime errors when a value might be absent
5. Example divide where the denominator might be zero

#### 2. `Result <T><E>`
1. Used for returning and propagating errors
2. Two variants
	1. `Ok(value)` Operation success, returns a value
	2. `Err(error)` Operation failed, returns an error

## Syntax with Examples

### Basic example
```rust
#[derive(Debug)]
enum Direction {  // the instances of this enum can have any value out of the defined variants i.e North, South, East, West
	// variants
	North,
	South,
	East,
	West
}

fn main() {
	
	let dir1 = Direction::North;
	let dir2 = Direction::South;
	
	println!("{:?}", dir1);
	println!("{:?}", dir2);
}
```

> if you're not using a variable `var1` and it's throwing you an error, change it to `_var1`
### Enum variants with data
```rust
enum Message {
	Quit, // this variant contains no data
	Move {x: i32, y: i32}, // contains 2 named fields x y. This is like a struct embedded in an enum variant
	Write(String), // this variant is called Write which holds a String
	ChangeColor(i32, i32, i32) // holds 3 i32 represents rgb data
}

fn main() {

	let _msg1 = Message::Quit;
	let msg2 = Message::Move {x: 10, y: 20};  // initilize it just like struct
	let _msg3 = Message::Write(String::from("Hello"));
	let _msg4 = Message::ChangeColor(255, 255, 255);
	
	// enums are often used with pattern matching
	match msg2 {
		Message::Quit => println!("Quit"),
		Message::Move {x, y} => println!("Move to ({}, {})", x, y),
		Message::Write(text) => println!("Write: {}", text),
		Message::ChangeColor(r, g, b) => println!("ChangeColor: rgb({}, {}, {})", r, g, b)
	}
	
}

```

