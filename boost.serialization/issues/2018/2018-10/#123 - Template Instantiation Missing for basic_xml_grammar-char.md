# #123 - Template Instantiation Missing for basic_xml_grammar<char> [Closed]

> Username: epsilonomega2k  
> Created at: 2018-10-01 20:58:22 UTC  
> Updated at: 2018-10-10 15:49:17 UTC  
> Closed at: 2018-10-10 15:49:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/123  

Hello,  
On Ubuntu 18.04 the template instantiation for basic_xml_grammar<char> is missing from the dynamic library, but is present in the static library. Specifically look at the following output where I am checking the existence of the symbols for two functions load_start and parse_start_tag:  
  
~$ nm -D /usr/lib/x86_64-linux-gnu/libboost_serialization.so.1.62.0 | grep load_start  
000000000002d100 W _ZN5boost7archive18basic_xml_iarchiveINS0_12xml_iarchiveEE10load_startEPKc  
~$ nm -D /usr/lib/x86_64-linux-gnu/libboost_serialization.so.1.62.0 | grep parse_start_tag  
~$ nm /usr/lib/x86_64-linux-gnu/libboost_serialization.a | grep load_start  
nm: stl_port.o: no symbols  
0000000000000000 W _ZN5boost7archive18basic_xml_iarchiveINS0_12xml_iarchiveEE10load_startEPKc  
~$ nm /usr/lib/x86_64-linux-gnu/libboost_serialization.a | grep parse_start_tag  
nm: stl_port.o: no symbols  
0000000000000000 W _ZN5boost7archive17basic_xml_grammarIcE15parse_start_tagERSi  
                 U _ZN5boost7archive17basic_xml_grammarIcE15parse_start_tagERSi  
  
This appears to be related to the fact that the instantiation is in file xml_grammar.cpp which includes file basic_xml_grammar.ipp. Both of those are in libs/serialization/src, so they are accessible only during the compilation of boost itself, but not during compilation/linking of any software against boost. I will venture to guess that an optimizing compiler/linker has for some reason removed the unused instantiation in the dynamic library, but I could not verify that because I was unable to find the flags used for the compilation of the officially distributed library (by Ubuntu).  
  
I was able to copy basic_xml_grammar.ipp into my project together with the relevant instantiation code from xml_grammar.cpp (and the code of function init_chset() ). This causes the missing instantiation to appear in the object code of my own software and as a result everything compiles and links properly.  
  
I am raising this question with you rather than with Ubuntu at this time because I could not see the reason why basic_xml_grammar.ipp and the instantiation are in boost serialization rather than boost archive and why they are in the implementation of the library rather than the header files.   
  
If you are pondering why I need these in my code: because I implement my own archive xml_iarchive_nan which inherits everything from your standard xml_iarchive, but has special code to read nan in doubles because the standard library unfortunately does not read what it writes when it comes to doubles whose values are nan or inf.  
  
Let me know if you need a code sample and I will spend the time to generate minimal example that shows the issue. Also let me know if this is absolutely not relevant to you and you prefer me to have discussion with Ubuntu about their compile/link flags that have lost the relevant instantiation.  
  
Thank you.

---

## Comment 1

> Username: epsilonomega2k  
> Created at: 2018-10-02 18:53:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-426390067  

Here is the specific file and lines to blame: libs/serialization/build/Jamfile.v2 .  
The <cxxflags> for gcc are set to "-fvisibility=hidden -fvisibility-inlines-hidden" which eliminates the visibility of boost::archive::basic_xml_grammar<char>::parse_start_tag(std::istream&) because this is defined/instantiated in the cpp file that is compiled.  
  
