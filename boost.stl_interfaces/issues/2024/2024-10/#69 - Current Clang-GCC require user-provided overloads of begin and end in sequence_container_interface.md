# #69 - Current Clang/GCC require user-provided overloads of begin and end in sequence_container_interface [Closed]

> Username: thebrandre  
> Created at: 2024-10-11 05:44:34 UTC  
> Updated at: 2024-10-21 23:37:26 UTC  
> Closed at: 2024-10-21 23:37:25 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/69  

[Here is an example](https://godbolt.org/z/fnxxaxo9x), where Clang and GCC require a user-provided overload of the const begin/end members in contrast to [the documentation](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_stlinterfaces/tutorial___sequence_container_interface_.html#boost_stlinterfaces.tutorial___sequence_container_interface_.container) (see also [here](https://github.com/boostorg/stl_interfaces/blob/develop/doc/tutorial.qbk#L643)).  
  
Interestingly, Clang and GCC are inconsistent in whether or not one declares copy and move operations in the scope of the derived type.  
But I wouldn't consider this a bug of `sequence_container_interface` because the documentation states the special member functions should be provided. And in this case, it works with both compilers.  
  
msvc seems to be consistent with GCC.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-10-21 23:37:25 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/69#issuecomment-2427926824  

This is not a bug.  It is an unfortunate language rule.  If you replace the call to the member `contains()` with the identical code, outside of `Foo` -- that is, if you change the `return` statement at the end of `main()` to:  
  
```c++  
return std::ranges::find(v, std::array{1, 2}) != std::ranges::end(v);  
```  
  
, then everything works.  (I changed your example always to provide the special members, even though you should not have to, as that was not central to the issue.)  
  
To fix this, I *think* you can make `contains()` dependent.  That is, add a superfluous template head.  Something like `template <typename Dummy = void>`.  This will cause name lookup to be deferred until after the type is complete, and I think the original code will work.  Alternatively, you could create a typedef for the `sequence_container_interface` base class, and use that to qualify your calls to the members you need.  So you'd write something like `ranges::find(this->base_type::begin(), ...`.
