# #227 - how to set size of 'protected_fixedsize_stack' ? [Closed]

> Username: lazychase  
> Created at: 2023-06-14 01:02:26 UTC  
> Updated at: 2023-06-14 03:06:50 UTC  
> Closed at: 2023-06-14 03:06:50 UTC  
> Url: https://github.com/boostorg/context/issues/227  

I am using stackful coroutines(not Coroutine2).   
The development environment is Windows, and the service environment is Linux.(boost 1.80. CMakeProject. win32(msvc 143, vcpkg)/amazonlinux-2)  
  
In the Windows environment, using ''segment stack'' doesn't seem easy., so I want to set a fixed stack size larger.  
(segment stack : https://www.boost.org/doc/libs/1_78_0/libs/coroutine2/doc/html/coroutine2/stack/segmented.html#segmented)  
  
==== boost\context\windows\protected_fixedsize_stack.hpp ===  
```  
namespace boost {  
namespace context {  
  
template< typename traitsT >  
class basic_protected_fixedsize_stack {  
private:  
    std::size_t     size_;  
  
public:  
    typedef traitsT traits_type;  
  
    basic_protected_fixedsize_stack( std::size_t size = traits_type::default_size() ) BOOST_NOEXCEPT_OR_NOTHROW :  
        size_( size) {  
    }  
```  
The stack size is set very small in my situation, so I want to change it. how can i do this?

---

## Comment 1

> Username: lazychase  
> Created at: 2023-06-14 03:06:50 UTC  
> Url: https://github.com/boostorg/context/issues/227#issuecomment-1590379521  

I temporarily applied like this  
  
=== vcpkg\installed\x64-windows\include\boost\coroutine ===  
```  
namespace boost {  
namespace coroutines {  
  
struct attributes  
{  
    std::size_t     size;  
    flag_unwind_t   do_unwind;  
  
    attributes() BOOST_NOEXCEPT :  
        //size( stack_allocator::traits_type::default_size() ),   --> In my environment the 'default size' is 65536  
        size(stack_allocator::traits_type::default_size() * 10 ),  
        do_unwind( stack_unwind)  
    {  
    }  
```
