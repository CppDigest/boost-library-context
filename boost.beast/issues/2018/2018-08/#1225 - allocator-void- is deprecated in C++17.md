# #1225 - allocator<void> is deprecated in C++17 [Closed]

> Username: DragonOsman  
> Created at: 2018-08-14 18:07:24 UTC  
> Updated at: 2018-08-22 12:31:14 UTC  
> Closed at: 2018-08-22 12:31:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1225  

Hello.  
  
Boost.Beast is using `allocator<void>` apparently.  In here:  
```cpp  
template <typename T>  
inline typename associated_allocator<T>::type  
get_associated_allocator(const T& t) BOOST_ASIO_NOEXCEPT  
{  
  return associated_allocator<T>::get(t);  
}  
```  
It's saying it for this line: `inline typename associated_allocator<T>::type`.  
  
And I also got a whole bunch of warnings, some of which are weird (here's a Gist link for build messages): https://gist.github.com/DragonOsman/6f3c0f7fd2f52b1016e80f355f68b8dd .    
  
I want to ask for help especially on the preprocessor macros it says aren't defined.  I'm asking here because what I'm asking about are warnings generated because of code from Beast.  [I'm not asking about the warnings generated because of code in the standard library.]

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-14 20:14:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-413000821  

> In here  
  
In where? Which file and line number is `allocator<void>` being used?  
  
> preprocessor macros it says aren't defined  
  
If you're talking about `BOOST_BEAST_DOXYGEN` that is something I can probably fix. If you want to make the warnings go away, just define `BOOST_BEAST_DOXYGEN` to `0` in your build scripts.

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-08-16 01:36:01 UTC  
> Updated at: 2018-08-16 01:52:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-413394242  

It's in `associated_allocator.hpp`, line 103, and also in `bind_handler.hpp`, line 129.    
  
Some other warnings:  
```  
1>c:\boost_1_68_0\boost\intrusive\detail\std_fwd.hpp(30): warning C4643: Forward declaring 'less' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\intrusive\detail\std_fwd.hpp(33): warning C4643: Forward declaring 'equal_to' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\intrusive\detail\std_fwd.hpp(35): warning C4643: Forward declaring 'input_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\intrusive\detail\std_fwd.hpp(36): warning C4643: Forward declaring 'forward_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\intrusive\detail\std_fwd.hpp(37): warning C4643: Forward declaring 'bidirectional_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\intrusive\detail\std_fwd.hpp(38): warning C4643: Forward declaring 'random_access_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\move\detail\iterator_traits.hpp(31): warning C4643: Forward declaring 'input_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\move\detail\iterator_traits.hpp(32): warning C4643: Forward declaring 'forward_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\move\detail\iterator_traits.hpp(33): warning C4643: Forward declaring 'bidirectional_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\move\detail\iterator_traits.hpp(34): warning C4643: Forward declaring 'random_access_iterator_tag' in namespace std is not permitted by the C++ Standard.  
1>c:\boost_1_68_0\boost\move\detail\iterator_traits.hpp(35): warning C4643: Forward declaring 'output_iterator_tag' in namespace std is not permitted by the C++ Standard.  
```  
There are more instance of such warnings, like from `container_fwd.hpp` (more than one line).    
  
And the preprocessor macros I see right now are:  
`BOOST_BEAST_DOXYGEN`,   
`BOOST_BEAST_MULTI_BUFFER_DEBUG_CHECK`,  
  
I've added `DBG` in VS' preprocessor directives.  When I try to add the other two, though, I get errors.  And it gives that warning about macros being undefined for all of the macros mentioned in `#if` checks anywhere in Beast, actually.  I think I'll try to `#define` all of them to 0 like you suggested (thanks).    
  
Edit: Trying to do `#define BOOST_BEAST_DOXYGEN = 0` triggers this error:  
```  
boost_1_68_0\boost\beast\core\error.hpp(30): fatal error C1017: invalid integer constant expression  
```  
The line in question, line 30 in error.hpp, is the first one seen here:  
```cpp  
#if BOOST_BEAST_DOXYGEN  
error_category const&  
generic_category();  
#else  
using boost::system::generic_category;  
#endif  
```  
It's probably better to leave the macros undefined.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-16 03:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-413410972  

> It's in associated_allocator.hpp, line 103, and also in bind_handler.hpp, line 129.  
  
There is no file in beast called associated_allocator.hpp. What is the full path to that file? Also, there are multiple files called bind_handler.hpp in Beast, what is the full path to that file?

---

## Comment 4

> Username: djarek  
> Created at: 2018-08-16 11:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-413509866  

The deprecation warning is about the specialization `std::allocator<void>` which is used in ASIO as the default proto-allocator. Unfortunately, MSVC does not seem to be proto-allocator aware, so I think the warning is bogus. I guess we should ask @glenfe just to make sure :).

---

## Comment 5

> Username: DragonOsman  
> Created at: 2018-08-17 15:29:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-413902212  

@vinniefalco I'll get the full path for the latter file and get back to you on that.  Need to compile the code again with the /Wall compiler flag (there a lot of warnings that seem meaningless that are generated in this setting, so I try to mostly keep it at /W4).    
  
@djarek Thanks for the reply and info.  I'll wait, then.

---

## Comment 6

> Username: DragonOsman  
> Created at: 2018-08-20 20:17:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-414448965  

The `std::allocator<void>` deprecation warnings are from these files:  
```  
1. boost_1_68_0\boost\asio\associated_allocator.hpp (103)  
2. boost_1_68_0\boost\beast\core\detail\bind_handler.hpp (129)  
3. boost_1_68_0\boost\beast\core\impl\buffered_read_stream.ipp(58)  
4. boost_1_68_0\boost\beast\http\impl\read.ipp(71)  
5. boost_1_68_0\boost\beast\http\impl\read.ipp(244)  
6. boost_1_68_0\boost\beast\http\impl\read.ipp(374)  
7. boost_1_68_0\boost\beast\http\impl\write.ipp(88)  
8. boost_1_68_0\boost\beast\http\impl\write.ipp(241)  
9. boost_1_68_0\boost\beast\http\impl\write.ipp(360)  
```  
The number in parentheses is the line number.    
  
I also have an "unreachable code" warning from `boost_1_68_0\boost\beast\core\impl\buffers_cat.ipp(186)`.  
  
`boost_1_68_0\boost\beast\core\impl\buffers_suffix.ipp(221): warning C4868: compiler may not enforce left-to-right evaluation order in braced initializer list`.  You can probably ignore this one; I'm not sure.

---

## Comment 7

> Username: glenfe  
> Created at: 2018-08-20 21:42:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-414472872  

https://github.com/chriskohlhoff/asio/issues/290#issuecomment-377727614

---

## Comment 8

> Username: DragonOsman  
> Created at: 2018-08-22 12:31:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1225#issuecomment-415015484  

Thanks for the link.  So it's okay, then.
