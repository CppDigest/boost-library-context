# #45 - Some test files failed to build due to "error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol" [Closed]

> Username: BinCaoWR  
> Created at: 2019-05-28 07:38:05 UTC  
> Updated at: 2021-03-05 18:51:21 UTC  
> Closed at: 2021-03-05 18:51:21 UTC  
> Url: https://github.com/boostorg/convert/issues/45  

The following files failed to build:  
../libs/convert/test/performance.cpp:31:38: error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
../libs/convert/test/spirit_converter.cpp:22:38: error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
../libs/convert/test/stream_converter.cpp:26:38: error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
../libs/convert/test/printf_converter.cpp:19:38: error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
../libs/convert/test/strtol_converter.cpp:166:38: error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
  
According to http://www.cplusplus.com/reference/complex/arg/, arg should be declared in complex as below:  
complex (1) | template<class T> T arg (const complex<T>& x);  
arithmetic type (2) |  double arg (ArithmeticType x);  // additional overloads  
  
As a result, the arg declared in boost test files should be renamed.

---

## Comment 1

> Username: yet-another-user  
> Created at: 2019-05-28 09:59:04 UTC  
> Url: https://github.com/boostorg/convert/issues/45#issuecomment-496449956  

Thank you for your input. Much appreciated. Would you mind to clarify under what circumstances it fails to build... because 1) it builds on my machine (ubuntu); 2) it builds and runs in Boorst regression (https://www.boost.org/development/tests/develop/developer/convert.html). From what I understand the "arg" you reference is, in fact, in the std namspace, i.e. std::arg declared in <complex>... which I do not see even included in the mentioned cpp files. SO, again, would you mind shedding some light on how the problem pops up? Thanks.

---

## Comment 2

> Username: BinCaoWR  
> Created at: 2019-05-29 02:28:48 UTC  
> Url: https://github.com/boostorg/convert/issues/45#issuecomment-496759220  

Thanks for your quick response. I build the tests on gcc-8.1.0 & C++14. If it cannot be reproduced in your environment, it would be probably due to arg is not declared in your complex.h on the C++/toolchain version you are using.  
Here is the info about how complex can be included in the boost cpp files.   
  
../libs/convert/test/performance.cpp: At global scope:  
../libs/convert/test/performance.cpp:31:38: error: 'namespace arg = boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
 namespace arg = boost::cnv::parameter;  
                                      ^  
In file included from ccomplex:16,  
                 from complex:5,  
                 from boost_1_70_0/boost/type_traits/is_complex.hpp:12,  
                 from boost_1_70_0/boost/type_traits.hpp:96,  
                 from boost_1_70_0/boost/convert/detail/is_fun.hpp:11,  
                 from boost_1_70_0/boost/convert.hpp:28,  
                 from ../libs/convert/test/performance.cpp:13:  
complex.h:664:20: note: previous declaration 'long double arg(_Lcomplex)'  
 inline long double arg(_Lcomplex _Left)  
                    ^~~

---

## Comment 3

> Username: yet-another-user  
> Created at: 2019-05-29 02:39:32 UTC  
> Url: https://github.com/boostorg/convert/issues/45#issuecomment-496761118  

Indeed, I use g++-7.4.0 and it does not have "arg" defined in ccomplex.  
I'll look into fixing it. Still, I am really curious why that does not fail  
in regression tests.  
  
https://www.boost.org/development/tests/develop/developer/convert.html  
shows that all compiled on gcc- 8~c++11, gcc- 8~c++14, etc.  
  
Any idea why that might be? Tnx.  
  
  
On Wed, May 29, 2019 at 12:28 PM BinCaoWR <notifications@github.com> wrote:  
  
> Thanks for your quick response. I build the tests on gcc-8.1.0 & C++14. If  
> it cannot be reproduced in your environment, it would be probably due to  
> arg is not declared in your complex.h on the C++/toolchain version you are  
> using.  
> Here is the info about how complex can be included in the boost cpp files.  
>  
> ../libs/convert/test/performance.cpp: At global scope:  
> ../libs/convert/test/performance.cpp:31:38: error: 'namespace arg =  
> boost::cnv::boost::cnv::parameter;' redeclared as different kind of symbol  
> namespace arg = boost::cnv::parameter;  
> ^  
> In file included from ccomplex:16,  
> from complex:5,  
> from boost_1_70_0/boost/type_traits/is_complex.hpp:12,  
> from boost_1_70_0/boost/type_traits.hpp:96,  
> from boost_1_70_0/boost/convert/detail/is_fun.hpp:11,  
> from boost_1_70_0/boost/convert.hpp:28,  
> from ../libs/convert/test/performance.cpp:13:  
> complex.h:664:20: note: previous declaration 'long double arg(_Lcomplex)'  
> inline long double arg(_Lcomplex _Left)  
> ^~~  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/45?email_source=notifications&email_token=AANTAIDV2VOP2EPGEEPIHR3PXXS6DA5CNFSM4HQARZAKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODWN7DNA#issuecomment-496759220>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANTAIBAMRMEYBHO2Q6O5GTPXXS6DANCNFSM4HQARZAA>  
> .  
>

---

## Comment 4

> Username: BinCaoWR  
> Created at: 2019-05-29 03:23:42 UTC  
> Url: https://github.com/boostorg/convert/issues/45#issuecomment-496768767  

I'm not sure why the boost regression can pass. Perhaps need to confirm if is_complex.hpp is included and how arg(the conditionals, etc) is defined in complex.

---

## Comment 5

> Username: yet-another-user  
> Created at: 2019-06-21 09:54:59 UTC  
> Url: https://github.com/boostorg/convert/issues/45#issuecomment-504367634  

I have no forgotten about the issue. Just recently upgraded to gcc-8.3. Will be trying to see why tat might be failing... because IMO it should not. :-)

---

## Comment 6

> Username: yet-another-user  
> Created at: 2021-03-05 18:51:21 UTC  
> Url: https://github.com/boostorg/convert/issues/45#issuecomment-791613047  

Can't reproduce. Closing.
