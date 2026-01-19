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

### Enum variants with data
```rust
enum Animal {
	Dog(String),
	Cat(String),
	Bird(String)
}

fn sound(animal: Animal) -> &'static str {
	match animal {
		Animal::Dog(_)  => "Bark",    // returns the ref to static str
		Animal::Cat(_)  => "Meow",   // _ means wildcard, ignore the data
		Animal::Bird(_) => "Tweet",
	}
}

fn main() {
	let pet1 = Animal::Dog(String::from("Buddy"));
	let pet2 = Animal::Cat(String::from("Wiskers"));
	let pet3 = Animal::Bird(String::from("Tweety"));
	
	println!("Pet1 makes a sound: {}", sound(pet1));
	println!("Pet2 makes a sound: {}", sound(pet2));	
	println!("Pet3 makes a sound: {}", sound(pet3));		
}

```

### Methods in enums
```rust
enum TrafficLight {
	Red,
	Yellow,
	Green,
}

impl TrafficLight {

	fn duration(&self) -> u32 {
		match self {
			TrafficLight::Red => 120,
			TrafficLight::Yellow => 5,
			TrafficLight::Green => 30,
		}
	}

}

fn main() {
	let red_light = TrafficLight::Red;
	let yellow_light = TrafficLight::Yellow;
	let green_light = TrafficLight::Green;
	
	println!("red light duration: {}", red_light.duration());
	println!("yellow light duration: {}", yellow_light.duration());
	println!("green light duration: {}", green_light.duration());
}
```

### `Option<T>` example
```rust

fn divide(x: f32, y: f32) -> Option<f32> {
	if y == 0.0 {
		None
	} else {
		Some(x / y)
	}
}

fn main() {
	let x = 6.0; let y = 0.0;
	let result = divide(x, y);
	
	match result {
		Some(value) => println!("divide result: {}",  value),
		None        => println!("Cannot divide by Zero"),
	}
}

```

### example with file error handling
```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
	
	let file_result = File::open("hello.md");
	
	match file_result {
		Ok(file)   => println!("File Opened Successfully: {:?}", file),
		Err(error) => match error.kind() {
			ErrorKind::NotFound => println!("File Not Found"),
			other_error         => println!("Error opening file: {:?}", other_error),
		}
	}
	
}
```

### Enums with Structs
```rust
use std::fmt;

enum JobStatus {
	Applied,
	Interviewing,
	Offered,
	Rejected,
}

struct Candidate {
	name: String,
	status: JobStatus
}

impl Candidate {
	fn new(name: String, status: JobStatus) -> Candidate {
		Candidate { name, status }
	}
	
	fn get_status(&self) -> &str {
		match self.status {
			JobStatus::Applied => "Applied",
			JobStatus::Interviewing => "Interviewing",
			JobStatus::Offered => "Offered",
			JobStatus::Rejected => "Rejected",
		}
	}
	
	fn update_status(&mut self, status: JobStatus) {
		self.status = status
	}
}

impl fmt::Display for Candidate {
	fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		write!(f, "Candidate: '{}' have status: '{}'", self.name, self.get_status())
	}
}

fn main() {
	let mut candidate1 = Candidate::new(String::from("John"), JobStatus::Applied);
	
	println!("{}", candidate1);
	candidate1.update_status(JobStatus::Interviewing);
	println!("{}", candidate1);
	candidate1.update_status(JobStatus::Rejected);
	println!("{}", candidate1);
	candidate1.update_status(JobStatus::Offered);
	println!("{}", candidate1);
}
```


## read book and comeback
