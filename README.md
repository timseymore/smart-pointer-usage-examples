# Smart Pointer Example Usage (C++20)

This document demonstrates best practices and common patterns for using `std::unique_ptr`, `std::shared_ptr`, and `std::weak_ptr` in modern C++.

---
## Example 1: Managing Ownership

**Exclusive ownership:** use `std::unique_ptr`
struct Resource { Resource() { stdcout << "Resource acquired\n"; } ~Resource() { stdcout << "Resource released\n"; } void do_something() { std::cout << "Resource is working\n"; } };
// Factory function returning unique_ptr stdunique_ptr<Resource> make_resource() { auto res = stdmake_unique<Resource>(); res->do_something(); return res; }

**Shared ownership:** use `std::shared_ptr`
stdshared_ptr<Resource> make_shared_resource() { auto res = stdmake_shared<Resource>(); res->do_something(); return res; }

**Non-owning access:** use raw pointer or reference
void use_resource_non_owning(Resource* res) { if (res) res->do_something(); }

---

## Example 2: Avoiding Circular References

Two classes with a circular relationship:
struct B; // Forward declaration
struct A { stdshared_ptr<B> b; // A owns B ~A() { stdcout << "A destroyed\n"; } };
struct B { stdweak_ptr<A> a;   // B references A, but does not own ~B() { stdcout << "B destroyed\n"; } };
// Factory function to create and link A and B stdshared_ptr<A> create_linked_A_and_B() { auto a = stdmake_shared<A>(); auto b = std::make_shared<B>(); a->b = b;   // A owns B b->a = a;   // B references A (non-owning) return a;   // a and b are now linked; no circular ownership }

---

## Example 3: Using Smart Pointers in Containers

---

## Example 4: Transferring Ownership
void transfer_ownership_example() { auto res1 = stdmake_unique<Resource>(); stdunique_ptr<Resource> res2 = stdmove(res1); // Ownership transferred if (!res1) stdcout << "res1 is now null\n"; if (res2) res2->do_something(); }
---

## Example 5: Observing `shared_ptr` with `weak_ptr`
void weak_ptr_example() { stdshared_ptr<Resource> sharedRes = stdmake_shared<Resource>(); stdweak_ptr<Resource> weakRes = sharedRes; if (auto locked = weakRes.lock()) { locked->do_something(); } sharedRes.reset(); if (weakRes.expired()) { stdcout << "Resource has been destroyed\n"; } }

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
