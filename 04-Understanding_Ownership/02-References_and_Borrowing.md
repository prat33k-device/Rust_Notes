# Variable Scope

```rust
fn main() {
	let x = 5; // x comes into scope
	
	println!("{x}");
} // x goes out of scope, memory drop, Rust calls a special function 'drop'
```


# Move (Ownership transfer)

```rust
fn main() {
	let s1 = String::from("Hello"); // s1 comes into scope
	let s2 = s1; // s1 moved to s2, s1 goes out of scope, s2 comes into scope
	// This is called OWNERSHIP TRANSFER
	// but now we can't access s1
}
```


