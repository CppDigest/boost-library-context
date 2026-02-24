# #34 - Abstract Base By Reference Canidate Causing Errors [Open]

> Username: ajorians  
> Created at: 2019-02-11 13:51:37 UTC  
> Updated at: 2019-02-11 22:29:58 UTC  
> Url: https://github.com/boostorg/rational/issues/34  

Please consider this sample code:  
```cpp  
#include <boost/rational.hpp>  
#include <iostream>  
  
class AbstractBase  
{  
public:  
   virtual ~AbstractBase() {}  
   virtual int MethodToMakeBaseAbstract() const = 0;  
};  
class Derived : public AbstractBase  
{  
public:  
   int MethodToMakeBaseAbstract() const override { return 0; }  
};  
  
int FramesToSeconds( const AbstractBase& /*project*/ )  
{  
   return 3;  
}  
int FramesToSeconds( boost::rational<int> /*editRate*/ )  
{  
   return 4;  
}  
  
int main()  
{  
   AbstractBase* p = new Derived();  
     
   std::cout << FramesToSeconds( *p ) << std::endl;  
     
   delete p;  
}  
```  
  
I believe the output should be `3` and with no problems compiling.  It compiles fine on Clang.  I am having issues with GCC (9.0.1.201902) and Microsoft Visual Studio 15.7.5 with Boost version 1.64.0 and higher (tested through Boost version 1.69.0).  Boost version 1.63.0 seems builds fine with that example on GCC.  
  
