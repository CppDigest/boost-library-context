# #13 Add missing include guards to speed up compilation [Closed]

> Username: jeanphilippeD  
> Created at: 2017-07-03 20:46:15 UTC  
> Updated at: 2017-07-06 21:44:21 UTC  
> Closed at: 2017-07-06 21:44:21 UTC  
> Url: https://github.com/boostorg/function/pull/13  

Even if function.hpp is in the precompiled header, without these  
include guards, we pay the cost of pre-processing using boost pp.  
This can be prohibitive with interactive parsing tools.  
  
Test:  
Apply update locally without rebuilding boost:  
 - rebuild large project that uses boost function using Visual Studio 2017.  
 - verify using the '/showincludes' option that we only pre-process boost function in the pre-compiled header.  
  
Fixes: https://svn.boost.org/trac10/ticket/13105

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-07-03 20:57:40 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-312732489  

AMDG  
  
On 07/03/2017 02:46 PM, jeanphilippeD wrote:  
> Even if function.hpp is in the precompiled header, without these  
> include guards, we pay the cost of pre-processing using boost pp.  
> This can be prohibitive with interactive parsing tools.  
>   
  
The #include guard is left out intentionally,  
so that redefining BOOST_FUNCTION_MAX_ARGS  
and #including <boost/function.hpp> again works.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeanphilippeD  
> Created_at: 2017-07-03 21:53:24 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-312739823  

Thanks you very much for your answer.  
I should have realised that there was a reason for it.  
  
I think i have an idea of how we can still have this and also the efficiency of the include guard.  
That would involved defining a new `BOOST_FUNCTION_MAX_ARGS_DEFINED` for each function we define and in the function header check `#if BOOST_FUNCTION_MAX_ARGS_DEFINED < BOOST_FUNCTION_MAX_ARGS`  
  
I'll try it and update the pull request if it works.  
  
Kind regards,  
Jean-Philippe Dufraigne.

---

## Comment 3

> Username: jeanphilippeD  
> Created_at: 2017-07-03 23:06:48 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-312747489  

Hi,  
  
So i did manage to update the pull request.  
Please let me know if this is more acceptable, and whether there is any change needed.  
  
Kind Regards,  
Jean-Philippe  
  
Seem to be working with:  
  
```cpp  
// Boost.Function library examples  
  
//  Copyright Douglas Gregor 2001-2003. Use, modification and  
//  distribution is subject to the Boost Software License, Version  
//  1.0. (See accompanying file LICENSE_1_0.txt or copy at  
//  http://www.boost.org/LICENSE_1_0.txt)  
  
// For more information, see http://www.boost.org  
  
#include <iostream>  
  
#define BOOST_FUNCTION_MAX_ARGS 0  
#include <boost/function.hpp>  
  
#undef BOOST_FUNCTION_MAX_ARGS // does not work without this  
//#define BOOST_FUNCTION_MAX_ARGS 1 // work both with or without  
#include <boost/function.hpp>  
  
#include <functional>  
  
struct X {  
  X(int val) : value(val) {}  
  
  int foo(int x) { return x * value; }  
  
  int value;  
};  
  
  
int  
main()  
{  
  boost::function<int (int)> f;  
  X x(7);  
  f = std::bind1st(std::mem_fun(&X::foo), &x);  
  
  std::cout << f(5) << std::endl; // Call x.foo(5)  
  return 0;  
}  
```

---

## Comment 4

> Username: eldiener  
> Created_at: 2017-07-04 14:04:01 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-312886250  

As I understand it you do not want to process boost/function.hpp if it is included more than once in a translation unit and the #define for BOOST_FUNCTION_MAX_ARGS has not changed. Is this correct ? If so you seem to have handled this in an incredibly roundabout manner. Why not simply #define BOOST_FUNCTION_MAX_ARGS_DEFINED to be the same as BOOST_FUNCTION_MAX_ARGS the first time that boost/function.hpp is included and then only process boost/function.hpp in any subsequent time if BOOST_FUNCTION_MAX_ARGS_DEFINED is not equal to BOOST_FUNCTION_MAX_ARGS. It seems to me you can easily do this in boost/function.hpp without having to change any other file.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-07-05 02:30:45 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-312981991  

Ignore my previous comment, as it does not work. I am still looking at your solution.

---

## Comment 6

> Username: eldiener  
> Created_at: 2017-07-06 19:44:19 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-313499245  

I have pushed a fix for your problem to the 'develop' branch similar to, but slightly more compact, than what you proposed. Would you please try this fix and see if it solves your problem. I also added a test of multiple inclusions of function.hpp with different BOOST_FUNCTION_MAX_ARGS in order to test the fix.

---

## Comment 7

> Username: jeanphilippeD  
> Created_at: 2017-07-06 21:44:21 UTC  
> Url: https://github.com/boostorg/function/pull/13#issuecomment-313527943  

Thanks you very much, that is great.  
I just checked with your patch and it solves my issue.

---
