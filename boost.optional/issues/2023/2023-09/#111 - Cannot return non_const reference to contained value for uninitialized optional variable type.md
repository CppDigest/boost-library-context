# #111 - Cannot return non_const reference to contained value for uninitialized optional variable type. [Open]

> Username: robertramey  
> Created at: 2023-09-05 21:12:37 UTC  
> Updated at: 2026-02-18 20:39:06 UTC  
> Url: https://github.com/boostorg/optional/issues/111  

I'm having an issue with the following code:  
  
From boost/optional.hpp - lines 1267-1281 (problem lines marked with ~)  
  
>  // Returns a reference to the value if this is initialized, otherwise,  
    // the behaviour is UNDEFINED  
    // No-throw  
    reference_const_type get() const { BOOST_ASSERT(this->is_initialized()) ; return this->get_impl(); }  
~  reference_type       get()       { BOOST_ASSERT(this->is_initialized()) ; return this->get_impl(); }  
  
>  /// Returns a copy of the value if this is initialized, 'v' otherwise  
    reference_const_type get_value_or ( reference_const_type v ) const { return this->is_initialized() ? get() : v ; }  
    reference_type       get_value_or ( reference_type       v )       { return this->is_initialized() ? get() : v ; }  
  
>  /// Returns a pointer to the value if this is initialized, otherwise,  
    // the behaviour is UNDEFINED  
    // No-throw  
    pointer_const_type operator->() const { BOOST_ASSERT(this->is_initialized()) ; return this->get_ptr_impl() ; }  
~  pointer_type       operator->()       { BOOST_ASSERT(this->is_initialized()) ; return this->get_ptr_impl() ; }  
  
The code returns non-const reference and pointer.  These used for getting a reference to the contained variable holding the type presumably so it can be modified.  (this is my case).  BUT, the BOOST_ASSERT traps if the variable isn't initialized.  This makes it impossible to modify the contained value.  The is necessary in the context of the serialization library.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-12-29 00:49:48 UTC  
> Url: https://github.com/boostorg/optional/issues/111#issuecomment-1871650133  

Can you show a small program that uses `optional` and suffers from this problem?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2026-02-18 20:39:06 UTC  
> Url: https://github.com/boostorg/optional/issues/111#issuecomment-3923097543  

The use case you describe is intentionally not supported. It is an incorrect usage of this library to try to get a reference to a non-existing contained value.  
  
Instead, I would recommend first creating the contained value using `T`'s default constructor, and then getting a reference to it:  
  
```c++  
optional<int> oi;  // no contained value  
oi.emplace();      // now containing a default-initialized value  
fill_as_you_please(*oi);  
```
