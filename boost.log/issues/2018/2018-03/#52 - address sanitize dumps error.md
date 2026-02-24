# #52 - address sanitize dumps error [Closed]

> Username: yuwenyong  
> Created at: 2018-03-30 02:13:16 UTC  
> Updated at: 2018-03-30 08:49:08 UTC  
> Closed at: 2018-03-30 07:54:21 UTC  
> Url: https://github.com/boostorg/log/issues/52  

I'm using Boost 1.65. It seems newer version has the same problem. Even the first example has the same problem.  
I compile my code with -fsanitize=address -fsanitize=undefined options. When run the application it will dump errors listed below but not crash:  
  
`/usr/local/include/boost/smart_ptr/detail/shared_count.hpp:426:36: runtime error: member call on address 0x6030000134b0 which does not point to an object of type 'sp_counted_base'  
0x6030000134b0: note: object is of type 'boost::detail::sp_counted_impl_p<boost::log::v2_mt_posix::core>'  
 03 00 80 6a  40 2a 4f fb b6 7f 00 00  05 00 00 00 01 00 00 00  b0 ed 00 00 20 60 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::detail::sp_counted_impl_p<boost::log::v2_mt_posix::core>'  
/usr/local/include/boost/log/attributes/attribute_value.hpp:200:37: runtime error: member call on address 0x60400000cad0 which does not point to an object of type 'impl'  
0x60400000cad0: note: object is of type 'boost::log::v2_mt_posix::attributes::attribute_value_impl<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >'  
 01 00 00 43  88 2a 4f fb b6 7f 00 00  01 00 00 00 be be be be  b0 22 01 00 30 60 00 00  1d 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::attributes::attribute_value_impl<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >'`

---

## Comment 1

> Username: Lastique  
> Created at: 2018-03-30 07:54:21 UTC  
> Url: https://github.com/boostorg/log/issues/52#issuecomment-377470798  

This is a false positive because in both cases the pointer is to a base class and the object is of a derived class.

---

## Comment 2

> Username: yuwenyong  
> Created at: 2018-03-30 08:18:36 UTC  
> Url: https://github.com/boostorg/log/issues/52#issuecomment-377475232  

@Lastique   Then how to solve it to avoid displaying these messages

---

## Comment 3

> Username: Lastique  
> Created at: 2018-03-30 08:24:55 UTC  
> Url: https://github.com/boostorg/log/issues/52#issuecomment-377476367  

I'm not familiar with sanitizers and don't know if you can selectively disable that message. If not, just disable the sanitizer.  
  
Also, I would recommend reporting this to your compiler vendor.

---

## Comment 4

> Username: yuwenyong  
> Created at: 2018-03-30 08:30:49 UTC  
> Updated at: 2018-03-30 08:49:08 UTC  
> Url: https://github.com/boostorg/log/issues/52#issuecomment-377477450  

OK.  But even the hello world example dumps these errors. And it is not wise to disable asan.  Hope this problem will be solved in the future.  
  
Sorry, providing a little bit more information:I'm using GCC 5.4
