# Rust Server & Client that use **gRPC**

## What does the app do?
Once the server and client are running, they can communicate via gRPC

## Programming Languages:
- Rust
- proto

## Frameworks & Libraries:
- prost - https://github.com/tokio-rs/prost
- tokio - https://tokio.rs
- tonic - https://docs.rs/tonic/latest/tonic/
- tonic-build (as a build dependency) - https://docs.rs/tonic-build/latest/tonic_build/

## Implementation:
First of all, I've created the *.proto* file (path: **./proto/payments.proto**):
```proto
syntax = "proto3";
package payments;

service Bitcoin {
    rpc SendPayment (BitcoinPaymentRequest) returns (BitcoinPaymentResponse);
}

message BitcoinPaymentRequest {
    string from_addr = 1;
    string to_addr = 2;
    uint32 amount = 3;
}

message BitcoinPaymentResponse {
    bool    successful = 1;
    string  message = 2;
}
```
Second step is to create a *build.rs* file in the root directory to compile the proto file using the **tonic** library:
```rust
fn main() -> Result<(), Box<dyn std::error::Error>> {
    tonic_build::compile_protos("proto/payments.proto")?;
    Ok(())
}
```
The 3rd step was to create the **client** & the **server** and both contains:
```rust
pub mod payments {
    tonic::include_proto!("payments");
}
```
This allows us to import (*use*) the structs which we have defined using *proto*.

## Build & Run Instructions:
The most important thing is the structure of the **Cargo.toml** file:
```toml
[package]
name = "project"
version = "0.1.0"
edition = "2021"

[[bin]]
name = "server"
path = "src/server.rs"

[[bin]]
name = "client"
path = "src/client.rs"

[dependencies]
tonic = "0.8.3"
prost = "0.11.8"
tokio = { version = "1.0", features = ["macros", "rt-multi-thread"] }

[build-dependencies]
tonic-build = "0.8.4"
```
This lets us compile and run the server and the client separately using the following commands:
```bash
cargo run --bin server
```
```bash
cargo run --bin client
```