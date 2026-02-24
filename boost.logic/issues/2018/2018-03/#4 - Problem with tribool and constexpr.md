# #4 - Problem with tribool and constexpr [Closed]

> Username: robertramey  
> Created at: 2018-03-31 04:41:22 UTC  
> Updated at: 2018-08-20 20:45:59 UTC  
> Closed at: 2018-08-20 20:45:59 UTC  
> Url: https://github.com/boostorg/logic/issues/4  

Consider the following simple program  
````  
#include <boost/logic/tribool.hpp>  
  
struct A {  
    int m_i;  
    constexpr A() : m_i(-1) {}  
    constexpr A(int a) : m_i(a) {}  
};  
  
using namespace boost::logic;  
  
constexpr tribool operator<(const A & left, const A & right){  
    if(left.m_i == -1)  
        return indeterminate;  
    else  
    if(right.m_i == -1)  
        return indeterminate;  
    else  
        return left.m_i < right.m_i;  
}  
  
int main()  
{  
    static_assert(A(1) < A(2), "A(1) < A(2)");  
    return 0;  
}  
````  
Compilation of this code with gcc 6.2 produces the following error.  This indicates that conversion to bool by tribool is not a constexpr expression.  
````  
$g++ -std=c++14 -I../../.. t.cpp  
t.cpp: In function ‘int main()’:  
t.cpp:23:2: error: non-constant condition for static assertion  
  static_assert(A(1) < A(2), "A(1) < A(2)");  
  ^~~~~~~~~~~~~  
t.cpp:23:43: error: ‘(boost::logic::tribool::dummy::nonnull != 0u)’ is not a constant expression  
  static_assert(A(1) < A(2), "A(1) < A(2)");  
                                           ^  
````  
Meanwhile, clang 8.1.0 doesn't seem to have any problem with this  
````  
$clang -std=c++14 -I../../.. t.cpp  
````

---

## Comment 1

> Username: pdimov  
> Created at: 2018-03-31 18:32:54 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-377713691  

Should be fixed by https://github.com/boostorg/logic/pull/5 although I haven't checked. :-)

---

## Comment 2

> Username: robertramey  
> Created at: 2018-03-31 21:07:35 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-377723514  

Would it be possible to remove the part of the the test program which comments out the test of related to this PR?

---

## Comment 3

> Username: robertramey  
> Created at: 2018-08-16 18:22:33 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-413639653  

Is there any resolution on this?  I see a long chain of comments at #5 but it doesn't seem the actual code has been altered in any way - at least in the past couple of years?  What's the final decision on this?

---

## Comment 4

> Username: jeking3  
> Created at: 2018-08-19 14:44:49 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-414132503  

I haven't had time to look at it.  When I last left it I was thinking the fix in #5 could be merged but I wanted more time to consider all of the comments, since there was no definitive direction from everyone commenting.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-08-19 15:55:14 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-414136914  

Seems to me that peters fix  making it explicit when C++ version supports - using the traditional safebool implementation otherwise, is the least unacceptable option.  It's an improvement and gets us over the hump of constexpr not being supported by the casting operator in gcc.  So I'm strongly recommending the change now - so it can show up in the test matrix ASAP.  I'm not even closing the door to reversion the change in the future, or perhaps re-thinking it.  But no one has the time/interest/inclination to actually  take responsibility for dealing with this.  
  
For my particular case, I see that I can address it by defining a conversion function "to_bool(const tribool & t) which I could then sprinkle in my project.  This would work and not be as bad as I had originally thought.  But I don't want to do this is "static_cast<bool>(tribool) is going to be fixed.  I  just need to know one way or the other.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-08-20 12:30:41 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-414300216  

When I compile the example with gcc 5.4 from Ubuntu Xenial and using the fix from #5 (that I just merged) I get:  
```  
boost@8780e9643445:/boost/libs/logic/test$ ../../../b2 gcc62ramey -a -q cxxstd=11  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
...found 192 targets...  
...updating 7 targets...  
link.mklink ../../../boost/config.hpp  
link.mklink ../../../boost/detail/workaround.hpp  
gcc.compile.c++ ../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-11-iso/gcc62ramey.o  
gcc62ramey.cpp: In function 'constexpr boost::logic::tribool operator<(const A&, const A&)':  
gcc62ramey.cpp:19:1: error: body of constexpr function 'constexpr boost::logic::tribool operator<(const A&, const A&)' not a return-statement  
 }  
 ^  
gcc62ramey.cpp: In function 'int main()':  
gcc62ramey.cpp:23:5: error: non-constant condition for static assertion  
     static_assert(A(1) < A(2), "A(1) < A(2)");  
     ^  
gcc62ramey.cpp:23:29: error: 'constexpr boost::logic::tribool operator<(const A&, const A&)' called in a constant expression  
     static_assert(A(1) < A(2), "A(1) < A(2)");  
                             ^  
  
    "g++"   -std=c++11 -fPIC -m64 -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1  -I"../../.." -c -o "../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-11-iso/gcc62ramey.o" "gcc62ramey.cpp"  
  
...failed gcc.compile.c++ ../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-11-iso/gcc62ramey.o...  
...failed updating 1 target...  
...updated 3 targets...  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2018-08-20 12:56:14 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-414306830  

It uses control flow in a constexpr function, which is C++14. For C++11, needs to be rewritten as a single return, in terms of the ternary operator.

---

## Comment 8

> Username: jeking3  
> Created at: 2018-08-20 20:45:40 UTC  
> Url: https://github.com/boostorg/logic/issues/4#issuecomment-414457197  

Okay, my mistake, I should have used `cxxstd=14` when testing it.  When using that, it compiles with the fix checked into develop.  (#5)  
  
> boost@30f6c89a4fc7:/boost/libs/logic/test$ ../../../b2 cxxstd=14 gcc62ramey  
> Performing configuration checks  
>   
>     - default address-model    : 64-bit (cached)  
>     - default architecture     : x86 (cached)  
>     - symlinks supported       : yes (cached)  
> ...found 196 targets...  
> ...updating 5 targets...  
> gcc.compile.c++ ../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-14-iso/gcc62ramey.o  
> gcc.link ../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-14-iso/gcc62ramey  
> testing.capture-output ../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-14-iso/gcc62ramey.run  
> **passed** ../../../bin.v2/libs/logic/test/gcc62ramey.test/gcc-5.4.0/debug/cxxstd-14-iso/gcc62ramey.test  
> ...updated 5 targets...  
>
