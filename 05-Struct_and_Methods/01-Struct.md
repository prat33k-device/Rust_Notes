# Struct

A custom datatype to group multiple data together

- helps organize complex data into single unit making code readable and maintainable
- Provides type safety by ensuring that only valid datatypes are used for each member
- promote reusability and modularity

## Syntax

```rust
struct StructName {
	field1: FieldType1,
	field2: FieldType2.
	// More fields
}
```

Example:
```run-rust {pre}
// Define the struct
struct Rectangle {
	width: u32,
	height: u32
}
```

```rust
fn main() {
	// struct instantiation
	let r1 = Rectangle {
		width: 30,
		height: 50
	};
	
	println!("Width: {}", r1.width);
	println!("Height: {}", r1.height);
}
```

### Field Init Shorthand
```rust
fn main() {
	let width = 30;
	let height = 50;
	let r1 = Rectangle {
		width,
		height,
	};
	// we can omit the value if variable names are same
}
```

### Creating instances from other Instances
```rust
fn main() {
	let r1 = Rectangle {
		width: 30,
		height: 100
	};
	
	let r2 = Rectangle {
		width: 60, // explicitly defined
		..r1      // this extracts the value of height: 100 from r1
	};
	
	// r2.width -> 60 
	// r2.height -> 100 (same as r1)
}
```

## Ownership of Struct

- By Default, rust treats all types that don't define **Copy** trait as Movable
- All structs we defined so far follow the Ownership rules that we covered in [[01-Ownership]]

### Ownership Transfer
  ```run-rust
  fn main() {
	  let r1 = Rectangle {
		  width: 20,
		  height: 50,
	  };
	  
	  let r2 = r1;  // Move ownership from r1 to r2
	  // now r1 is no longer valid
	  
	  println!("width: {}, height: {}", r2.width, r2.height);
  }
  ```

### Borrow Ownership (Immutable References)
```run-rust
fn main() {
	let rect1 = Rectangle {
		width: 20,
		height: 30,
	};
	
	print_dimentions(&rect1); // Borrow rect1
	
	// rect1 still valid
	println!("rec1 height: {}", rect1.height);
}

fn print_dimentions(r: &Rectangle) {
	println!("Dimesions: {}X{}", r.width, r.height);
}
```

### Borrow Ownership (Mutable References)
```run-rust
fn main() {
	let mut rect1 = Rectangle {
		width: 20,
		height: 30,
	};
	
	modify_dimentions(&mut rect1); // Borrow rect1
	
	// rect1 still valid
	println!("Modified Dimesions: {}X{}", rect1.width, rect1.height);
}

fn modify_dimentions(r: &mut Rectangle) {
	r.width = 100;
	r.height = 100;
}
```

### Structs with non Copy Fields
```run-rust
struct Owner {
	name: String
}

struct House {
	owner: Owner,
	rooms: u32
}

fn main() {

	let owner1 = Owner {
		name: String::from("Alice")
	};
	
	let house1 = House {
		owner: owner1,    // move owner1 to owner
		rooms: 5
	};
	
	// now owner1 become invalid
	// println!("{}", owner1.name);  // Error
	
	println!("owner name: {}, rooms in house: {}", house1.owner.name, house1.rooms);
}
```

## Printing a struct
```rust
fn main() {
	let r1 = Rectangle {
		width: 10,
		height: 20
	};
	
	// println!("rect: {}", r1); // Error, can't use println! because Rectangle does not implement the Display trait
	
}
```

- In Rust, if you try to `println!("{}")` something, then compiler expects it to implement the **std::fmt::Display trait**
- Similarly, if you try to `println!("{:?}")` or `println!("{:#?}")`something for pretty print, then compiler expects it to implement the **std::fmt::Debug trait**

A Very Easy way for now to implement the Debug trait
```run-rust

//derived debug attribute which auto generates the Debug attributes
#[derive(Debug)]
struct Point {
	x: i32,
	y: i32
}

fn main() {

	let p1 = Point {
		x: 2,
		y: 3
	};
	
	 println!("p1: {:?}", p1);
}

```

