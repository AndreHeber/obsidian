```bash
cargo new <project_name> && cd <project_nam>
```
generates new subfolder with given project name and everything initialized.

```bash
cargo build
```
solves dependencies & compiles


```bash
cargo run
```
`cargo build` + running the exe

```bash
cargo doc --open
```
compiles and opens the documentation for your project

for small releases:
```bash
cargo +nightly build -Z build-std=std,panic_abort -Z build-std-features=panic_immediate_abort \
    --target x86_64-apple-darwin --release
```
