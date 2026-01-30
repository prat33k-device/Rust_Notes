# Unrecoverable Errors

1. Represent serious errors and bugs in the code
2. Program cannot continue execution, rust will forcefully stop the program
3. typically handled by `panic!` macro 

## `panic!` macro

 when panic! is called, the program will **stop execution**, **unwind the stack** and **clean up any resources** before aborting

### example when panic! is called
```rust
fn main() {
	let v = vec![1,2,3];
	
	// this will cause a panic!
	println!("4th element of array is: {}", v[3]);
}
```

### unwinding the stack bedore abort
1. Default 
2. rust will walk back up the call stack, cleaning up resources such as memory allocations
3. usefull for debugging
But this can be slow for some performance critical application

### Abort without stack unwinding
in Cargo.toml file
```toml
[profile.release]
panic = 'abort'
```
program will terminate immediately without unwinding which can be faster but leaves less information about what went wrong

### When to use panic!
![[Pasted image 20260127093527.png]]

### Alternatives to panic! - Returning Result or Option

```rust
fn divide(a: i32, b: i32) -> Result<i32, String> {
	if b == 0 {
		return Err(String::from("Division by Zero"));
	}
	Ok(a/b)
}

fn main() {
	match divide(10, 0) {
		Ok(result) => println!("Result: {}", result),
		Err(e) =>  panic!("Error: {}", e),
	}
}
```

### Best practices to use panic!

1. 