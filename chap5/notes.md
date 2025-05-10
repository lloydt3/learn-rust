[home](../README.md)
### Chapter 5
#### 5.1 Defining & Instantiating Struct
Struct (similar to type or schema definition in TS)
```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```
After definining, we can use it to create an ```instance``` using actual values
```rust
// note this examples uses String with ownership instead of &str
// this is intentionally due to lifetime requirements
// chapter 10 will explain more about lifetimes
fn main() {
    let user = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };
    println!("{}",user.username); // dot notation to get value
}
```
If mutable, we can change it using dot notation
```rust
fn main() {
    let mut user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```
Creating instance can be simplified if field name is same as variable name.
```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username: username,
        email: email,
        sign_in_count: 1,
    }
}
// this following is the same as the above
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username, // no need write username: username
        email, // no need write email: email
        sign_in_count: 1,
    }
}
```

To create new instance using data from another instance, use ```..```
```rust
fn main() {
    // --snip--

    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}

// The following is the same as the above
fn main() {
    // --snip--

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```
<b style="color:orange">NOTE: In the above example, all fields except email was moved into user2. So if you try to do something with eg. user1.username there will be an error.</b>

#### 5.1.2 Tuple Struct
```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```
To destructure it:
```rust
let Point(x, y, z) = origin;
```

#### 5.1.3 Unit-like Struct Without Fields
```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```
Why is this neccessary? To implement trait on some type but no data. Honestly I have no idea.. maybe I will understand it's importance as I go on this tutorial.

#### 5.3 Add functionality with Derived Traits
```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {}", rect1);
}
```

The above code will result in error of```error[E0277]: `Rectangle` doesn't implement `std::fmt::Display```

To fix this, you can use 
##### (1) Debug trait
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {rect1:?}");
    // rect1 is Rectangle { width: 30, height: 50 } 
    println!("rect1 is {rect1:#?}"); // with # for easier read
    // react1 is Rectangle {
    //     width: 30,
    //     height: 50,
    // }
}
```
You can also use ```dbg!``` 
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    dbg!(&rect1);
}
// prints
// [src/main.rs:10:16] 30 * scale = 60
// [src/main.rs:14:5] &rect1 = Rectangle {
//     width: 60,
//     height: 50,
// }
```
But beware, unlike ```println!``` it takes ownership and then returns it. This is sometimes useful for debugging.
##### (2) Implement Display
```rust
use std::fmt;
struct Rectangle {
    width: u32,
    height: u32,
}

impl fmt::Display for Rectangle {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(
            f,
            "Rectangle {{ width: {}, height: {} }}",
            self.width, self.height
        )
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };
    println!("rect1 is {}", rect1);
}
```

#### 5.4 Method Syntax
Using ```impl``` to define method. The first parameter is always ```self```, which represents the instance of the struct it is called on.
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 { // &self is short for self: &Self
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```
There can be multiple  ```fn``` in each ```impl``` block. 
```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```
There can also be multiple ```impl``` blocks.
```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
``` 

##### 5.4.1 Automatic referencing and dereferencing
Rust automatically adds in ```&```, ```&mut```, or ```*``` so object matches the signature of the method. 
```rust
// the following is the same 
// This automatic referencing behavior works because methods have a clear receiver.
p1.distance(&p2);
(&p1).distance(&p2);
```

##### 5.4.2 Associated functions
- All functions defined using impl are associated functions. 
- Associated functions that are not methods are often used for constructors that will return a new instance of the struct. This is like ```new``` in python or other language.
```rust
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}
```
In the above example, use ```::``` to call the associated function;```let sq = Rectangle::square(3);```

[home](../README.md)