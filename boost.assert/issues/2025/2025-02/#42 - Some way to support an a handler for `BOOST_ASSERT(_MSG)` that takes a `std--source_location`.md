# #42 - Some way to support an a handler for `BOOST_ASSERT(_MSG)` that takes a `std::source_location` [Open]

> Username: RedBeard0531  
> Created at: 2025-02-05 14:37:36 UTC  
> Updated at: 2025-02-05 14:57:44 UTC  
> Url: https://github.com/boostorg/assert/issues/42  

Problem: Using `std::source_location` is more efficient than passing separate file/line/column/func info because the location is a single pointer to static storage, rather than 2 pointer + 2 const ints. That can result in codegen bloat, especially on platforms like Aarch64 where it takes multiple instructions to load a global pointer. Given the usecase of assertions, this is often used for very cold (hopefully never executed!) code that sits in the same function as hot code, so you often want to make it as small as possible to maximize icache and iTLB. Note that it is important to use `source_location` from `std::` rather than `boost::` because the `std::` flavor uses implementation techniques that are not available to userspace C++ to make it a single pointer to static storage.    
  
Possible solution 1: Add a new macro like `BOOST_ENABLE_ASSERT_HANDLER_WITH_STD_SOURCE_LOCATION` to opt into a different handler API that takes `std::source_location`.  
  
Possible solution 2: Give the user full control over the handler expression by just making the failure case be a macro call like `BOOST_ASSERT_FAILURE_HANDLER()` and `BOOST_ASSERT_MSG_FAILURE_HANDLER(msg)`. The only tricky issue is that you would likely need some way to inject declarations and #includes into global scope, but it may be reasonable to say that anyone who can use this facility correctly can probably also use `-include "my_boost_assert_prefix_header.h"`. This option also has the advantage for anyone who wants to use `__builtin_trap()` to get the ultimate code size reduction, while still checking asserts.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-02-05 14:57:42 UTC  
> Url: https://github.com/boostorg/assert/issues/42#issuecomment-2637099341  

Codegen demo: https://godbolt.org/z/Mxbh6fjx5  
  
`std::source_location` needs to be passed by value to realize any gains, `boost::source_location` isn't worth supporting, at least for code size reasons.
