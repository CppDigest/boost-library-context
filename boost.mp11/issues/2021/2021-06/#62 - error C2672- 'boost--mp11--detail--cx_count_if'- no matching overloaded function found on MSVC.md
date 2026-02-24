# #62 - error C2672: 'boost::mp11::detail::cx_count_if': no matching overloaded function found on MSVC [Closed]

> Username: QuellaZhang  
> Created at: 2021-06-16 06:18:27 UTC  
> Updated at: 2021-10-28 18:45:47 UTC  
> Closed at: 2021-10-28 18:45:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/62  

**Issue description:**  
Found Boost\libs\mp11 build error C2672 in MSVC, this issue can be reproduced on c92d2d0b6fa9ae77094416f8142a8128c0424941.  Could you please take a look?  
  
**Reproduce steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2. open a VS 2017 x86 command prompt and browse to f:\Boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\mp11\test  
  
**ErrorMessage:**  
.\boost/mp11/detail/mp_count.hpp(111): error C2672: 'boost::mp11::detail::cx_count_if': no matching overloaded function found  
libs\mp11\test\mp_count_if.cpp(38): note: see reference to class template instantiation 'boost::mp11::detail::mp_count_if_impl<L1,std::is_const>' being compiled  
libs\mp11\test\mp_count_if.cpp(38): note: see reference to alias template instantiation 'boost::mp11::mp_count_if<L1,std::is_const>' being compiled  
.\boost/mp11/detail/mp_count.hpp(111): error C3206: 'boost::mp11::detail::cx_count_if': invalid template argument for 'P', missing template argument list on class template 'std::is_const'  
.\boost/mp11/detail/mp_count.hpp(95): note: see declaration of 'boost::mp11::detail::cx_count_if'  
.\boost/mp11/detail/mp_count.hpp(111): error C3206: 'boost::mp11::detail::cx_count_if': invalid template argument for 'P', missing template argument list on class template 'std::is_volatile'  
.\boost/mp11/detail/mp_count.hpp(95): note: see declaration of 'boost::mp11::detail::cx_count_if'  
.\boost/mp11/detail/mp_count.hpp(111): error C3206: 'boost::mp11::detail::cx_count_if': invalid template argument for 'P', missing template argument list on class template 'std::is_pointer'  
.\boost/mp11/detail/mp_count.hpp(95): note: see declaration of 'boost::mp11::detail::cx_count_if'  
.\boost/mp11/detail/mp_count.hpp(111): error C3206: 'boost::mp11::detail::cx_count_if': invalid template argument for 'P', missing template argument list on class template 'is_even'  
.\boost/mp11/detail/mp_count.hpp(95): note: see declaration of 'boost::mp11::detail::cx_count_if'

---

## Comment 1

> Username: pdimov  
> Created at: 2021-06-16 14:53:05 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-862449035  

Does not reproduce for me with either VS2017 or VS2019. The code is in an `!BOOST_MP11_WORKAROUND( BOOST_MP11_MSVC, < 1930 )` block, are you sure you're using VS 2017? 1930 is VS 2021 (no released VS 2019 goes above 1930, 16.10 and 16.11 are 1929).

---

## Comment 2

> Username: QuellaZhang  
> Created at: 2021-06-17 02:48:42 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-862874709  

Hmm, VS2017 is a typo, we are using VS2019 + latest cl.exe to detect MSVC regression.  
  
Checked the version of MSVC, which is higher than 1930, the next VS released should be able to reproduce this issue. I changed `!BOOST_MP11_WORKAROUND( BOOST_MP11_MSVC, < 1930 )` => `!BOOST_MP11_WORKAROUND( BOOST_MP11_MSVC, < 1931 )` and the problem disappeared.  
  
BTW, the next major version of VS to be released is VS2022, see https://devblogs.microsoft.com/visualstudio/visual-studio-2022/.  
  
`F:\gitP\boostorg\boost>cl /Bv`  
`Microsoft (R) C/C++ Optimizing Compiler Version 19.30.30303.97 for x86`  
`Copyright (C) Microsoft Corporation.  All rights reserved.`  
`Compiler Passes:`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\cl.exe:        Version 19.30.30303.97`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\c1.dll:        Version 19.30.30303.97`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\c1xx.dll:      Version 19.30.30303.97`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\c2.dll:        Version 19.30.30303.97`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\c1xx.dll:      Version 19.30.30303.97`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\link.exe:      Version 14.30.30303.97`  
` C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\mspdb140.dll:  Version 14.30.30303.97`  
 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\1033\clui.dll: Version 19.30.30303.97`

---

## Comment 3

> Username: pdimov  
> Created at: 2021-06-17 02:56:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-862877459  

I will update the workaround once a compiler that reports 1930 is released, although a more preferable outcome from my point of view would be for the compiler to be fixed. :-) Maybe you could report the problem to the frontend team. :-)

---

## Comment 4

> Username: QuellaZhang  
> Created at: 2021-06-17 03:04:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-862880501  

Ok, I will confirm with the the frontend team.

---

## Comment 5

> Username: QuellaZhang  
> Created at: 2021-06-18 05:51:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-863775284  

@pdimov, confirmed and this issue has been fixed on MSVC today. The next version of VS should not contain this fix because it will be released soon, maybe next next version. :-)

---

## Comment 6

> Username: pdimov  
> Created at: 2021-07-06 14:40:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-874820647  

What is the expected `_MSC_VER` of the compiler that will have this fixed? 1931? 1932?

---

## Comment 7

> Username: pdimov  
> Created at: 2021-10-28 18:45:46 UTC  
> Url: https://github.com/boostorg/mp11/issues/62#issuecomment-954107203  

Tests pass on VS2022RC2, so I assume this has been fixed in the released compiler.
