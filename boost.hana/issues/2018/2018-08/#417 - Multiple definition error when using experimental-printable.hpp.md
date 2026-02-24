# #417 - Multiple definition error when using experimental/printable.hpp [Closed]

> Username: indiosmo  
> Created at: 2018-08-13 14:57:56 UTC  
> Updated at: 2019-04-04 20:19:35 UTC  
> Closed at: 2018-08-16 13:15:26 UTC  
> Url: https://github.com/boostorg/hana/issues/417  

Function print_detail::strip_type_junk(...) defined in experimental/printable.hpp line 101 seems to be missing the static keyword.  
  
```  
../../../services/order_router/test/test_service.o: In function `boost::hana::experimental::print_detail::strip_type_junk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)':  
/home/msi/cpp_workspace/boost_1_67_0/include/boost/hana/experimental/printable.hpp:101: multiple definition of `boost::hana::experimental::print_detail::strip_type_junk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)'  
../../../services/order_router/src/service.o:/home/msi/cpp_workspace/boost_1_67_0/include/boost/hana/experimental/printable.hpp:101: first defined here  
```

---

## Comment 1

> Username: indiosmo  
> Created at: 2019-04-04 19:45:33 UTC  
> Url: https://github.com/boostorg/hana/issues/417#issuecomment-480037249  

Hello, I updated from 1.67 to 1.69 and it appears this bug is back.  
The function is no longer inline:   
  
https://github.com/boostorg/hana/blob/be9878006194657fec8ca8ff1e4dccb5f6d95cc3/include/boost/hana/experimental/printable.hpp#L101  
```

---

## Comment 2

> Username: ricejasonf  
> Created at: 2019-04-04 20:19:35 UTC  
> Url: https://github.com/boostorg/hana/issues/417#issuecomment-480048382  

I don't think this bug fix was in any release. It is not even in the `master` branch yet.  
  
https://github.com/boostorg/hana/blob/boost-1.67.0/include/boost/hana/experimental/printable.hpp#L101
