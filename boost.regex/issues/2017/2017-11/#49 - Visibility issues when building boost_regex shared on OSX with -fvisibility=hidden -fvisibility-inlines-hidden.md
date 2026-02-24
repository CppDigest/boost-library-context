# #49 - Visibility issues when building boost_regex shared on OSX with -fvisibility=hidden -fvisibility-inlines-hidden [Closed]

> Username: dioss-Machiel  
> Created at: 2017-11-03 13:14:08 UTC  
> Updated at: 2018-08-27 17:35:10 UTC  
> Closed at: 2018-08-25 17:06:42 UTC  
> Url: https://github.com/boostorg/regex/issues/49  

We tried to build a fully shared version of Boost for OSX with -fvisibility=hidden -fvisibility-inlines-hidden, however when linking boost_log_setup with boost_regex we got the following linker error:  
  
`Undefined symbols for architecture x86_64:  
  "boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int)", referenced from:  
      void boost::log::v2_mt_posix::aux::anonymous::matches_predicate::initializer<char>::operator()<boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > >(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >&) const in matches_relation_factory.o  
      void boost::log::v2_mt_posix::aux::anonymous::matches_predicate::initializer<wchar_t>::operator()<boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > >(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >&) const in matches_relation_factory.o  
  "boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::do_assign(wchar_t const*, wchar_t const*, unsigned int)", referenced from:  
      void boost::log::v2_mt_posix::aux::anonymous::matches_predicate::initializer<char>::operator()<boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > > >(boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >&) const in matches_relation_factory.o  
      void boost::log::v2_mt_posix::aux::anonymous::matches_predicate::initializer<wchar_t>::operator()<boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > > >(boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >&) const in matches_relation_factory.o  
  "boost::match_results<char const*, std::__1::allocator<boost::sub_match<char const*> > >::maybe_assign(boost::match_results<char const*, std::__1::allocator<boost::sub_match<char const*> > > const&)", referenced from:  
      boost::re_detail_106501::perl_matcher<char const*, std::__1::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() in matches_relation_factory.o  
      boost::re_detail_106501::perl_matcher<char const*, std::__1::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_match() in matches_relation_factory.o  
  "boost::match_results<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> > >::maybe_assign(boost::match_results<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> > > const&)", referenced from:  
      boost::re_detail_106501::perl_matcher<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> >, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::match_prefix() in matches_relation_factory.o  
      boost::re_detail_106501::perl_matcher<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> >, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::match_match() in matches_relation_factory.o  
  "boost::re_detail_106501::perl_matcher<char const*, std::__1::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::construct_init(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)", referenced from:  
      boost::re_detail_106501::perl_matcher<char const*, std::__1::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::perl_matcher(char const*, char const*, boost::match_results<char const*, std::__1::allocator<boost::sub_match<char const*> > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags, char const*) in matches_relation_factory.o  
  "boost::re_detail_106501::perl_matcher<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> >, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::construct_init(boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > > const&, boost::regex_constants::_match_flags)", referenced from:  
      boost::re_detail_106501::perl_matcher<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> >, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::perl_matcher(wchar_t const*, wchar_t const*, boost::match_results<wchar_t const*, std::__1::allocator<boost::sub_match<wchar_t const*> > >&, boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > > const&, boost::regex_constants::_match_flags, wchar_t const*) in matches_relation_factory.o  
ld: symbol(s) not found for architecture x86_64  
`  
  
Upon further investigation it looks like do_assign is not correctly exported from boost_regex, however compiling with define=BOOST_REGEX_NO_EXTERNAL_TEMPLATES fixes this issue.  
  
We looked at the preprocesser output and LLVM output, in the preprocessor output the visibility is set to default, however in the LLVM output the visibility is "weak hidden".   
  
We have no idea if this is a clang bug or a boost bug, hopefully someone can figure out what is going on.  
  
Compiler version:  
testPCs-iMac:boost_1_65_1 testpc$ clang -v  
Apple LLVM version 9.0.0 (clang-900.0.37)  
Target: x86_64-apple-darwin17.0.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-24 18:27:45 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415843525  

I can reproduce this with clang on Ubuntu, but either I'm going nuts or there's something very strange going on with the preprocessor.  
  
If we compile the file that instantiates and exports those template instances using the same options that b2 uses, except for preprocessing we get:  
```  
"clang++" -c -x c++ -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_REGEX_DYN_LINK=1 -I"../../.." -c libs/regex/src/instances.cpp  
```  
Then I can determine that the macro BOOST_REGEX_INSTANTIATE is NOT set inside boost/regex/v4/instantiate.hpp.  But the thing is, if I put:  
```  
#ifndef BOOST_REGEX_INSTANTIATE  
#error "Ha"  
#else  
#error "Hey"  
#endif  
```  
immediately prior to the inclusion of instantiate.hpp inside regex.hpp, then it's clear that the macro IS set immediately prior to the inclusion!!  
Huh?  Anyone any ideas what's going on here?

---

## Comment 2

> Username: Lastique  
> Created at: 2018-08-24 20:30:49 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415874280  

