# Traits

1. traits define shared behavior across types
2. a trait is like a contract that the types can signup for
3. if a type implements a trait, it promises a certain behavior
4. similar to Interfaces in other languages
5. traits allow methods across types, enabling polymorphism

### example
```rust
// define a Sendable trait
pub trait Sendable {
    fn send(&self) -> String;   //Types that can implement Sendable must implement a send method
} 

struct Email {
    recipient: String,
    subject: String,
    _body: String,
}
struct SMS {
    phone_number: String,
    message: String,
}

impl Sendable for Email {
    fn send(&self) -> String {
        format!("Sending email to {}: {}", self.recipient, self.subject)
    }
}

impl Sendable for SMS {
    fn send(&self) -> String {
        format!("Sending email to {}: {}", self.phone_number, self.message)
    }
}


fn main() {
    let email = Email {
        recipient: String::from("alice@example.com"),
        subject: String::from("Meeting Reminder"),
        _body: String::from("Don't forget about our meeting tomorrow at 10 AM."),
    };


    let sms = SMS {
        phone_number: String::from("+1234567890"),
        message: String::from("Your OTP is 123456."),
    };

    println!("{}", email.send());
    println!("{}", sms.send());
}

```

## Using default implementations

```rust
// define a Sendable trait
pub trait Summarize {
    fn summarize(&self) -> String {  // all types that will implement this trait but don't overwrite this function will have this default functionality
        String::from("Read more ...")
    }
} 

pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

pub struct SocialPost {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub repost: bool,
}

impl Summarize for SocialPost {} // if you implement summarize function here then it will overweite the default

fn main() {
    let post = SocialPost {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        repost: false,
    };

    println!("1 new post: {}", post.summarize());
}

```

## Using Traits as parameters

1. We can explore how to use traits to define functions that accept many different types.
2. Instead of a concrete type for the `item` parameter, we specify the `impl` keyword and the trait name.
3. This parameter accepts any type that implements the specified trait.
4. In the body of `notify`, we can call any methods on `item` that come from the `Summary` trait, such as `summarize`
5. We can call `notify` and pass in any instance of `NewsArticle` or `SocialPost`.

```rust
pub fn notify(item: &impl Summary) {
	println!("Breaking news! {}", item.summarize());
}
```

### Trait bound syntax
impl Trait syntax is actually a short for for a longer form of syntax called trait bound
```rust
pub fn notify<T: Summary> (item: &T) {
	println!("Breaking news! {}", item.summarize());
}
```

### examples of same function but with both syntax
```rust
// multiple parameters
pub fn notify(item1: &impl Summarize, item2: &impl Summarize) {}

pub fn notify<T: Summarize> (item1: &T, item2: &T) {}
```

#### Multiple trait bounds with + syntax
This means that `item` must implement both `Display` and `Summary`.
```rust
pub fn notify(item: &(impl Summary + Display)) {}

pub fn notify<T: Summary + Display> (item: &T) {}
```

#### Clearer Trait Bounds with where Clauses
```rust
// let some function
fn some_function<T: Display + Clone, U: Clone + Debug> (t: &T, u: &U) -> i32 {}
// can be written as
fn some_function<T, U> (t: &T, u: &U) -> i32
where
	T: Display + Clone,
	U: Clone + Debug,
{
	// code
}
```

## Returing types that implement traits

> you can only use `impl Trait` if you’re returning a single type.

```rust

// THIS WON'T COMPILE ❌❌❌
fn returns_summarizable(switch: bool) -> impl Summary {
    if switch {
        NewsArticle {
            headline: String::from(
                "Penguins win the Stanley Cup Championship!",
            ),
            location: String::from("Pittsburgh, PA, USA"),
            author: String::from("Iceburgh"),
            content: String::from(
                "The Pittsburgh Penguins once again are the best \
                 hockey team in the NHL.",
            ),
        }
    } else {
        SocialPost {
            username: String::from("horse_ebooks"),
            content: String::from(
                "of course, as you probably already know, people",
            ),
            reply: false,
            repost: false,
        }
    }
}
```
Returning either a `NewsArticle` or a `SocialPost` isn’t allowed due to restrictions around how the `impl Trait` syntax is implemented in the compiler. We’ll cover how to write a function with this behavior in the [“Using Trait Objects to Abstract over Shared Behavior”](https://doc.rust-lang.org/book/ch18-02-trait-objects.html#using-trait-objects-to-abstract-over-shared-behavior) section of Chapter 18.

## using trait bounds to conditionally implement methods

> `Self` is a type alias for the type of the `impl` block

```rust
struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }
}

// this methods only immplements on types that implement Display + PartialOrd
impl<T: Display + PartialOrd> Pair<T> {
	fn cmp_display(&self) {
		if self.x >= self.y {
			println!("The largest member is x = {}", self.x);
		} else {
			println!("The largest memeber is y = {}", self.y)
		}
	}
}
```


