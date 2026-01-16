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
```rust
// Define the struct
struct Rectangle {
	width: u32,
	height: u32
}

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
  ```rust
  fn main() {
	  let r1 = Rectangle {
		  width: 20,
		  height: 50,
	  };
	  
	  let r2 = r1;  // Move ownership from r1 to r2
	  // now r1 is no longer valid
  }
  ```

### Borrow Ownership (Immutable References)
```rust
fn main() {
	let rec1 = Rectangle {
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
```rust
fn main() {
	let rec1 = Rectangle {
		width: 20,
		height: 30,
	};
	
	modify_dimentions(&mut rect1); // Borrow rect1
	
	// rect1 still valid
	println!("Modified Dimesions: {}X{}", r.width, r.height);
}

fn modify_dimentions(r: &Rectangle) {
	r.width = 100;
	r.height = 100;
}
```

