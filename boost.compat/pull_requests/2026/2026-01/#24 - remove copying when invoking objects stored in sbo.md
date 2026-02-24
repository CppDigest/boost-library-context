# #24 remove copying when invoking objects stored in sbo [Merged]

> Username: cmazakas  
> Created at: 2026-01-15 22:17:17 UTC  
> Updated at: 2026-01-22 15:24:06 UTC  
> Merged at: 2026-01-22 07:49:54 UTC  
> Closed at: 2026-01-22 07:49:54 UTC  
> Url: https://github.com/boostorg/compat/pull/24  

Resolves: https://github.com/boostorg/compat/issues/23  
  
Right now the invoke holders take the storage by-value which is strictly incorrect when using SBO as it creates a copy of the callable which leads to surprising results in user-code that expect multiple calls to a lambda with a mutable capture.

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-16 00:11:26 UTC  
> Url: https://github.com/boostorg/compat/pull/24#issuecomment-3757461462  

Instead of `storage&`, make it a template `Storage&` to capture the `const` which will allow you to omit the `const_cast`s.

---

## Comment 2

> Username: cmazakas  
> Created_at: 2026-01-16 17:49:34 UTC  
> Url: https://github.com/boostorg/compat/pull/24#issuecomment-3761145938  

> Instead of `storage&`, make it a template `Storage&` to capture the `const` which will allow you to omit the `const_cast`s.  
  
I'm trying that but I'm having trouble getting code like this to then compile:  
```cpp  
        move_only_function<int( noex_callable ) const> f2( std::move( f1 ) );  
        BOOST_TEST_EQ( f2( c ), 1235 );  
  
        move_only_function<int( noex_callable )> f3( std::move( f2 ) );  
        BOOST_TEST_EQ( f3( c ), 1235 );  
```  
  
The compilers reject it with:  
```  
./boost/compat/move_only_function.hpp:421:24: error: invalid conversion from ‘int (*)(boost::compat::detail::move_only_function_base<boost::compat::detail::ref_quals::none, true, false, int, noex_callable>::storage_type&, noex_callable&&)’ {aka ‘int (*)(const boost::compat::detail::storage&, noex_callable&&)’} to ‘int (*)(boost::compat::detail::move_only_function_base<boost::compat::detail::ref_quals::none, false, false, int, noex_callable>::storage_type&, noex_callable&&)’ {aka ‘int (*)(boost::compat::detail::storage&, noex_callable&&)’} [-fpermissive]  
  421 |         invoke_ = base.invoke_;  
```  
The problem being there's no conversion from:  
```cpp  
int (*)(const boost::compat::detail::storage&  
```  
to  
```cpp  
int (*)(boost::compat::detail::storage&  
```  
  
Many of the tests fail like:  
```  
libs/compat/test/move_only_function_test.cpp:844:41: note: in instantiation of function template specialization 'boost::compat::move_only_function<int (long)>::move_only_function<boost::compat::move_only_function<int (long) const>, boost::compat::move_only_function<int (long) const>, 0>' requested here  
  844 |         move_only_function<int( long )> e3( std::move( e2 ) );  
```  
  
Do you want me to push up my WIP branch?  
  
The more I think about it, the const_cast is probably harmless here anyway because we correctly apply the cv-ref qualifiers at the invoke site.  
  
I'm not sure what we should do to fix these kinds of function pointer cast errors.

---

## Comment 3

> Username: pdimov  
> Created_at: 2026-01-16 17:53:40 UTC  
> Url: https://github.com/boostorg/compat/pull/24#issuecomment-3761160482  

Ah, I see.  
  
Fewer const_casts will be needed if you declare the functions to take `storage const&` (or `storage const*` if you prefer) and then const_cast inside, instead of at the call site.

---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2026-01-18 17:45:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/24#pullrequestreview-3675616119  

📁 test/move_only_function_test.cpp

```diff
  28 | 
  29 |+ #ifdef _MSC_VER
  30 |+ #pragma warning(disable: 4789) // false buffer overrun warning in test_mutable_lambda()
```

> Username: pdimov  
> Created_at: 2026-01-18 17:45:21 UTC  
> Updated_at: 2026-01-18 17:45:22 UTC  
> Url: https://github.com/boostorg/compat/pull/24#discussion_r2702597536  

What is the buffer overrun warning?


---

## Comment 5

> Username: pdimov  
> Created_at: 2026-01-18 18:33:27 UTC  
> Url: https://github.com/boostorg/compat/pull/24#issuecomment-3765584045  

Can you please structure this as: commit 1 - test additions without the 4789 warning suppression; commit 2 - changes to the implementation; commit 3 - warning suppression in the test?

---

## Comment 6

> Username: pdimov  
> Created_at: 2026-01-20 16:43:23 UTC  
> Url: https://github.com/boostorg/compat/pull/24#issuecomment-3773888407  

I've merged the first two commits to develop.  
  
As for the third one, the code is correct, the branch is never taken. However, there's still a better fix because the reason for the warning is that `manage_local<VT>` is still instantiated for large `VT`, even though it's not called. That's because here  
  
https://github.com/boostorg/compat/blob/945b2862b4db538e14f48e1fbae4ad4a03220011/include/boost/compat/move_only_function.hpp#L447  
  
the compile-time constant condition `storage::use_sbo<VT>()` is tested with a runtime `if`.  
  
`if constexpr` would of course have solved this, but since it's C++17, another approach would be to add another `true_type`/`false_type` to `init`, one that takes `std::integral_constant<bool, storage::use_sbo()>()`.  
  
A drive-by comment:  
  
https://github.com/boostorg/compat/blob/945b2862b4db538e14f48e1fbae4ad4a03220011/include/boost/compat/move_only_function.hpp#L400  
  
Best practices when using placement new is to qualify it, `::new`, so that the (unlikely) possibility of class-specific placement new is avoided.

---
