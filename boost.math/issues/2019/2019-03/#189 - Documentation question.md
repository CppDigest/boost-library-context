# #189 - Documentation question [Closed]

> Username: robertramey  
> Created at: 2019-03-14 19:01:35 UTC  
> Updated at: 2019-05-08 17:15:02 UTC  
> Closed at: 2019-05-08 13:36:38 UTC  
> Url: https://github.com/boostorg/math/issues/189  

In the documentation at:  
  
https://www.boost.org/doc/libs/1_69_0/libs/math/doc/html/math_toolkit/high_precision/use_multiprecision.html  
  
I find the following text:  
  
Define a text string which is a C++ comment with the program licence, copyright etc. You could of course, tailor this to your needs, including your copyright claim. There are versions of array provided by Boost.Array in boost::array or the C++11 std::array, but since not all platforms provide C++11 support, this program provides the Boost version as fallback.  
  
static const char* prolog =  
{  
  "// Use, modification and distribution are subject to the\n"  
  "// Boost Software License, Version 1.0.\n"  
  "// (See accompanying file LICENSE_1_0.txt\n"  
  "// or copy at ""http://www.boost.org/LICENSE_1_0.txt)\n\n"  
  
  "// Copyright ???? 2013.\n\n"  
  
  "// Use boost/array if std::array (C++11 feature) is not available.\n"  
  "#ifdef  BOOST_NO_CXX11_HDR_ARRAY\n"  
  "#include <boost/array/array.hpp>\n"  
  "#else\n"  
  "#include <array>\n"  
  "#endif\n\n"  
};  
  
  
using boost::multiprecision::cpp_dec_float_50;  
using boost::math::constants::pi;  
// VS 2010 (wrongly) requires these at file scope, not local scope in `main`.  
// This program also requires `-std=c++11` option to compile using Clang and GCC.  
  
int main()  
{  
  
This seems way out of context to me.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-03-14 19:34:10 UTC  
> Url: https://github.com/boostorg/math/issues/189#issuecomment-473026456  

100% agree, the sample output at the end is mangled as well.  All that section needs to say is that that our example program has a preamble which prints out some boilerplate.  The example should probably be using cpp_bin_float_50 as the example multiprecision type as well.

---

## Comment 2

> Username: pabristow  
> Created at: 2019-03-15 14:09:16 UTC  
> Url: https://github.com/boostorg/math/issues/189#issuecomment-473299970  

Shal I have a go at re-writing this?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-03-16 08:50:08 UTC  
> Url: https://github.com/boostorg/math/issues/189#issuecomment-473512659  

>Shal I have a go at re-writing this?  
  
Please.  Thanks!

---

## Comment 4

> Username: pabristow  
> Created at: 2019-05-08 13:36:38 UTC  
> Url: https://github.com/boostorg/math/issues/189#issuecomment-490488362  

Believed fixed in develop c759981a2..200e1b914  develop -> develop.  
  
[CI SKIP] in comment, so all math docs need rebuilding.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-05-08 17:15:02 UTC  
> Url: https://github.com/boostorg/math/issues/189#issuecomment-490572440  

Thanks Paul.  
  
I note that big_seventh.cpp is still using cpp_dec_float?
