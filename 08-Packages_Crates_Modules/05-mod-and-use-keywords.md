
In Rust, **`mod`** is for **declaring/creating a module**, and **`use`** is for **bringing names into scope** (so you can refer to them more easily).

---

## `mod` keyword (defines a module)

### 1) Inline module

`mod math {     pub fn add(a: i32, b: i32) -> i32 {         a + b     } }`

Now you access it like:

`let x = math::add(2, 3);`

### 2) Module in another file

If you write:

`mod math;`

Rust will look for the module code in:

- `math.rs` OR
    
- `math/mod.rs` (older style)
    

So `mod` basically means: **“this module exists, compile it”**.

---

## `use` keyword (imports names into current scope)

Example:

`use std::collections::HashMap;  fn main() {     let mut m = HashMap::new(); }`

Without `use`, you’d have to write:

`let mut m = std::collections::HashMap::new();`

So `use` means: **“I want to refer to this path directly here”**.

---

## Common difference in one line

`mod a;        // declares module a (loads/compiles it) use a::func;  // imports func into current scope`

---

## Extra important points

### `mod` does NOT import anything

Even if you declare a module, you still need the full path unless you use `use`.

### `use` does NOT create a module

If the module doesn’t exist, `use something::x;` will fail.

---

## Quick summary

- **`mod`** → _creates/declares module structure_
    
- **`use`** → _makes paths shorter by importing names_