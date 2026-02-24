# #312 - Concepts build error async_result.hpp [Closed]

> Username: leleftheriades  
> Created at: 2019-12-24 11:19:08 UTC  
> Updated at: 2020-12-30 01:12:16 UTC  
> Closed at: 2020-12-30 01:12:15 UTC  
> Url: https://github.com/boostorg/asio/issues/312  

In **Visual Studio 16.3**  
I set the following project property:  
**C++ Language Standard:** Preview - Features from the Latest C++ Working Draft (/std:c++latest)  
  
Compilation fails for async_result.hpp

---

## Comment 1

> Username: 215020267  
> Created at: 2020-01-02 08:53:09 UTC  
> Updated at: 2020-01-02 09:58:34 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-570147756  

We also encountered the same problem.  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
  
**ErrorMessage:**  
.\boost/asio/async_result.hpp(42): error C2062: type 'bool' unexpected  
.\boost/asio/async_result.hpp(43): error C2143: syntax error: missing ';' before '{'  
.\boost/asio/async_result.hpp(43): error C2447: '{': missing function header (old-style formal list?)  
.\boost/asio/async_result.hpp(54): error C7568: argument list missing after assumed function template 'callable_with'  
.\boost/asio/async_result.hpp(56): note: see reference to class template instantiation 'boost::asio::detail::is_completion_handler_for<T,R(Args...)>' being compiled  
.\boost/asio/async_result.hpp(54): error C2059: syntax error: '>'  
.\boost/asio/async_result.hpp(55): error C3770: 'unknown-type': is not a valid base class  
.\boost/asio/async_result.hpp(61): error C2062: type 'bool' unexpected  
.\boost/asio/async_result.hpp(62): error C2065: 'T': undeclared identifier  
.\boost/asio/async_result.hpp(62): error C2923: 'boost::asio::detail::is_completion_signature': 'T' is not a valid template type argument for parameter 'T'  
.\boost/asio/async_result.hpp(62): error C2955: 'boost::asio::detail::is_completion_signature': use of class template requires template argument list  
.\boost/asio/async_result.hpp(32): note: see declaration of 'boost::asio::detail::is_completion_signature'  
.\boost/asio/async_result.hpp(67): error C2061: syntax error: identifier 'completion_signature'  
.\boost/asio/async_result.hpp(68): error C2062: type 'bool' unexpected  
.\boost/asio/async_result.hpp(69): error C2065: 'T': undeclared identifier  
.\boost/asio/async_result.hpp(69): error C2065: 'Signature': undeclared identifier  
.\boost/asio/async_result.hpp(69): error C2923: 'boost::asio::detail::is_completion_handler_for': 'T' is not a valid template type argument for parameter 'T'  
.\boost/asio/async_result.hpp(69): error C2923: 'boost::asio::detail::is_completion_handler_for': 'Signature' is not a valid template type argument for parameter 'Signature'  
.\boost/asio/async_result.hpp(69): error C2955: 'boost::asio::detail::is_completion_handler_for': use of class template requires template argument list  
.\boost/asio/async_result.hpp(48): note: see declaration of 'boost::asio::detail::is_completion_handler_for'  
.\boost/asio/async_result.hpp(105): error C2039: 'completion_signature': is not a member of 'boost::asio'  
.\boost/asio/async_result.hpp(25): note: see declaration of 'boost::asio'  
.\boost/asio/async_result.hpp(105): error C2061: syntax error: identifier 'completion_signature'  
.\boost/asio/async_result.hpp(144): error C2061: syntax error: identifier 'completion_handler_for'  
.\boost/asio/async_result.hpp(190): note: see reference to class template instantiation 'boost::asio::async_result<CompletionToken>' being compiled  
.\boost/asio/async_result.hpp(144): error C2988: unrecognizable template declaration/definition  
.\boost/asio/async_result.hpp(144): error C2059: syntax error: ','  
.\boost/asio/async_result.hpp(150): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
.\boost/asio/async_result.hpp(194): error C2039: 'completion_signature': is not a member of 'boost::asio'  
.\boost/asio/async_result.hpp(25): note: see declaration of 'boost::asio'  
.\boost/asio/async_result.hpp(194): error C2061: syntax error: identifier 'completion_signature'  
.\boost/asio/async_result.hpp(195): error C2065: 'Signature': undeclared identifier  
.\boost/asio/async_result.hpp(196): error C2977: 'boost::asio::async_result': too many template arguments  
.\boost/asio/async_result.hpp(107): note: see declaration of 'boost::asio::async_result'  
.\boost/asio/async_result.hpp(198): error C2913: explicit specialization; 'boost::asio::async_result' is not a specialization of a class template  
.\boost/asio/async_result.hpp(205): error C2039: 'completion_signature': is not a member of 'boost::asio'  
.\boost/asio/async_result.hpp(25): note: see declaration of 'boost::asio'  
.\boost/asio/async_result.hpp(205): error C2061: syntax error: identifier 'completion_signature'  
.\boost/asio/async_result.hpp(211): error C2065: 'Signature': undeclared identifier  
.\boost/asio/async_result.hpp(251): note: see reference to class template instantiation 'boost::asio::async_completion<CompletionToken>' being compiled  
.\boost/asio/async_result.hpp(250): error C2065: 'Signature': undeclared identifier  
.\boost/asio/async_result.hpp(250): error C2955: 'boost::asio::async_result': use of class template requires template argument list  
.\boost/asio/async_result.hpp(195): note: see declaration of 'boost::asio::async_result'  
.\boost/asio/async_result.hpp(258): error C2955: 'boost::asio::async_result': use of class template requires template argument list  
.\boost/asio/async_result.hpp(195): note: see declaration of 'boost::asio::async_result'  
.\boost/asio/async_result.hpp(259): note: see reference to class template instantiation 'boost::asio::detail::async_result_helper<CompletionToken,Signature>' being compiled  
.\boost/asio/async_result.hpp(288): error C2672: 'boost::asio::detail::async_result_initiate_memfn_helper': no matching overloaded function found  
.\boost/asio/async_result.hpp(292): note: see reference to class template instantiation 'boost::asio::detail::async_result_has_initiate_memfn<CompletionToken,Signature>' being compiled  
.\boost/asio/async_result.hpp(290): error C3206: 'boost::asio::detail::async_result_initiate_memfn_helper': invalid template argument for 'T', missing template argument list on class template 'boost::asio::async_result'  
.\boost/asio/async_result.hpp(281): note: see declaration of 'boost::asio::detail::async_result_initiate_memfn_helper'  
.\boost/asio/async_result.hpp(290): error C3206: 'boost::asio::detail::async_result_initiate_memfn_helper': invalid template argument for '<unnamed-symbol>', missing template argument list on class template 'boost::asio::async_result'  
.\boost/asio/async_result.hpp(278): note: see declaration of 'boost::asio::detail::async_result_initiate_memfn_helper'  
.\boost/asio/async_result.hpp(350): error C2039: 'completion_signature': is not a member of 'boost::asio'  
.\boost/asio/async_result.hpp(25): note: see declaration of 'boost::asio'  
.\boost/asio/async_result.hpp(350): error C2061: syntax error: identifier 'completion_signature'  
.\boost/asio/async_result.hpp(353): error C2065: 'Signature': undeclared identifier  
.\boost/asio/async_result.hpp(353): error C2923: 'boost::asio::detail::async_result_has_initiate_memfn': 'Signature' is not a valid template type argument for parameter 'Signature'  
.\boost/asio/async_result.hpp(354): error C2760: syntax error: unexpected token '>', expected 'expression'  
.\boost/asio/async_result.hpp(360): error C2065: 'Initiation': undeclared identifier  
.\boost/asio/async_result.hpp(360): error C2065: 'initiation': undeclared identifier  
.\boost/asio/async_result.hpp(361): error C2065: 'token': undeclared identifier  
.\boost/asio/async_result.hpp(362): error C2065: 'Args': undeclared identifier  
.\boost/asio/async_result.hpp(362): warning C4346: 'boost::asio::detail::async_result_has_initiate_memfn::value': dependent name is not a type  
.\boost/asio/async_result.hpp(362): note: prefix with 'typename' to indicate a type  
.\boost/asio/async_result.hpp(362): error C2988: unrecognizable template declaration/definition  
.\boost/asio/async_result.hpp(362): error C2059: syntax error: '...'  
.\boost/asio/async_result.hpp(363): error C2143: syntax error: missing ';' before '{'  
.\boost/asio/async_result.hpp(363): error C2447: '{': missing function header (old-style formal list?)  
.\boost/asio/async_result.hpp(371): error C2039: 'completion_signature': is not a member of 'boost::asio'  
.\boost/asio/async_result.hpp(25): note: see declaration of 'boost::asio'  
.\boost/asio/async_result.hpp(371): error C2061: syntax error: identifier 'completion_signature'

