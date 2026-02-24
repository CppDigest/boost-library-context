# #19 - error: no type named 'basic_string_view' in namespace 'std' [Closed]

> Username: kybr  
> Created at: 2018-02-16 13:39:31 UTC  
> Updated at: 2018-02-20 19:53:18 UTC  
> Closed at: 2018-02-20 19:52:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/19  

Perhaps, it's just my system, but i got this error when compiling an example:  
  
```  
xx $ c++ -std=c++14 -I include example/quick_examples.cpp  
In file included from example/quick_examples.cpp:13:  
In file included from include/boost/pfr.hpp:12:  
In file included from include/boost/pfr/precise.hpp:14:  
In file included from include/boost/pfr/precise/ops.hpp:15:  
In file included from include/boost/pfr/precise/io.hpp:17:  
include/boost/pfr/detail/io.hpp:32:16: error: no type named 'basic_string_view' in namespace 'std'  
    const std::basic_string_view<CharT, Traits>& s) noexcept {  
          ~~~~~^  
include/boost/pfr/detail/io.hpp:32:33: error: expected ')'  
    const std::basic_string_view<CharT, Traits>& s) noexcept {  
                                ^  
include/boost/pfr/detail/io.hpp:31:26: note: to match this '('  
inline auto quoted_helper(  
                         ^  
include/boost/pfr/detail/io.hpp:33:22: error: use of undeclared identifier 's'  
  return std::quoted(s);  
                     ^  
3 errors generated.  
xx $  
```  
So, i edited `include/boost/pfr/detail/io.hpp` to remove the _basic_string_view_ declaration and just included the _string_view_ header:  
  
```C++  
// Copyright (c) 2016-2017 Antony Polukhin  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#ifndef BOOST_PFR_DETAIL_IO_HPP  
#define BOOST_PFR_DETAIL_IO_HPP  
#pragma once  
  
#include <boost/pfr/detail/config.hpp>  
  
#include <boost/pfr/detail/sequence_tuple.hpp>  
#include <iomanip>  
#include <iosfwd>  // stream operators  
  
// Forward declaration  
// namespace std {  
//   template <class CharT, class Traits> class basic_string_view;  
// }  
#include <string_view> // addded  
...  
```  
  
The the example compiled and worked. thanks.  
  
macOS 10.13.4 Beta (17E150g)  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin17.5.0

---

## Comment 1

> Username: Bu11etmagnet  
> Created at: 2018-02-16 17:03:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/19#issuecomment-366295569  

Classes from the standard library should not be forward-declared anyway. Doing so is undefined behavior. See for example https://stackoverflow.com/a/307408  
The right way is to include the appropriate header.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-02-20 19:53:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/19#issuecomment-367098769  

Many thanks for the report!
