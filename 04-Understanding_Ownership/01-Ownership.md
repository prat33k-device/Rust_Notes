# Ownership

- This is the feature that sets Rust apart from other languages
  1. **Memory Safety:** ensures memory related errors like null pointer, dereferencing, buffer overflows, memory leaks, double free of memory are caught during <span style="color:red">Compile Time</span>
  2. **Performance:**  ensuring zero cost abstractions and system level control
  3. **Concurrency:** Helps avoid data races, making concurrent programming safe and more efficient
  4. **NO GARBAGE COLLECTION**
- C/C++ manual memory collection is error prune
- java's garbage collector introduce unpredictable latency
- Rust ownership is 3rd way to manage memory

## why memory management is dificult
## 3 Ownership Rules

1. Each value in Rust has a variable that's called Owner
2. There can only be 1 owner at a time
3. When owner goes out of scope, the value will be dropped

#