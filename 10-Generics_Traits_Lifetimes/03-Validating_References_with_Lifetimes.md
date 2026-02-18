# Borrow checker

 ```rust
 fn main() {
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {r}");   //          |
}                         // ---------+
 ```

Here, we’ve annotated the lifetime of `r` with `'a` and the lifetime of `x` with `'b`. As you can see, the inner `'b` block is much smaller than the outer `'a` lifetime block. At compile time, Rust compares the size of the two lifetimes and sees that `r` has a lifetime of `'a` but that it refers to memory with a lifetime of `'b`. The program is rejected because `'b` is shorter than `'a`: The subject of the reference doesn’t live as long as the reference.

```rust
fn main() {
    let x = 5;            // ----------+-- 'b
                          //           |
    let r = &x;           // --+-- 'a  |
                          //   |       |
    println!("r: {r}");   //   |       |
                          // --+       |
}                         // ----------+
```

Here, `x` has the lifetime `'b`, which in this case is larger than `'a`. This means `r` can reference `x` because Rust knows that the reference in `r` will always be valid while `x` is valid.


#  Generic Lifetimes in function
We’ll write a function that returns the longer of two string slices. This function will take two string slices and return a single string slice. After we’ve implemented the `longest` function, the code should print `The longest string is abcd`.
## Lifetime Annotation Syntax
```rust
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

## In Function Signatures
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```
The function signature now tells Rust that for some lifetime `'a`, the function takes two parameters, both of which are string slices that live at least as long as lifetime `'a`. The function signature also tells Rust that the string slice returned from the function will live at least as long as lifetime `'a`. In practice, it means that the lifetime of the reference returned by the `longest` function is the same as the smaller of the lifetimes of the values referred to by the function arguments. These relationships are what we want Rust to use when analyzing this code.


Let’s look at how the lifetime annotations restrict the `longest` function by passing in references that have different concrete lifetimes.

```rust
✅✅✅✅
fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {result}");
    }
}
```
In this example, `string1` is valid until the end of the outer scope, `string2` is valid until the end of the inner scope, and `result` references something that is valid until the end of the inner scope. Run this code and you’ll see that the borrow checker approves; it will compile and print `The longest string is long string is long`.


```rust
❌❌❌❌❌ // compilation error
fn main() {
    let string1 = String::from("long string is long");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {result}");
}

```

```bash
$ cargo run
   Compiling chapter10 v0.1.0 (file:///projects/chapter10)
error[E0597]: `string2` does not live long enough
 --> src/main.rs:6:44
  |
5 |         let string2 = String::from("xyz");
  |             ------- binding `string2` declared here
6 |         result = longest(string1.as_str(), string2.as_str());
  |                                            ^^^^^^^ borrowed value does not live long enough
7 |     }
  |     - `string2` dropped here while still borrowed
8 |     println!("The longest string is {result}");
  |                                      ------ borrow later used here

For more information about this error, try `rustc --explain E0597`.
error: could not compile `chapter10` (bin "chapter10") due to 1 previous error

```

The error shows that for `result` to be valid for the `println!` statement, `string2` would need to be valid until the end of the outer scope. Rust knows this because we annotated the lifetimes of the function parameters and return values using the same lifetime parameter `'a`.

## Relationships
We’ve specified a lifetime parameter `'a` for the parameter `x` and the return type, but not for the parameter `y`, because the lifetime of `y` does not have any relationship with the lifetime of `x` or the return value.

```rust
fn longest<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```


`result` goes out of scope and gets cleaned up at the end of the `longest` function. We’re also trying to return a reference to `result` from the function. There is no way we can specify lifetime parameters that would change the dangling reference, and Rust won’t let us create a dangling reference. In this case, the best fix would be to return an owned data type rather than a reference so that the calling function is then responsible for cleaning up the value.
```rust
❌❌❌❌ // compilation error
fn longest<'a>(x: &str, y: &str) -> &'a str {
    let result = String::from("really long string");
    result.as_str()
}
```
# In struct definations
So far, the structs we’ve defined all hold owned types. We can define structs to **hold references**, but in that case, we would need to add a lifetime annotation on every reference in the struct’s definition.
```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().unwrap();  // string slice
    let i = ImportantExcerpt {
        part: first_sentence,
    };
}
```

This struct has the single field `part` that holds a string slice, which is a reference. As with generic data types, we declare the name of the generic lifetime parameter inside angle brackets after the name of the struct so that we can use the lifetime parameter in the body of the struct definition. This annotation means an instance of `ImportantExcerpt` can’t outlive the reference it holds in its `part` field.

# Lifetime elision

1. The patterns programmed into Rust’s analysis of references are called the _lifetime elision rules_. These aren’t rules for programmers to follow; they’re a set of particular cases that the compiler will consider, and if your code fits these cases, you don’t need to write the lifetimes explicitly.
2. Lifetimes on function or method parameters are called _input lifetimes_, and lifetimes on return values are called _output lifetimes_.



