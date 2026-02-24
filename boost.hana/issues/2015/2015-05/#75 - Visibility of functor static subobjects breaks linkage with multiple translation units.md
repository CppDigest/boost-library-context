# #75 - Visibility of functor static subobjects breaks linkage with multiple translation units [Closed]

> Username: palebedev  
> Created at: 2015-05-19 18:23:58 UTC  
> Updated at: 2015-05-22 23:03:06 UTC  
> Closed at: 2015-05-22 23:03:06 UTC  
> Url: https://github.com/boostorg/hana/issues/75  

```  
# echo '#include <boost/hana/comparable.hpp>' > a.cpp  
# cp a.cpp b.cpp  
# clang++ -stdlib=libc++ -std=c++14 -I/path/to/hana/include a.cpp b.cpp  
/usr/bin/x86_64-pc-linux-gnu-ld: error: /tmp/b-f9340e.o: multiple definition of 'boost::hana::_less_equal::than'  
/usr/bin/x86_64-pc-linux-gnu-ld: /tmp/a-86a8b2.o: previous definition here  
/usr/bin/x86_64-pc-linux-gnu-ld: error: /tmp/b-f9340e.o: multiple definition of 'boost::hana::_greater_equal::than'  
/usr/bin/x86_64-pc-linux-gnu-ld: /tmp/a-86a8b2.o: previous definition here  
/usr/bin/x86_64-pc-linux-gnu-ld: error: /tmp/b-f9340e.o: multiple definition of 'boost::hana::_less::than'  
/usr/bin/x86_64-pc-linux-gnu-ld: /tmp/a-86a8b2.o: previous definition here  
/usr/bin/x86_64-pc-linux-gnu-ld: error: /tmp/b-f9340e.o: multiple definition of 'boost::hana::_equal::to'  
/usr/bin/x86_64-pc-linux-gnu-ld: /tmp/a-86a8b2.o: previous definition here  
/usr/bin/x86_64-pc-linux-gnu-ld: error: /tmp/b-f9340e.o: multiple definition of 'boost::hana::_greater::than'  
/usr/bin/x86_64-pc-linux-gnu-ld: /tmp/a-86a8b2.o: previous definition here  
```  
  
I've reduced this to  
  
``` c++  
struct A  
{  
    struct B {};  
    static constexpr B b{};  
};  
constexpr A::B A::b;  
constexpr A a{};  
```  
  
N4296/3.5p5 says that static data members have same linkage as the name of the class they are members of, which is external here. While in-class declaration appears to be just a declaration (due to 9.4.2p3, despite having a required for constexpr objects initializer), out-of-class one is a definition. Since it's not a template and has external visibility it clashes with same definitions from other translation units that include hana headers. I hope I'm reading this properly, at least experimenting with recent clang from git and gcc 5.1 seems to support this interpretation.  
So, keeping this doesn't allow any program with multiple translation units utilizing hana to link. Proper "fix" would be to put these definitions in separate translation units, but this can't be done while the library is (rightfully) header-only. Removing out-of-class definition at least fixes my usage of hana, but breaks, for example, taking address of such objects as they are no longer defined. Actually, since removing these declarations worked for me, I'm not really sure what real usage could they be useful for.  
Now, 9.4.2p3 says that the definition is required, if the name is _odr-used_. 3.2p3 says that variables are odr-used unless applying lvalue-to-rvalue conversion to them yields a constant expression. I'm getting somewhat confused here, but the rest of the paragraph appears irrelevant. 5.20p2 explicitly allows lvalue-to-rvalue conversions for non-volatile glvalues that are constexpr-declared objects in constant expressions. Does this mean we're not odr-using these variables while accessing them, so we don't actually need their definitions? I haven't been able to come up with any other ways to odr-use them, besides taking their address, which doesn't seem useful to me. It looks like most, if not all, of your tests have a single translation unit, so I feel this could actually be a problem and not just a misunderstanding on my part.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-05-19 19:00:34 UTC  
> Url: https://github.com/boostorg/hana/issues/75#issuecomment-103633948  

Thanks for the very detailed bug report. I'm working on it.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-05-20 18:45:35 UTC  
> Url: https://github.com/boostorg/hana/issues/75#issuecomment-103991570  

From [this SO answer](http://stackoverflow.com/a/21344184/627587), I think the nested `static constexpr`  members are odr-used. Hence, they need a separate definition at namespace scope. I think the question is then how to define those in the headers without having ODR problems.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-05-20 18:54:53 UTC  
> Url: https://github.com/boostorg/hana/issues/75#issuecomment-103994763  

Basically, I need to give internal linkage to those `static` members. To do this, I can either enclose the `struct` they are defined in into an anonymous namespace or make them `const` (`const` variables have internal linkage by default). I'm currently looking at both options.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-05-20 19:00:07 UTC  
> Url: https://github.com/boostorg/hana/issues/75#issuecomment-103996402  

For the record, merely making the objects `const` does not seem to fix the issue.
