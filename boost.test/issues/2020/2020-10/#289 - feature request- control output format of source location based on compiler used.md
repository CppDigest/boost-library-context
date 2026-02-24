# #289 - feature request: control output format of source location based on compiler used [Open]

> Username: correaa  
> Created at: 2020-10-31 06:28:43 UTC  
> Updated at: 2022-03-04 21:45:13 UTC  
> Url: https://github.com/boostorg/test/issues/289  

Currently, in HRF format, the source location is printed as `file.ext(lineno):`.  
For example  
  
```  
./.././detail/test.cpp(764): error in "test_name": check a == b has failed [3 != 6]  
```  
  
It would be good for automated tools to have the alternative to print the source location in format more similar to source locations printed in clang and gcc (also Catch2), i.e. `file.ext:lineno`.  
For example the modified format out would print:  
  
```  
./.././detail/test.cpp:764: error in "test_name": check a == b has failed [3 != 6]  
```  
  
**Would it be possible to add this options to the format output?**  
  
(I tried editing the strings in `/usr/include/boost/test/minimal.hpp` and in `test/impl/compiler_log_formatter.ipp:print_prefix` but that didn't do it.)

---

## Comment 1

> Username: correaa  
> Created at: 2020-10-31 07:59:13 UTC  
> Updated at: 2020-10-31 08:00:44 UTC  
> Url: https://github.com/boostorg/test/issues/289#issuecomment-719899974  

I found this in the sources. It seems that the format is controlled by the compiler in the case of Xcode, based on an idea of https://richarddingwall.name/2008/06/01/using-the-boost-unit-test-framework-with-xcode-3  
  
```cpp  
#ifdef __APPLE_CC__  
        // Xcode-compatible logging format, idea by Richard Dingwall at  
        // <http://richarddingwall.name/2008/06/01/using-the-boost-unit-test-framework-with-xcode-3/>.  
        output << file_name << ':' << line_num << ": ";  
#else  
        output << file_name << '(' << line_num << "): ";  
#endif  
```  
  
Very good, with the same criteria the (default) format can be deduced from the compiler used to compile Boost.Test in linux as well (e.g. GCC, clang --same format incidentally--, intel, etc)  
  
That is I propose:  
```cpp  
#if defined(__APPLE_CC__) or defined(__GNUC__) or defined(__clang__)  
        // Xcode-compatible logging format, idea by Richard Dingwall at  
        // <http://richarddingwall.name/2008/06/01/using-the-boost-unit-test-framework-with-xcode-3/>.  
       // also for gcc and clang  
        output << file_name << ':' << line_num << ": ";  
#else  
        output << file_name << '(' << line_num << "): ";  
#endif  
```  
(note: that a macro defined in test is not going to work because this is an `ipp` file and therefore is precompiled at the point of use.)  
  
I found a solution using the facilities of Boost.Test, but it is really and overkill just to change this detail (parenthesis for colon).  
  
https://stackoverflow.com/a/64619245/225186
