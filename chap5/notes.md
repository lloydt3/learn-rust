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