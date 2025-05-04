[home](../README.md)
### Chapter 4
#### 4.1 Stack and Heap
- Stack is like stack of plates, follows last in first out rule with push/pop methods.
- Heap allocates memory using pointer, ie ```allocating```.
- Pushing/accessing data in stack is faster compare to allocating/accessing on the heap.

#### 4.2 Ownership Rules
- Each value in Rust has a owner.
- Only 1 owner at a time.
- When owner is out of ```scope```, value will be dropped.

<u>What is scope?</u>
```rust
  {                        // s is not valid here, not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }                      // this scope is now over, and s is no longer valid
```
Note you can use curly bracket to force a scope.
```rust
{
    const S: &str = "my scope is valid in this curly bracket";
    println!("{S}");
} // S goes out of scope
// redeclare is possible now
const S: &str = "S can be redeclared now";
println!("{S}");
```
<u>Using ```String``` type instead of string literal to explain ownership</u>

String literal contents is known at compile time, hardcoded directly into the final executable making string literals fast and efficient. But these properties only come from the string literal’s immutability. 

With the ```String``` type, in order to support a mutable, growable piece of text, we need to allocate an amount of memory on the heap, unknown at compile time, to hold the contents.
```rust
// converting string literal to String
// note you must use let and not const
let s = String::from("string literal");

// mutating String, but string literal not
let mut s = String::from("string literal");
s.push_str("is mutated!");
println!("{s}");

```
1. The memory must be requested from the memory allocator at runtime 
- done using ```String::from```.
2. We need a way of returning this memory to the allocator when we’re done with our String.
- done using Rust's ```drop``` that is called when out of scope (In other language, there is Garbage Collector or handle manually)

<u>Issues with copying</u>
For primitive data types like ```int```
```rust
let x = 5;
let y = x; // this copies the stack data value of 5 to y
```
But for data types like ```String```, the variable stores pointers, len & cap on the ```stack```, and the actual data is stored on the ```heap```, reference using the pointers.

```rust
let s1 = String::from("hello"); 
let s2 = s1; // copies only stack data
```
Rust does not copy ```heap``` data because it is runtime expensive.
Hence, there is a problem because s1 & s2 will point to the same ```heap```, and if ```drop``` executes it will try to free both memory of s1 & s2, which lead to double free error. So Rust solves it by invalidating s1. This is called ```move```, as in moving s1 to s2.

Rust also immediately ```drop``` memory that are not used
```rust
let mut s = String::from("hello");
s = String::from("ahoy"); // "drop executes and hello" is freed from memory
```
To deeply copy both ```stack``` and ```heap``` data, use ```clone```.
```rust
let s1 = String::from("hello");
let s2 = s1.clone();
```
For variables that implements ```Copy``` trait, it does not use ```move``` and no need for ```clone```.
- All the integer types, such as u32.
- The Boolean type, bool, with values true and false.
- All the floating-point types, such as f64.
- The character type, char.
- Tuples, if they only contain types that also implement Copy. For - example, (i32, i32) implements Copy, but (i32, String) does not.

As in the example:
```rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // because i32 implements the Copy trait,
                                    // x does NOT move into the function,
    println!("{}", x);              // so it's okay to use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{some_string}");
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{some_integer}");
} // Here, some_integer goes out of scope. Nothing special happens.
```

Returning values in a function transfers ownership. To get back the variable, we can return it together with the result return as in:
```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{s2}' is {len}.");
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```
Too much work! so use ```references``` and ```borrowing```

#### 4.2 References
Rules:
- At any given time, you can have either one mutable reference or any number of immutable references.
- References must always be valid.