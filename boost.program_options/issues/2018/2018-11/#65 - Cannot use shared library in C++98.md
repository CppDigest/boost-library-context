# #65 - Cannot use shared library in C++98 [Closed]

> Username: jbeich  
> Created at: 2018-11-12 18:38:08 UTC  
> Updated at: 2022-06-16 13:51:37 UTC  
> Closed at: 2022-06-16 13:51:37 UTC  
> Url: https://github.com/boostorg/program_options/issues/65  

After https://github.com/boostorg/function/pull/25 building a C++ < 11 consumer fails. Affects at least [gearmand](https://ptpb.pw/C_HL), [pdfedit](https://ptpb.pw/98Fg), [uhd](https://ptpb.pw/oVqK). CC @Kojoley  
  
```c++  
$ cat >a.cc  
#include <boost/program_options.hpp>  
  
int main(int argc, char **argv) {  
  boost::program_options::options_description desc("foo");  
  boost::program_options::parse_command_line(argc, argv, desc);  
  return 0;  
}  
  
$ c++ -std=gnu++98 a.cc -lboost_program_options  
>>> referenced by a.cc  
>>>               /tmp/a-fa8c69.o:(boost::program_options::basic_command_line_parser<char>::extra_parser(boost::function1<std::__1::pair<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&>))  
c++: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-11-12 18:44:00 UTC  
> Updated at: 2018-11-12 18:44:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-437987697  

It is not a surprise I mentioned the roots of the problem in https://github.com/boostorg/function/pull/25#issuecomment-431603996.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-11-12 19:09:42 UTC  
> Updated at: 2018-11-12 22:20:30 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-437995223  

<strike>Can you try to revert #25 locally and check with the latest boost::parameter? It looks like the actual problem is not even #25 change, but https://github.com/boostorg/parameter/commit/fb88905a99031d5f041ce4592c94743dfbf7259a (boost::parameter now uses `std::function` when it is available).

---

## Comment 3

> Username: jbeich  
> Created at: 2018-11-12 19:22:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-437998939  

boostorg/parameter@fb88905a9903 is missing on `master` branch, so nothing to revert. I'm testing the upcoming Boost 1.69.

---

## Comment 4

> Username: jbeich  
> Created at: 2018-11-12 19:29:51 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438001236  

Reverting boostorg/function#25 does help. It was the first thing I've tried.

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-11-12 22:28:45 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438050678  

Can you please check if https://github.com/Kojoley/program_options/commit/99727ac7104bd3f4c04d795d86808ac3e7e0f3ed solves the problem?

---

## Comment 6

> Username: jbeich  
> Created at: 2018-11-12 22:54:59 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438058170  

After 99727ac7104b Boost itself [fails to build](https://ptpb.pw/EHY5):  
```c++  
In file included from libs/program_options/src/options_description.cpp:13:  
./boost/program_options/parsers.hpp:172:38: error: too few template arguments for class template 'pair'  
                       function<std::pair<std::string(std::string)>,  
                                     ^  
./boost/container/detail/std_fwd.hpp:39:8: note: template is declared here  
struct pair;  
       ^  
In file included from libs/program_options/src/options_description.cpp:13:  
./boost/program_options/parsers.hpp:173:54: error: no viable conversion from 'boost::program_options::ext_parser' (aka 'function<pair<basic_string<char, char_traits<char>, allocator<char> >, basic_string<char, char_traits<char>, allocator<char> > > (const basic_string<char, char_traits<char>, allocator<char> > &)>') to 'int'  
                                 const std::string&> ext  
                                                     ^  
./boost/function/function_template.hpp:955:5: note: candidate function  
    operator safe_bool () const  
    ^  
./boost/program_options/parsers.hpp:173:54: note: passing argument to parameter 'ext' here  
                                 const std::string&> ext  
                                                     ^  
2 errors generated.  
```

---

## Comment 7

> Username: vprus  
> Created at: 2018-11-13 06:42:33 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438155261  

Replacing `function1` with `function` does not seem an appropriate fix for me. Sure, it might fix program_options, however it suggests that existing code using functionN will break, which I find rather undesirable, and IIUC it's not the behaviour that @pdimov wanted either.

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-11-13 11:35:24 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438235180  

>  it suggests that existing code using functionN will break  
  
I do not understand why `functionN` still is not deprecated and used in boost libraries. There is no compilers on regression matrix that does not support partial template specialization. It is a straightforward fix to existing code to migrate to `function` and it only needed for the user who still uses `functionN`.  
  
On the other hand, the problem is that @jbeich uses libraries compiled with C++11 with C++03 apps. I do not think we should bother about this case anymore.

---

## Comment 9

> Username: jbeich  
> Created at: 2018-11-13 13:20:18 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438263043  

@Kojoley, after fixing a typo in 99727ac7104b C++98 example and the affected consumers ([gearmand](https://ptpb.pw/VAGt), [pdfedit](https://ptpb.pw/Ba1X), [uhd](https://ptpb.pw/KEaP)) build fine. Testing all Boost consumers for regressions downstream (~400 packages) may take more time.

---

## Comment 10

> Username: pdimov  
> Created at: 2018-11-13 18:02:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/65#issuecomment-438374955  

It's probably best to revert the Function change for now.