---

## Comment 2

> Username: davidstone  
> Created at: 2020-01-22 17:45:53 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-577303929  

Two things are causing this issue.  
  
The first is that `BOOST_ASIO_CONCEPT` expands to `concept bool` instead of just `concept`. Some compilers also accept this older syntax, but it is not what's in the current draft of the standard.  
  
The second problem is this code from async_result.hpp  
  
```  
template <typename T>  
BOOST_ASIO_CONCEPT completion_signature =  
  detail::is_completion_signature<T>::value;  
```  
```  
template <typename T, completion_signature Signature>  
BOOST_ASIO_CONCEPT completion_handler_for =  
  detail::is_completion_handler_for<T, Signature>::value;  
```  
  
The first snippet is defining a concept. The second is trying to constrain the concept definition on the first concept, which is incorrect. Assuming the logic is otherwise correct, what you need to do here is instead (for the second snippet)  
  
```  
template <typename T, typename Signature>  
BOOST_ASIO_CONCEPT completion_handler_for =  
  completion_signature<Signature> and detail::is_completion_handler_for<T, Signature>::value;  
```  
  
Alternatively, `is_completion_handler_for` could be defined to require `completion_signature` for its second parameter.

---

## Comment 3

> Username: stevefan1999-personal  
> Created at: 2020-01-23 07:50:06 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-577555864  

