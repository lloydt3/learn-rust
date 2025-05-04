[Home](../README.md)
### Chapter 3

#### 3.1 Variable declaration concepts
```rust
let x = 5; //immutable

const MINS_IN_A_DAY: u32 = 24 * 60; // must be caps with type declaration (eg. u32)

// mutable let, but type is fixed
let mut x = 5;
x = 6;

//shadowing, redeclare and mutable type
let x = "name"; // string
let x = x.len(); // int
```

#### 3.2 Data types
##### Scalar (4 types) vs Compound (2 types)
> note that there are more data types beside these 2
```rust
// 1) Integer: scalar
i8, i16, i32, i64; i128, isize;
u8, u16, u32, u64; u128, usize;

// 2) Floating-point number: scalar
f32, f64;

// 3) Boolean: scalar
bool;

// 4) character: scalar
char; 
let a: char = 'a'; // use single quote!
```
```rust
// 5) tuples: compound
let tup: (i32, f64) = (500, 12.3); // once declare, size is fixed

// access
let (x, y) = tup; //destructuring
let x = tup.1; // access directly w/ index
```
```rust
// 6) array: compound
let a = [1,2,3,4,5]; 
// all elements must be same type, and once declare the length is fixed
// if need to shrink or grow, use vector

// type declaration
let a: [i32; 5] = [1,2,3,4,5];
// same value declaration
let a = [3; 5]; // [3,3,3,3,3]

// access
let first = a[0];
let second = a[1];
```
#### 3.2 Functions
```rust
// function signature must have type declaration
fn my_function (input: i32, another_input: i32){
    println!("{input}");
    println!("{another_input}");
}
// function with return must have type
fn five() -> i32 {
    5
}
```
#### 3.3 Statement vs Expression
- Statements: instructions that perform some action and do not return a value.

```rust
fn main() {
    let x = 5; // statement
    let x = (let y = 6); // ERROR: let y = 6 is statement, do not return value so error
}
```
- Expressions: evaluate to a resultant value
```rust
// calling a function/macro is a expression
some_function();

// curly block is expression
{
    let x = 1;
    x = 1 // no semicolon, if semicolon it will become statement
}
```

#### 3.4 Control flow
##### ```let``` and ```if``` combination
```rust
// if and let combi
let is_odd = true;
let x = if is_odd { 1 } else { 2 }; // must also be same type
```
##### loop
```rust
// basic loop
loop {
    println!("This is a loop");
}

// break and return value
let mut counter = 0;

let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2; // add semicolon
    }
};

println!("The result is {result}");

// labeling loops
 let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
```

##### while
```rust
 while number != 0 {
    println!("{number}!");
    number -= 1;
}
```

##### for
```rust
// create array and loop with for
let a = [10, 20, 30, 40, 50];

for element in a {
    println!("the value is: {element}");
    // prints 10,20,...50
}

// in-line
for element in 10..20 {
    println!("the value is: {element}");
    // prints 10,11,12...19
}
```