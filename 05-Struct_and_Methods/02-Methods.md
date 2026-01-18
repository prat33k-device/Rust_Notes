# Methods

Functions associated to structs, enums or traits

## Syntax
```rust
struct Book {
	title: String,
	pages: u32,
}

impl Book {   // under here we can define all the methods for Book struct

	pub fn reading_time(&self) -> u32 {
		self.pages * 5   // asuming it takes 5 mins to read a page
	}
	
	pub fn has_more_pages_than(&self, another_book: &Book) -> bool {
		self.pages > another_book.pages
	}
	
	// if we want to update something through methods
	pub fn update_pages(&mut self, new_pages: u32) {
		self.pages = new_pages;
	}
	
	// this is a associate function because it doesn't take &self as an input
	pub fn default_book(pages: u32) -> Book {
		Book {
			title: String::from("Untitled"),
			pages
		}
	}

}

fn main() {

	let mut book1 = Book {
		title: String::from("Lord of the Rings"),
		pages: 532
	};
	
	let mut book2 = Book {
		title: String::from("Dark Matter"),
		pages: 864
	};
	
	let mut book3 = Book::default_book(0);
	
	println!(
		"Reading Time of {} is {}",
		book1.title,
		book1.reading_time()
	);
	println!(
		"Did {} have more pages than {}? {}",
		book2.title,
		book1.title,
		book2.has_more_pages_than(&book1)
	);
	
	println!("{} have {} pages", book1.title, book1.pages);
	book1.update_pages(248);
	println!("{} have {} pages", book1.title, book1.pages);
	
	println!("{} book have {} pages", book3.title, book3.pages);
	book3.title = String::from("Zero to One"); book3.pages = 124;
	println!("{} book have {} pages", book3.title, book3.pages);
	
}
```

## Implement Display trait

> in Rust, std::fmt::Display trait is responsible to format anything when we print it using {} in println! macro

```run-rust
use std::fmt;

struct Point {
	x: i32,
	y: i32,
}

impl fmt::Display for Point {
	fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		write!(f, "({}, {})", self.x, self.y)
	}
}

fn main() {
	
	let p1 = Point {
		x: 3,
		y: 5,
	};
	
	println!("{}", p1);
}

```

## add book contents later
