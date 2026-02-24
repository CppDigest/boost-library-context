# #362 - BOOST_SYMBOL_IMPORT should not be defined to nothing [Open]

> Username: Flamefire  
> Created at: 2021-02-02 08:50:27 UTC  
> Updated at: 2023-05-15 08:14:36 UTC  
> Url: https://github.com/boostorg/config/issues/362  

For non-Win32 platforms BOOST_SYMBOL_IMPORT is defined to nothing which is wrong:   
  
https://gcc.gnu.org/wiki/Visibility  
> Symbol visibility is "default" by default but if the linker encounters just one definition with it hidden - just one - that typeinfo symbol becomes permanently hidden (remember the C++ standard's ODR - one definition rule).  
  
Hence (all being build with hidden visibility) a program using a boost shared library will not see e.g. the same typeinfo as the Boost lib which results in e.g. failure of being able to catch or dynamic_cast the class.  
  
I observed this behavior on OSX, on Linux it seems harder to reproduce. See also https://stackoverflow.com/questions/65951466/why-can-a-thrown-exception-with-hidden-visibility-still-be-caught for more details  
  
Solution: Define BOOST_SYMBOL_IMPORT to BOOST_SYMBOL_VISIBLE when BOOST_SYMBOL_EXPORT is too.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-02-02 18:01:17 UTC  
> Url: https://github.com/boostorg/config/issues/362#issuecomment-771846586  

Summary of what we know so far:  
  
- when using hidden visibility, for exceptions thrown from .so to be caught, they need to be marked BOOST_SYMBOL_VISIBLE in both the library and the caller.  
- BOOST_SYMBOL_EXPORT is defined to BOOST_SYMBOL_VISIBLE, but BOOST_SYMBOL_IMPORT is not.  
- this causes exception classes marked as BOOST_LIBRARY_DECL to not work with libc++ (on both Linux and macOS).  
- libstdc++ uses string comparison for exceptions, and hence masks the issue; exceptions work there regardless.  
- the two ways to fix this issue are (1) mark all exception classes BOOST_SYMBOL_VISIBLE in addition to BOOST_LIBRARY_DECL, or (2) make BOOST_SYMBOL_IMPORT also expand to BOOST_SYMBOL_VISIBLE.  
- (2) has the upside of making everything work, and the downside that class members inherit the visibility of their class; therefore, inline member functions of a class marked VISIBLE become VISIBLE too, unless -fvisibility-inlines-hidden is used. This isn't always desirable.  
- CMake chooses to make _DECL expand to the equivalent of _VISIBLE for both export and import; they have chosen solution (2).

---

## Comment 2

> Username: pdimov  
> Created at: 2021-02-02 18:11:49 UTC  
> Url: https://github.com/boostorg/config/issues/362#issuecomment-771855999  

In addition, it appears that the problem only affects exception classes that are marked _DECL but are entirely header-only. So at the moment I'm inclined to think that this is a problem with the specific library (ProgramOptions) rather than with the macros.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-02-03 16:02:19 UTC  
> Url: https://github.com/boostorg/config/issues/362#issuecomment-772619455  

https://github.com/boostorg/program_options/pull/106

---

## Comment 4

> Username: Flamefire  
> Created at: 2023-05-15 08:14:36 UTC  
> Url: https://github.com/boostorg/config/issues/362#issuecomment-1547396525  

I'd like to add a third option: Add a `BOOST_CLASS_EXPORT` define which on Windows is the same as `BOOST_SYMBOL_EXPORT` but on platforms supporting visibility it should be `BOOST_SYMBOL_VISIBLE`  
And maybe a `BOOST_CLASS_IMPORT` defined as `BOOST_SYMBOL_IMPORT` for clarity.  
  
Because check this class:  
```  
struct Foo{  
  virtual ~Foo();  
  virtual int getAnswer() = 0;  
  static int id;  
};  
```  
  
How would you decorate this class? You need `BOOST_SYMBOL_VISIBLE` to make the virtual parts work on libc++ but `BOOST_SYMBOL_EXPORT/IMPORT` to handle the static data member defined in a cpp-file.  
  
I realize (now) that `BOOST_CLASS_EXPORT` could be `#define BOOST_CLASS_EXPORT BOOST_SYMBOL_VISIBLE BOOST_SYMBOL_EXPORT` as it seems from https://github.com/boostorg/program_options/pull/106 the duplicated attribute doesn't cause issues.  
  
However for classes (especially exception classes) fully defined in the header I don't see the need for `BOOST_SYMBOL_EXPORT` as `BOOST_SYMBOL_VISIBLE` should be enough already and that is what is [documented](https://www.boost.org/doc/libs/master/libs/config/doc/html/boost_config/boost_macro_reference.html) too.