I'm not sure I reproduced your test accurately, but adding this `#ifndef` block at https://github.com/boostorg/regex/blob/develop/include/boost/regex/v4/regex.hpp#L93 prints "Ha" for me (i.e. the macro is *not* defined). It is defined immediately prior to instances.hpp inclusion, at https://github.com/boostorg/regex/blob/develop/include/boost/regex/v4/regex.hpp#L95 and https://github.com/boostorg/regex/blob/develop/include/boost/regex/v4/regex.hpp#L106.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-08-24 20:40:08 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415876479  

I'm not sure this is the source of the problem, but I suspect the problem might be because the functions themselves are not marked with visibility attributes, only the explicit template instantiations are (if I'm reading correctly through the macros). Maybe clang doesn't propagate visibility attributes attached to the explicit instantiations.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-08-24 22:46:41 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415901719  

After a few experiments it doesn't look like marking the function definitions visible makes any difference.  
  
Something strange is going on on the linking stage. For example, in the compiled object files, instances.o contains a visible weak definition of `boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int)`, but this definition is present in the final shared object as a *local* (hidden) non-weak symbol. The only public `do_assign` it contains is `boost::basic_regex<int, boost::icu_regex_traits>::do_assign(int const*, int const*, unsigned int)`, which is defined in icu.o. I'm not sure what causes the former `do_assign` to be demoted to a local symbol.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-08-25 12:18:23 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415965524  

OK, the isues I thought I was seeing with the preprocessor were cured by deleting boost/ and running ./b2 headers again.  
  
Now if I preprocess with either clang or gcc  
```  
clang++   -fPIC -m64 -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_REGEX_DYN_LINK=1 -I"../../.." -E instances.cpp  
```  
Then I see for example:  
```  
template __attribute__((__visibility__("default"))) void  
   match_results<const char*>::maybe_assign(  
      const match_results<const char*>& m);  
```  
So that's all OK then.  
But... if I build object files with gcc, and then link with either clang or gcc the resulting .so is fine.  Similarly, if I build the object files with clang and then link with gcc the .so does not have `maybe_assign` exported :(  
  
So then I built object files using:  
  
```  
g++   -fPIC -m64 -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_REGEX_DYN_LINK=1 -I"../../.." -c -o gcc-obj.o instances.cpp  
clang++   -fPIC -m64 -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_REGEX_DYN_LINK=1 -I"../../.." -c -o clang-obj.o instances.cpp  
```  
And examined them with nm, and both seems to have public symbols present, GCC compiled obj reports:  
  
```  
0000000000000000 W _ZN5boost13match_resultsIN9__gnu_cxx17__normal_iteratorIPKcNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEESaINS_9sub_matchISB_EEEE12maybe_assignERKSF_  
```  
  
While clang has an extra record:  
```  
0000000000000476 r .L__PRETTY_FUNCTION__._ZN5boost13match_resultsIN9__gnu_cxx17__normal_iteratorIPKcNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEESaINS_9sub_matchISB_EEEE12maybe_assignERKSF_  
0000000000000000 W _ZN5boost13match_resultsIN9__gnu_cxx17__normal_iteratorIPKcNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEESaINS_9sub_matchISB_EEEE12maybe_assignERKSF_  
```  
  
But none the less has the symbol as public/visible.  
  
Which pretty much mirrors what you found.  There's something very arcane going on, and my attempts to reproduce this with a small test case have all failed so far.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2018-08-25 17:06:41 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415983267  

I give up.  Fixed in https://github.com/boostorg/regex/commit/40ecdc3f8b52cc6833f05b37610e916824ba88c7 by disabling external templates.

---

## Comment 7

> Username: apolukhin  
> Created at: 2018-08-25 17:16:34 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415983900  

We did the same thing in our projects. Other tricks did not help.  
  
This is probably a clang issue. I do not report it because the behavior is strange but according to the C++ standard it is undefined, so it could be implementation specific.

---

## Comment 8

> Username: Lastique  
> Created at: 2018-08-25 19:28:13 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-415991297  

Thanks, John.  
  
I think it *should* be reported to clang, otherwise it won't get fixed. As a test case we could point to Boost.Regex prior to https://github.com/boostorg/regex/commit/40ecdc3f8b52cc6833f05b37610e916824ba88c7. Even if the standard does not define behavior wrt. shared libraries and visibility, the behavior we observe is certainly not desirable; the compiler should behave more like gcc.

---

## Comment 9

> Username: apolukhin  
> Created at: 2018-08-26 16:29:19 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-416051043  

Ok, I'll try to produce a minimized test case suitable for clang error report in a week or two. On success I'll fill a clang issue and post it here.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2018-08-26 17:40:02 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-416055689  

I had another attempt at producing a test case this morning - failed miserably :(

---

## Comment 11

> Username: jzmaddock  
> Created at: 2018-08-27 17:35:09 UTC  
> Url: https://github.com/boostorg/regex/issues/49#issuecomment-416304963  

Submitted clang issue:  https://bugs.llvm.org/show_bug.cgi?id=38724
