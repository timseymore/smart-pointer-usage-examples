# Smart Pointer Example Usage (C++20)

This document demonstrates best practices and common patterns for using `std::unique_ptr`, `std::shared_ptr`, and `std::weak_ptr` in modern C++.

### Refer to `SmartPointersExamples.h` for the code of the examples listed below.
---
#### Example 1: Managing Ownership

---

#### Example 2: Avoiding Circular References

---

#### Example 3: Using Smart Pointers in Containers

---

#### Example 4: Transferring Ownership

---

#### Example 5: Observing `shared_ptr` with `weak_ptr`

---

> **Note:**  
> No `main()` function is provided. These examples are intended for inclusion in other code or for use in unit tests or documentation snippets.

---

## Common Pitfalls to Avoid When Using Smart Pointers

1. **Creating Circular References with `shared_ptr`**
   - If two objects own each other via `shared_ptr`, they will never be destroyed (memory leak).
   - **Solution:** Use `std::weak_ptr` for back-references or observer relationships.

2. **Unnecessary Use of `shared_ptr`**
   - Overusing `shared_ptr` adds overhead and can make ownership unclear.
   - **Solution:** Prefer `std::unique_ptr` for exclusive ownership. Use `shared_ptr` only when shared ownership is truly needed.

3. **Mixing Raw Pointers and Smart Pointers**
   - Don’t create multiple smart pointers from the same raw pointer. This can cause double deletion.
   - **Solution:** Always transfer ownership to a smart pointer immediately after allocation.

4. **Dangling `weak_ptr`**
   - Accessing a `weak_ptr` without checking if it’s expired can lead to undefined behavior.
   - **Solution:** Always use `lock()` and check the result before using the object.

5. **Storing Smart Pointers for Non-Owning Access**
   - Don’t use smart pointers just to access an object you don’t own.
   - **Solution:** Use raw pointers or references for non-owning access.

6. **Forgetting to Use `std::make_unique` / `std::make_shared`**
   - Using `new` directly with smart pointers is error-prone and less efficient.
   - **Solution:** Use `std::make_unique` and `std::make_shared` for allocation.

7. **Not Understanding Ownership Semantics**
   - Passing smart pointers by value can transfer or share ownership unexpectedly.
   - **Solution:** Pass by reference or const reference when you don’t want to transfer/share ownership.

8. **Custom Deleters**
   - If you use a custom deleter, make sure it matches the allocation method.
   - **Solution:** Only use custom deleters when necessary and ensure correctness.

---

Avoiding these pitfalls will help you write safer and more maintainable C++ code with smart pointers.
