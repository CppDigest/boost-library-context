# #176 - Stack overflow in basic_parser<Handler>:: parse_array [Closed]

> Username: pauldreik  
> Created at: 2020-08-20 07:40:05 UTC  
> Updated at: 2020-08-20 16:33:14 UTC  
> Closed at: 2020-08-20 16:33:14 UTC  
> Url: https://github.com/boostorg/json/issues/176  

When polishing up the fuzzer for the pull request, the fuzzer segfaulted. Using gdb, it could be seen that the call stack was very deep. Using clang 8 instead of 10, the sanitizers caught the problem and gave the following output:  
```AddressSanitizer:DEADLYSIGNAL  
=================================================================  
==55149==ERROR: AddressSanitizer: stack-overflow on address 0x7fff99e5dfe8 (pc 0x00000055044e bp 0x7fff99e5e090 sp 0x7fff99e5dfc0 T0)  
    #0 0x55044d in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1866  
    #1 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #2 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #3 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #4 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #5 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #6 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #7 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
    #8 0x5509d9 in char const* boost::json::basic_parser<validate(std::basic_string_view<char, std::char_traits<char> >)::null_parser::handler>::parse_array  
<true, false, false, false>(char const*) /home/banan/code/json/fuzzing/../include/boost/json/basic_parser.hpp:1912:14  
#snip a few hundred repeated lines  
```  
  
The source line is https://github.com/CPPAlliance/json/blob/16e8b9d1af0ec8ff18a75ebf7b1dd887768b3d73/include/boost/json/basic_parser.hpp#L1910-L1915  
  
The offending input is on base 64 form (gzipped, the input should have sha1 dd71568af00470dd417db9d5b36bc73e31bcc2f3):  
```  
H4sIALAhPl8AA+2ZvU4CURCFqTHxGW42sRKMP4hKb2PhT7TbUKC5KgbQLEssjG9gfAALn8h3QmTX  
hMINjBnl3J3zFbIIhr3nmzMWxHEhVTchOjo/OXYXfpi6006axsnAPXSGw62oNn35Kbq/vPNXqXvs  
prduy/V9/9InUSuOOknifM/3/SCN2s/5u/PHuJ091hvb2UWajHx2dd3pDb8up08Go14v/8vpz+kN  
dQepv/n6DFd86//OX97sdyS4gbQB7+kHbEtSRf2YdDML96OAv9TL8OeA3y12SQC+TlXUj1kyN+yO  
AHU/Qd2sSfBTZIMF4OtUxXaUbIYAfJ1Eju2JKd0CKADfRAgsO0UL6riPSrWPbOsEBT+4JfaAXyXM  
wXaluaEF4OtURf2YdDMLqyeA30SYAz9FNlgAvk5VbEbJRgjA1Uh+j+2JKd0CKADfRAjgp2hlnhdE  
/ai2t6UqtqMsXVFt6wwB/BRZCgH4OlWxHSWbIQBfJ5Fje2K4AATg6wwB/BRZCgH4OlWxHSWbIQBf  
J1kO6hPBMVbDdpRc8ALwdYYAfooshQB8naqEdExqV8N2lFyIAvB1Ejm2J4YLQAC+zhDAT5GlEICv  
UxXbUbIZAvB1kiBQHyBOvRq2o+T/AwH4OkMAP0WWQgC+TlVsR8lmCMDXSeTYnpjSLYAC8E2EAH6K  
VuZ5QVSOantDqmI7ytKV07bOEGCKnFE1bEfJ7S0AXyeRI7UJqp5NFsAma4CfIkshAF+nKrajZDMW  
Bl8mWQA/5/VKpbK+08js7hw2m7Xc8Hh8Np6w2nKb+a+qr2/vLxPd9YP9vebKR8bGWrzb+ARX86Va  
RogAAA==  
```  
The file has a lot of left brackets, the beginning looks like this:  
```  
[[[[[[[[[[[[[[[[[[[[[[[[[[[  
    "JSON Test Patt[rn pass1",  
    {"object with 1 member":["arr element"]},  
    {},  
    [],  
    -42,  
    true,  
    false,  
   
    null,  
    {  
        "integer": [[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[   null,  
    {  
```  
  
To reproduce, on ubuntu 20.04:  
```sh  
apt install clang-8  
export CLANG=clang++-8  
./fuzz.sh # interrupt as soon as it starts fuzzing, no need to wait  
curl -O https://raw.githubusercontent.com/pauldreik/json/fuzz_01/fuzzing/old_crashes/basic_parser/minimized-from-58a10325bce45df3e0b4988f932e5a400a344687  
./fuzzer_basic_parser minimized-from-58a10325bce45df3e0b4988f932e5a400a344687  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-20 14:46:40 UTC  
> Url: https://github.com/boostorg/json/issues/176#issuecomment-677710814  

By default the parser is supposed to limit the maximum depth to 32

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-20 14:50:38 UTC  
> Url: https://github.com/boostorg/json/issues/176#issuecomment-677713154  

Sorry! When I was making the max depth change I didn't change these lines:  
https://github.com/CPPAlliance/json/blob/develop/include/boost/json/basic_parser.hpp#L1850  
https://github.com/CPPAlliance/json/blob/develop/include/boost/json/basic_parser.hpp#L1950  
  
These should be `++depth_`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-20 14:54:42 UTC  
> Url: https://github.com/boostorg/json/issues/176#issuecomment-677715517  

> I didn't change these lines:  
  
The tests should have failed then. This means we need more tests.

---

## Comment 4

> Username: sdkrystian  
> Created at: 2020-08-20 15:02:47 UTC  
> Url: https://github.com/boostorg/json/issues/176#issuecomment-677720179  

Yup, I'm adding more in the fix.