Here is the compiler flags with using [Wandbox](https://wandbox.org/) to test this  
```  
g++ prog.cc -Wall -Wextra -I/opt/wandbox/boost-1.64.0/gcc-head/include -std=c++17   
```  
  
Here are the errors I am seeing:  
<details>  
```  
In file included from /opt/wandbox/gcc-head/include/c++/9.0.1/string_view:40,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/bits/basic_string.h:48,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/string:55,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/bits/locale_classes.h:40,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/bits/ios_base.h:41,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/iomanip:40,  
                 from /opt/wandbox/boost-1.64.0/gcc-head/include/boost/rational.hpp:68,  
                 from prog.cc:1:  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits: In instantiation of 'struct std::numeric_limits<AbstractBase>':  
/opt/wandbox/boost-1.64.0/gcc-head/include/boost/rational.hpp:119:7:   required from 'const bool boost::rational_detail::is_compatible_integer<AbstractBase, int>::value'  
/opt/wandbox/boost-1.64.0/gcc-head/include/boost/rational.hpp:156:21:   required by substitution of 'template<class T> constexpr boost::rational<int>::rational(const T&, const typename boost::enable_if_c<boost::rational_detail::is_compatible_integer<T, int>::value, void>::type*) [with T = AbstractBase]'  
prog.cc:29:37:   required from here  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:317:7: error: invalid abstract return type 'AbstractBase'  
  317 |       min() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~  
prog.cc:4:7: note:   because the following virtual functions are pure within 'AbstractBase':  
    4 | class AbstractBase  
      |       ^~~~~~~~~~~~  
prog.cc:8:16: note: 	'virtual int AbstractBase::MethodToMakeBaseAbstract() const'  
    8 |    virtual int MethodToMakeBaseAbstract() const = 0;  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/wandbox/gcc-head/include/c++/9.0.1/string_view:40,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/bits/basic_string.h:48,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/string:55,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/bits/locale_classes.h:40,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/bits/ios_base.h:41,  
                 from /opt/wandbox/gcc-head/include/c++/9.0.1/iomanip:40,  
                 from /opt/wandbox/boost-1.64.0/gcc-head/include/boost/rational.hpp:68,  
                 from prog.cc:1:  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:321:7: error: invalid abstract return type 'AbstractBase'  
  321 |       max() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:327:7: error: invalid abstract return type 'AbstractBase'  
  327 |       lowest() noexcept { return _Tp(); }  
      |       ^~~~~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:333:7: error: invalid abstract return type 'AbstractBase'  
  333 |       epsilon() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~~~~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:337:7: error: invalid abstract return type 'AbstractBase'  
  337 |       round_error() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~~~~~~~~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:341:7: error: invalid abstract return type 'AbstractBase'  
  341 |       infinity() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~~~~~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:346:7: error: invalid abstract return type 'AbstractBase'  
  346 |       quiet_NaN() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~~~~~~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:351:7: error: invalid abstract return type 'AbstractBase'  
  351 |       signaling_NaN() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~~~~~~~~~~~  
/opt/wandbox/gcc-head/include/c++/9.0.1/limits:357:7: error: invalid abstract return type 'AbstractBase'  
  357 |       denorm_min() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
      |       ^~~~~~~~~~  
```  
</details>  
  
There are several workarounds I can do to get my code to compile such as make the `AbstractBase` class not be abstract (no pure virtual functions), or rename the function so it is no longer considered, and also make my function take the base class by pointer instead of reference.  Because it builds on Clang, I am not sure if it is ultimately a GCC/MSVC bug.  
  
Let me know if there is any additional information I can give you to help.  Thank you.

---

## Comment 1

> Username: mclow  
> Created at: 2019-02-11 14:50:30 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462354729  

Does the problem persist if you replace all the `boost::rational<int>` with (say) `std::complex<double>`?

---

## Comment 2

> Username: ajorians  
> Created at: 2019-02-11 14:54:09 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462356068  

The problem does **not** persist with `std::complex<double>`.  Here is that sample I tested for you:  
```cpp  
#include <complex>  
#include <iostream>  
  
class AbstractBase  
{  
public:  
   virtual ~AbstractBase() {}  
   virtual int MethodToMakeBaseAbstract() const = 0;  
};  
class Derived : public AbstractBase  
{  
public:  
   int MethodToMakeBaseAbstract() const override { return 0; }  
};  
  
int FramesToSeconds( const AbstractBase& /*project*/ )  
{  
   return 3;  
}  
int FramesToSeconds( std::complex<double> /*editRate*/ )  
{  
   return 4;  
}  
  
int main()  
{  
   AbstractBase* p = new Derived();  
     
   std::cout << FramesToSeconds( *p ) << std::endl;  
     
   delete p;  
}  
```

---

## Comment 3

> Username: mclow  
> Created at: 2019-02-11 15:18:15 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462365285  

Ok, this is what I think is happening here.  
Gcc looks at the two overloads for `FramesToSeconds`, and has a `Derived &` in hand.  
So it attempts to see if it can convert this to a `boost::rational<int>` and fails, because there's a SFINAE check. But it produces a hard error, rather than a substitution failure.

---

## Comment 4

> Username: ajorians  
> Created at: 2019-02-11 15:57:06 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462380805  

> and has a `Derived &` in hand  
  
If it helps the problem still persists if I adjust the `main` function to look like this:  
```cpp  
int main()  
{  
   AbstractBase* p = new Derived();  
      
   const AbstractBase& baseRef = *p;  
     
   std::cout << FramesToSeconds( baseRef ) << std::endl;  
     
   delete p;  
}  
```  
  
Maybe it is still fair to say `Derived &`.  🤷‍♂️   
  
But yes I think you are right about how it attempt to see if it can convert to a `boost::rational<int>` and fails, rather than a substitution failure.

---

## Comment 5

> Username: jwakely  
> Created at: 2019-02-11 20:31:03 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462482757  

The changes in ff2530e11924323daa995fbd5ec1cf4bae26b53c cause `numeric_limits<AbstractBase>` to be instantiated, and that is ill-formed (no diagnostic required) because it declares member functions returning an abstract type. GCC rejects this, even in uninstantiated templates, as described at the end of https://gcc.gnu.org/gcc-4.9/porting_to.html  
  
The invalid instantiation happens outside the immediate context, and so is not just a substitution failure.  
  
A minimal reproducer that fails with GCC is:  
  
````  
template<typename T>  
struct numeric_limits  
{  
    static const bool is_specialized = false;  
    static T zero() { return T(); }  
};  
  
template<bool, typename T = void>  
struct enable_if  
{  
    typedef T type;  
};  
  
template<typename T>  
struct enable_if<false, T>  
{ };  
  
struct rational {  
    template<typename T>  
    struct is_compatible_integer  
    {  
        static const bool value = numeric_limits<T>::is_specialized;  
    };  
      
    template<typename T>  
    rational(T, typename enable_if<is_compatible_integer<T>::value>::type* = 0) { }  
};  
  
class AbstractBase  
{  
public:  
   virtual int MethodToMakeBaseAbstract() const = 0;  
};  
class Derived : public AbstractBase  
{  
public:  
   int MethodToMakeBaseAbstract() const override { return 0; }  
};  
  
int f( const AbstractBase& )  
{  
   return 3;  
}  
int f( rational )  
{  
   return 4;  
}  
  
void g(AbstractBase* p)  
{  
    f(*p);  
}  
````  
  
The solution is use `is_integral<T>` instead of relying on `numeric_limits<T>::is_integer`, or use some other SFINAE-friendly check that the type is a value type that won't cause `numeric_limits` to explode.

---

## Comment 6

> Username: zygoloid  
> Created at: 2019-02-11 21:44:10 UTC  
> Updated at: 2019-02-11 21:45:20 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462506693  

@mclow asked me to comment.  
  
See [P0929](wg21.link/p0929), voted into the working paper as a defect report at Rapperswil 2018. The old rules for abstract classes don't work very well, because they create errors "too early", and in contexts where we never required the class type in question to even be a complete type.  
  
Under the new post-P0929 rules, I think boost's technique is valid. Declaring a function whose return type is an abstract class type is now valid, and it's only defining or calling such a function that renders the program ill-formed (and instantiating `numeric_limits<Abstract>` to get at the `is_integer` member shouldn't do that). So you could argue that this is a GCC bug (but it wouldn't be especially fair to do so, since this is a recent DR). In the mean time, it seems like the onus is on boost to work around this.

---

## Comment 7

> Username: jwakely  
> Created at: 2019-02-11 22:29:58 UTC  
> Url: https://github.com/boostorg/rational/issues/34#issuecomment-462521003  

Thanks, @zygoloid -- this is https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86252
