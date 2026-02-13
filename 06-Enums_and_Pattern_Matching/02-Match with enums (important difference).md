
Here is the **Rust `match` syntax cheat-sheet** — especially useful for your `enum Command` case 👇

---

## Basic `match` syntax

`match value {     pattern1 => {         // code     }     pattern2 => {         // code     }     _ => {         // default (optional)     } }`

---

## 1. Match **Unit Variant**

### Enum

`enum Command {     List,     Add, }`

### Match

`match cmd {     Command::List => {         println!("List command");     }     Command::Add => {         println!("Add command");     } }`

---

## 2. Match **Tuple Variant**

### Enum

`enum Command {     List(String), }`

### Match

`match cmd {     Command::List(path) => {         println!("Path: {}", path);     } }`

Multiple fields:

`enum Command {     List(String, i32), }  match cmd {     Command::List(path, count) => {         println!("{} {}", path, count);     } }`

---

## 3. Match **Struct Variant** ⭐ (your error case)

### Enum

`enum Command {     List { path: String, count: i32 }, }`

### Match

`match cmd {     Command::List { path, count } => {         println!("{} {}", path, count);     } }`

Rename variables:

`match cmd {     Command::List { path: p, count: c } => {         println!("{} {}", p, c);     } }`

Ignore some fields:

`match cmd {     Command::List { path, .. } => {         println!("{}", path);     } }`

---

## 4. Match with `if` condition (guard)

`match cmd {     Command::List { count, .. } if count > 10 => {         println!("Large list");     }     Command::List { .. } => {         println!("Small list");     } }`

---

## 5. Catch-all (default)

`match cmd {     Command::List { path, .. } => println!("{}", path),     _ => println!("Unknown command"), }`

---

## 6. Match `Option`

`let x = Some(5);  match x {     Some(v) => println!("{}", v),     None => println!("No value"), }`

---

## 7. Match `Result`

`match result {     Ok(val) => println!("Success {}", val),     Err(e) => println!("Error {}", e), }`

---

## Common mistake (your error)

❌ Wrong:

`Command::List(path)`

✅ Correct (struct variant):

`Command::List { path }`