The compiler uses three rules to figure out the lifetimes of the references when there aren’t explicit annotations. The first rule applies to input lifetimes, and the second and third rules apply to output lifetimes. If the compiler gets to the end of the three rules and there are still references for which it can’t figure out lifetimes, the compiler will stop with an error. These rules apply to `fn` definitions as well as `impl` blocks.

The first rule is that the compiler assigns a lifetime parameter to each parameter that’s a reference. In other words, a function with one parameter gets one lifetime parameter: `fn foo<'a>(x: &'a i32)`; a function with two parameters gets two separate lifetime parameters: `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`; and so on.

The second rule is that, if there is exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters: `fn foo<'a>(x: &'a i32) -> &'a i32`.

The third rule is that, if there are multiple input lifetime parameters, but one of them is `&self` or `&mut self` because this is a method, the lifetime of `self` is assigned to all output lifetime parameters. This third rule makes methods much nicer to read and write because fewer symbols are necessary.

Let’s pretend we’re the compiler. We’ll apply these rules to figure out the lifetimes of the references in the signature of the `first_word` function in Listing 10-25. The signature starts without any lifetimes associated with the references:

`fn first_word(s: &str) -> &str {`

Then, the compiler applies the first rule, which specifies that each parameter gets its own lifetime. We’ll call it `'a` as usual, so now the signature is this:

`fn first_word<'a>(s: &'a str) -> &str {`

The second rule applies because there is exactly one input lifetime. The second rule specifies that the lifetime of the one input parameter gets assigned to the output lifetime, so the signature is now this:

`fn first_word<'a>(s: &'a str) -> &'a str {`

Now all the references in this function signature have lifetimes, and the compiler can continue its analysis without needing the programmer to annotate the lifetimes in this function signature.

Let’s look at another example, this time using the `longest` function that had no lifetime parameters when we started working with it in Listing 10-20:

`fn longest(x: &str, y: &str) -> &str {`

Let’s apply the first rule: Each parameter gets its own lifetime. This time we have two parameters instead of one, so we have two lifetimes:

`fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &str {`

You can see that the second rule doesn’t apply, because there is more than one input lifetime. The third rule doesn’t apply either, because `longest` is a function rather than a method, so none of the parameters are `self`. After working through all three rules, we still haven’t figured out what the return type’s lifetime is. This is why we got an error trying to compile the code in Listing 10-20: The compiler worked through the lifetime elision rules but still couldn’t figure out all the lifetimes of the references in the signature.

Because the third rule really only applies in method signatures, we’ll look at lifetimes in that context next to see why the third rule means we don’t have to annotate lifetimes in method signatures very often.


# In Method Definitions
```rust
impl<'a> ImportantExcerpt<'a> {
}
```

Here is an example where the third lifetime elision rule applies:
```rust
 impl<'a> ImportantExcerpt<'a> {
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention please: {announcement}");
        self.part
    }
}
```
There are two input lifetimes, so Rust applies the first lifetime elision rule and gives both `&self` and `announcement` their own lifetimes. Then, because one of the parameters is `&self`, the return type gets the lifetime of `&self`, and all lifetimes have been accounted for.


# The `'static` Lifetime

1. A Special lifetime is `'static`, which denotes that the affected reference _can_ live for the entire duration of the program.
2. All string literals have the `'static` lifetime, which we can annotate as follows:
	```rust
	let s: &'static str = "I have a static lifetime";
	```
3. The text of this string is stored directly in the program’s binary, which is always available. Therefore, the lifetime of all string literals is `'static`.

>You might see suggestions in error messages to use the `'static` lifetime. But before specifying 
>`'static` as the lifetime for a reference, think about whether or not the reference you have 
>actually lives the entire lifetime of your program, and whether you want it to. Most of the time, 
>an error message suggesting the `'static` lifetime results from attempting to create a dangling 
>reference or a mismatch of the available lifetimes. In such cases, the solution is to fix those 
>problems, not to specify the `'static` lifetime.


# Generic Type Parameters, Trait Bounds, and Lifetimes

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T> (x: &'a str, y: &'a, ann: T) -> &'a str
where T: Display,
{
	println!("Announcement: {}", ann);
	if x.len() > y.len() { x } else { y }
}
```
This is the `longest` function from earlier that returns the longer of two string slices. But now it has an extra parameter named `ann` of the generic type `T`, which can be filled in by any type that implements the `Display` trait as specified by the `where` clause. This extra parameter will be printed using `{}`, which is why the `Display` trait bound is necessary. Because lifetimes are a type of generic, the declarations of the lifetime parameter `'a` and the generic type parameter `T` go in the same list inside the angle brackets after the function name