# Workspace

1. contains multiple packages

```bash
mkdir my_workspace
```

create my_workspace/Cargo.toml
```toml
[workspace]

members = [
    "package1",
    "package2"
]
```
then inside my_workspace
```bash
cargo new package1
cargo new package2


# now we can

cargo run -p package1
cargo run -p package2
cargo build
```
