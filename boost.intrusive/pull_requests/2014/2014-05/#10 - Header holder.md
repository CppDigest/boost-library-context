# #10 Header holder [Closed]

> Username: mateidavid  
> Created at: 2014-05-20 21:18:59 UTC  
> Updated at: 2014-06-12 10:10:12 UTC  
> Closed at: 2014-06-12 10:10:12 UTC  
> Url: https://github.com/boostorg/intrusive/pull/10  

### Summary  
  
In this branch, we introduce two new features for the boost intrusive library.  
#### A new container option  
  
We implement a new container option for all intrusive containers (excluding hashmaps) called `header_holder_type`. If given, an object of this type is used to hold the container header node. The given type must contain non-static methods `const_pointer get_node () const` and `pointer get_node ()` that are used to produce the header node pointer. Furthermore, the holder type must be default-constructible. If this option is not given, a default header holder is used instead, which allocates the header node inside the container struct (as before).  
  
The header node holder follows the following semantics. The holder is constructed by all container constructors, and destructed by the container destructor. Assignment, swap, and move operations (applied to the container) do not alter the header node holder or the header node pointer, but they might alter the header node.  
  
The primary use of this new container option is to allow an intrusive container to construct its header node outside of the container struct.  
#### Test cases for the "bounded pointers" use case  
  
In this use case, all values (and nodes) manipulated by an intrusive container live inside a bounded address space (think: a small array). In this case, 8-byte pointers can be replaced by much smaller (e.g., 1-byte) indexes. Prior to these changes, the header node of an intrusive container was always allocated inside the container struct, making it problematic for the container to work with bounded pointers. With the new option, the header node holder constructor is free to allocate the header node inside the appropriate bounded address space, allowing us to use bounded pointers in intrusive containers.  
  
A sample bounded pointer implementation of is presented and tested for in the intrusive library test suite.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-06-12 10:10:12 UTC  
> Url: https://github.com/boostorg/intrusive/pull/10#issuecomment-45851134  

Added a relatively highly modified proposal starting from this branch in:  
  
SHA-1: 563eca32de3747244d06dd3ffc07059ce28761e0  
- Added support for node and value pointers non-convertible from raw references  
  
Changes:  
  
-> removed the use of operator spellings  
-> value_traits::value_traits_ptr determines de type of the pointer formed from the stored value traits.  
-> tested in several gcc, clang, and msvc compilers and workarounded all compilation bugs  
-> normalized class/variable names to boost.intrusive conventions.  
-> Updated bounded_pointer implementation to use enable_if-ed conversions  
-> Replaced the use of std::vector with the latest boost::container::vector as in many compilers storing bounded_reference lead to compilation errors.  
-> Removed log calls.  
  
Testing test_main_template_bptr with AVL trees fails in several compilers in release mode (infinite loop or crash). I haven't determined if there is an error in AVL code or it's related to bounded_pointer limitations. Disabled this testing until further investigation is done.  
  
Once regression testing in develop is Ok, it will be merged to master, ready for Boost 1.56.  Documentation update still missing, but this feature will be experimental and undocumented until this approach is properly tested and refined.

---
