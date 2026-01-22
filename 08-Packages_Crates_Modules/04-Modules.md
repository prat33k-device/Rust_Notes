# Modules

1. way to organize your code into separate namespaces
2. helps group related items like functions, structs, enums and constants
3. **Encapsulation**: Modules allow you to encapsulate functionality, controlling what is exposed to other parts of the program or external crates
4. **Namespace Management**: Modules create namespaces that prevent naming conflicts, allowing you to use the same name in different modules without clashes
5. **Hierarchical Organization**: Modules can be nested within each other, creating hierarchy that mirrors your program's structure

## inline module
```rust
mod math {
	pub fn add(x: i32, y: i32) -> i32 {
		x + y
	}
	pub fn substract(x: i32, y: i32) -> i32 {
		x - y
	}
}

fn main() {
	let addition = math::add(5, 4);
	let substraction = math::substract(3, 8);
	
	println!("add: {}, sub: {}", addition, substraction);
}
```

## module in different file
```rust
// src/math.rs
pub fn add(x: i32, y: i32) -> i32 {
	x + y
}
pub fn substract(x: i32, y: i32) -> i32 {
	x - y
}
```
```rust
// src/main.rs
mod math;
fn main() {
	let addition = math::add(5, 4);
	let substraction = math::substract(3, 8);
	
	println!("add: {}, sub: {}", addition, substraction);
}
```


