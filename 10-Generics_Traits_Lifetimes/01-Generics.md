# Generics

1. Generics allows so to write flexible, reusable code that works with any data types
2. Cleaner and more efficient code

if you have this kind of code
```rust
 fn first_element_i32(arr: &[i32]) -> i32 {
    arr[0]
}
fn first_element_usize(arr: &[usize]) -> usize {
    arr[0]
}
fn first_element_char(arr: &[char]) -> char {
    arr[0]
}
fn main() {
    let a = [1i32, 2i32, 3i32, 4i32];
    let b = [1usize, 2usize, 3usize, 4usize];
    let c = ['a', 'b', 'c', 'd'];

    println!("1st element i32: {}, 1st element usize: {}, 1st element char: {}", 
                first_element_i32(&a),
                first_element_usize(&b),
                first_element_char(&c));
}

```

this duplicated code can be cleaned by using generics

```rust
// writing a generic function that can handle multiple types
fn first_element<T> (arr: &[T]) -> &T {
    &arr[0]
}
fn main() {
    let a = [1i32, 2i32, 3i32, 4i32];
    let b = [1usize, 2usize, 3usize, 4usize];
    let c = ['a', 'b', 'c', 'd'];

    println!("1st element i32: {}, 1st element usize: {}, 1st element char: {}", 
                first_element(&a),
                first_element(&b),
                first_element(&c));
}
```

## example 1
```rust

// here we can use any type for both x and y but type(x) == type(y)
struct Pair<T> {
	x: T,
	y: T,
}
```

## example 2
```rust
struct Pair<T, U> {
	x: T,
	y: U,
}
// now here we can use different types of x and y as well
```

## example: now we can define similar result and option with enums
```rust
enum MyResult<T, E> {
	Ok(T),
	Err(E),
}

enum MyOption<T> {
	Some(T),
	None,
}
```

## example 4
```rust
struct Pair<T> {
	x: T,
	y: T,
}

impl<T> Point<T> {
//Here, we’ve defined a method named `x` on `Point<T>` that returns a reference to the data in the field `x`.
// We could have chosen a different name for this generic parameter than the generic parameter declared in the struct definition, but using the same name is conventional
	fn x(&self) -> &T { 
		&self.x 
	}
}

// method only for Point<f32>
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

impl<X1, Y1> Point<X1, Y1> {
    fn mixup<X2, Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c' };

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```


## Monomorphization

You might be wondering whether there is a runtime cost when using generic type parameters. The good news is that using generic types won’t make your program run any slower than it would with concrete types.

Rust accomplishes this by performing monomorphization of the code using generics at compile time. _Monomorphization_ is the process of turning generic code into specific code by filling in the concrete types that are used when compiled. In this process, the compiler does the opposite of the steps we used to create the generic function in Listing 10-5: The compiler looks at all the places where generic code is called and generates code for the concrete types the generic code is called with.