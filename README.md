# smart-pointer-usage-examples
A collection of examples of how to use smart pointers in C++.
#pragma once
#include <memory>
#include <vector>
#include <iostream>

// =====================================================
// Smart Pointer Example Usage (C++20)
// =====================================================
// This header demonstrates best practices and common patterns
// for using std::unique_ptr, std::shared_ptr, and std::weak_ptr.
// =====================================================

// Forward declaration for circular reference example
struct B;

// -----------------------------
// Example 1: Managing Ownership
// -----------------------------

// Exclusive ownership: use std::unique_ptr
struct Resource {
    Resource() { std::cout << "Resource acquired\n"; }
    ~Resource() { std::cout << "Resource released\n"; }
    void do_something() { std::cout << "Resource is working\n"; }
};

// Factory function returning unique_ptr
inline std::unique_ptr<Resource> make_resource() {
    auto res = std::make_unique<Resource>();
    res->do_something();
    return res;
}

// Shared ownership: use std::shared_ptr
inline std::shared_ptr<Resource> make_shared_resource() {
    auto res = std::make_shared<Resource>();
    res->do_something();
    return res;
}

// Non-owning access: use raw pointer or reference
inline void use_resource_non_owning(Resource* res) {
    if (res) res->do_something();
}

// ---------------------------------------------
// Example 2: Avoiding Circular References
// ---------------------------------------------

// Two classes with a circular relationship
struct A {
    std::shared_ptr<B> b; // A owns B
    ~A() { std::cout << "A destroyed\n"; }
};

struct B {
    std::weak_ptr<A> a;   // B references A, but does not own
    ~B() { std::cout << "B destroyed\n"; }
};

// Factory function to create and link A and B
inline std::shared_ptr<A> create_linked_A_and_B() {
    auto a = std::make_shared<A>();
    auto b = std::make_shared<B>();
    a->b = b;   // A owns B
    b->a = a;   // B references A (non-owning)
    return a;   // a and b are now linked; no circular ownership
}

// ---------------------------------------------------
// Example 3: Using smart pointers in containers
// ---------------------------------------------------

inline void container_example() {
    std::vector<std::unique_ptr<Resource>> resources;
    resources.push_back(std::make_unique<Resource>());
    resources.push_back(std::make_unique<Resource>());
    for (const auto& res : resources) {
        res->do_something();
    }
}

// ---------------------------------------------------
// Example 4: Transferring ownership
// ---------------------------------------------------

inline void transfer_ownership_example() {
    auto res1 = std::make_unique<Resource>();
    std::unique_ptr<Resource> res2 = std::move(res1); // Ownership transferred
    if (!res1) std::cout << "res1 is now null\n";
    if (res2) res2->do_something();
}

// ---------------------------------------------------
// Example 5: Observing shared_ptr with weak_ptr
// ---------------------------------------------------

inline void weak_ptr_example() {
    std::shared_ptr<Resource> sharedRes = std::make_shared<Resource>();
    std::weak_ptr<Resource> weakRes = sharedRes;
    if (auto locked = weakRes.lock()) {
        locked->do_something();
    }
    sharedRes.reset();
    if (weakRes.expired()) {
        std::cout << "Resource has been destroyed\n";
    }
}

// ---------------------------------------------------
// Note: No main() function is provided. These examples
// are intended for inclusion in other code or for use
// in unit tests or documentation snippets.
// =====================================================


// =====================================================
// Common Pitfalls to Avoid When Using Smart Pointers
// =====================================================
//
// 1. Creating Circular References with shared_ptr
//    - If two objects own each other via shared_ptr, they will never be destroyed (memory leak).
//    - Solution: Use std::weak_ptr for back-references or observer relationships.
//
// 2. Unnecessary Use of shared_ptr
//    - Overusing shared_ptr adds overhead and can make ownership unclear.
//    - Solution: Prefer std::unique_ptr for exclusive ownership. Use shared_ptr only when shared ownership is truly needed.
//
// 3. Mixing Raw Pointers and Smart Pointers
//    - Don’t create multiple smart pointers from the same raw pointer. This can cause double deletion.
//    - Solution: Always transfer ownership to a smart pointer immediately after allocation.
//
// 4. Dangling weak_ptr
//    - Accessing a weak_ptr without checking if it’s expired can lead to undefined behavior.
//    - Solution: Always use lock() and check the result before using the object.
//
// 5. Storing Smart Pointers for Non-Owning Access
//    - Don’t use smart pointers just to access an object you don’t own.
//    - Solution: Use raw pointers or references for non-owning access.
//
// 6. Forgetting to Use std::make_unique / std::make_shared
//    - Using new directly with smart pointers is error-prone and less efficient.
//    - Solution: Use std::make_unique and std::make_shared for allocation.
//
// 7. Not Understanding Ownership Semantics
//    - Passing smart pointers by value can transfer or share ownership unexpectedly.
//    - Solution: Pass by reference or const reference when you don’t want to transfer/share ownership.
//
// 8. Custom Deleters
//    - If you use a custom deleter, make sure it matches the allocation method.
//    - Solution: Only use custom deleters when necessary and ensure correctness.
//
// -----------------------------------------------------
// Avoiding these pitfalls will help you write safer and
// more maintainable C++ code with smart pointers.
// =====================================================