I am attaching a file that will choke the linker. My command line is  
g++ -o test32 test32.cpp -L/usr/lib/x86_64-linux-gnu/ -lboost_serialization  
[test32.zip](https://github.com/boostorg/serialization/files/2439281/test32.zip)  
  
Let me know if you need further information, I will be happy to assist, but in my view the better fix is to move the definition of basic_xml_grammar out of serialization and into the headers for archive. The other option is to remove the visibility options for gcc and the other compiler that support them. For the time being my solution is to link statically as the static library has the necessary symbols.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-10-03 18:14:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-426741147  

i built your test. No problem compiling nor linking.  I'm using:  
  
$clang -v  
Apple LLVM version 8.1.0 (clang-802.0.41)  
Target: x86_64-apple-darwin17.2.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
on mac osx  
  
When I attempted to execute it it throws an exception.  This seems related to the absence of a file named somefile.xml on my system.  maybe you want to send me one.

---

## Comment 3

> Username: robertramey  
> Created at: 2018-10-03 18:16:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-426741764  

It looks like you're using stl port.  I wonder if that might be significant. Currently there is are not tests in the boost text matrix using stlport.  Perhaps you want to run the serialization test suite on your system with stlport?

---

## Comment 4

> Username: epsilonomega2k  
> Created at: 2018-10-04 20:01:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-427150359  

The error related to somefile.xml is expected. On my system the code compiles, but does not link, so the existence of somefile.xml is irrelevant. I am testing this on two systems, one debian and one ubuntu and in both cases the compiler/linker used to compile the boost binaries distributed with the operating system are missing the symbols I specified above. The systems have no customizations in terms of standard c/c++ libraries or compilers. The info for the ubuntu system is:  
  
~$ g++ --version  
g++ (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
~$ uname -a  
Linux 4.15.0-34-generic #37-Ubuntu SMP Mon Aug 27 15:21:48 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux  
  
I downloaded the source code of boost that corresponds to the same version as the one distributed by the operating system Ubuntu 18.04.1 LTS and tries compiling the boost libraries the standard way described on the boost website. This produces a  shared library libboost_serialization.so.1.65.1 that lacks the symbols I described above. Then I went and edited libs/serialization/build/Jamfile.v2 removing the two flags (-fvisibility=hidden and -fvisibility-inlines-hidden) from the option specification for gcc. Then I recompiled and ended up with a shared library that contains the symbols. This is how I concluded that one or both of these flags cause the problem with gcc. It also explains why you are not able to reproduce it: clang and darwin must do something different than gcc and ubuntu(or debian for that matter). I think that you will need a debian system (or one of its derivatives) to reproduce the issue.  
  
Note that this is an obscure problem and does not appear until one tries to create their own archive class. Then the problem occurs only in the linking stage, but not at the compilation one. I am not sure how to fix this -- maybe you have better ideas, but here is my best shot at it:  
  
Given that you are able to compile my code, my suggestion is for us to clean it up, and if needed to provide the somefile.xml then add the code to the set of tests that the boost library goes through before releases. That will cause compilation with a variety of gcc versions on variety of platforms including debian derivatives. If it passes, boost gains a test for derived archives. If it fails as I believe that it will then you and/or the release managers will have error, so hopefully the proper fix can be found.  
  
As for your second comment, I will be glad to look into it, but I am not sure what to look for. Here are the messages that the linker spits out and I see note relevant to stlport. Maybe you can direct me at what to look for?  
  
$ g++ -o test32 test32.cpp -L/usr/lib/x86_64-linux-gnu/ -lboost_serialization  
/tmp/cclGeJ2y.o: In function `boost::archive::basic_xml_iarchive<xml_iarchive_nan>::load_start(char const*)':  
test32.cpp:(.text._ZN5boost7archive18basic_xml_iarchiveI16xml_iarchive_nanE10load_startEPKc[_ZN5boost7archive18basic_xml_iarchiveI16xml_iarchive_nanE10load_startEPKc]+0x75): undefined reference to `boost::archive::basic_xml_grammar<char>::parse_start_tag(std::istream&)'  
/tmp/cclGeJ2y.o: In function `boost::archive::basic_xml_iarchive<xml_iarchive_nan>::load_end(char const*)':  
test32.cpp:(.text._ZN5boost7archive18basic_xml_iarchiveI16xml_iarchive_nanE8load_endEPKc[_ZN5boost7archive18basic_xml_iarchiveI16xml_iarchive_nanE8load_endEPKc]+0x75): undefined reference to `boost::archive::basic_xml_grammar<char>::parse_end_tag(std::istream&) const'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::load(char*)':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadEPc[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadEPc]+0x69): undefined reference to `boost::archive::basic_xml_grammar<char>::parse_string(std::istream&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::load(wchar_t*)':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadEPw[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadEPw]+0x69): undefined reference to `boost::archive::basic_xml_grammar<char>::parse_string(std::istream&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::load(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadERNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadERNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE]+0x5a): undefined reference to `boost::archive::basic_xml_grammar<char>::parse_string(std::istream&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::load(std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >&)':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadERNSt7__cxx1112basic_stringIwSt11char_traitsIwESaIwEEE[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4loadERNSt7__cxx1112basic_stringIwSt11char_traitsIwESaIwEEE]+0x69): undefined reference to `boost::archive::basic_xml_grammar<char>::parse_string(std::istream&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::init()':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4initEv[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanE4initEv]+0x40): undefined reference to `boost::archive::basic_xml_grammar<char>::init(std::istream&)'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::xml_iarchive_impl(std::istream&, unsigned int)':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanEC2ERSij[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanEC5ERSij]+0x76): undefined reference to `boost::archive::basic_xml_grammar<char>::basic_xml_grammar()'  
/tmp/cclGeJ2y.o: In function `boost::archive::xml_iarchive_impl<xml_iarchive_nan>::~xml_iarchive_impl()':  
test32.cpp:(.text._ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanED2Ev[_ZN5boost7archive17xml_iarchive_implI16xml_iarchive_nanED5Ev]+0x67): undefined reference to `boost::archive::basic_xml_grammar<char>::windup(std::istream&)'  
collect2: error: ld returned 1 exit status

---

## Comment 5

> Username: robertramey  
> Created at: 2018-10-04 20:14:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-427153955  

OK somefile.xml is necessary.  We only have to track down the failure to link.  So we might be able to make the example smaller - though its pretty small already.  
  
"Then I went and edited libs/serialization/build/Jamfile.v2 removing the two flags (-fvisibility=hidden and -fvisibility-inlines-hidden) from the option specification for gcc. Then I recompiled and ended up with a shared library that contains the symbols. This is how I concluded that one or both of these flags cause the problem with gcc."  
  
I think you've got it here.  aside from the visibility flags there are visibility macros.  It's likely these have to be added.  Of course it's not obvious where.  I'll look into it.

---

## Comment 6

> Username: epsilonomega2k  
> Created at: 2018-10-05 17:42:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-427444528  

Here is a copy of somefile.xml together with a short program that creates it. test31 creates the somefile.xml, test32 reads it (if test32 compiles). I specifically wrote "nan" for the double in the archive, so to illustrate the point that if one tried to use the standard xml_iarchive the reading fails because the standard library does not read nan as double. Of course, this can be extended to cover inf and -inf.  
[test31_32.zip](https://github.com/boostorg/serialization/files/2451494/test31_32.zip)  
  
  
 Let me know if you need anything else from me.

---

## Comment 7

> Username: robertramey  
> Created at: 2018-10-10 04:00:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-428430244  

try making the following change and let me know what happens  
  
file boost/archive/impl/basic_xml_grammar.hpp line 67  
  
class BOOST_SYMBOL_VISIBLE basic_xml_grammar {  
...

---

## Comment 8

> Username: epsilonomega2k  
> Created at: 2018-10-10 14:45:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-428600786  

You got it!  
  
I can confirm that adding BOOST_SYMBOL_VISIBLE to line 67 as you suggested resolves the issue in an otherwise "virgin copy" of the boost sources (e.g. sources where no other modifications are made including modifications to the gcc flags of the type I suggested earlier).  
  
After the modification the symbols which were missing are actually found in the dynamic library (confirmed with nm) and I was able to link my example test32 against it.

---

## Comment 9

> Username: robertramey  
> Created at: 2018-10-10 15:49:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/123#issuecomment-428625803  

LOL - hallelujah !  I'll check in this change soon.