Disable MSVC concepts for the time being:  
```cmake  
if (MSVC)  
  add_definitions(-DBOOST_ASIO_DISABLE_CONCEPTS)  
endif()  
```

---

## Comment 4

> Username: davidstone  
> Created at: 2020-01-23 17:24:53 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-577783828  

This does not just affect MSVC. clang trunk fails to compile the code due to the second issue I outlined, and I wouldn't be surprised if gcc trunk will fail, too.

---

## Comment 5

> Username: tchaikov  
> Created at: 2020-02-12 07:34:56 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-585071282  

GCC-10 also fails to build.   
```  
/opt/ceph/include/boost/asio/async_result.hpp:70:20: error: a variable concept cannot be constrained  
   70 | BOOST_ASIO_CONCEPT completion_handler_for =  
      |                    ^~~~~~~~~~~~~~~~~~~~~~  
/opt/ceph/include/boost/asio/async_result.hpp:492:20: error: a variable concept cannot be constrained  
  492 | BOOST_ASIO_CONCEPT completion_token_for = requires(T&& t)  
      |                    ^~~~~~~~~~~~~~~~~~~~  
```  
  
the reason why it compiles with GCC-9 and before is that `async_result.hpp` is using the bug of https://gcc.gnu.org/bugzilla/show_bug.cgi?id=67658 , which was in turn fixed in GCC-10.

---

## Comment 6

> Username: stevefan1999-personal  
> Created at: 2020-02-12 13:28:38 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-585204867  

Hmm, it seems like it is a complete semantic change, so not a regression, but more serious than that.

---

## Comment 7

> Username: MakersF  
> Created at: 2020-03-24 15:40:30 UTC  
> Updated at: 2020-03-24 15:56:14 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-603314960  

I can confirm this fails to compile with clang-11  
  
There is a warning for the old way of specifying a concept  
  
```  
/boost_1_72_0/boost/asio/async_result.hpp:70:20: warning: ISO C++20 does not permit the 'bool' keyword after 'concept' [-Wconcepts-ts-compat]  
BOOST_ASIO_CONCEPT completion_handler_for =  
                   ^  
```  
  
and there is an error for constraining the concept  
  
```  
/boost_1_72_0/boost/asio/async_result.hpp:70:20: error: concept cannot have associated constraints  
```  
  
I edited the source following https://github.com/boostorg/asio/issues/312#issuecomment-577303929 and it worked (there is another instance of the same problem, but removing the concept from the template type and moving it `and`ed with the requires fixes it)

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-03-30 14:08:15 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-606021675  

Related Downstream:  boostorg/beast#1884

---

## Comment 9

> Username: yumetodo  
> Created at: 2020-04-26 04:29:59 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-619480063  

I have just faced this issue when I specified `/std:c++latest` to use template syntax for generic lambdas.

---

## Comment 10

> Username: ghost  
> Created at: 2020-12-30 01:12:14 UTC  
> Url: https://github.com/boostorg/asio/issues/312#issuecomment-752293328  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#738](https://github.com/chriskohlhoff/asio/issues/738).
