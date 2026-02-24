# #101 - Add is a Q/A section - Doesn't BOOST_FIT_STATIC_LAMBDA implies UB. [Closed]

> Username: viboes  
> Created at: 2016-02-29 23:31:02 UTC  
> Updated at: 2016-03-06 23:59:19 UTC  
> Closed at: 2016-03-01 17:05:14 UTC  
> Url: https://github.com/boostorg/hof/issues/101  

Matt Calabrese wrote:  
  
> I'm fairly certain that the reinterpret casting involved with  
> BOOST_FIT_STATIC_LAMBDA is UB.   
  
Not really, since the objects are empty, there is no data access. I have a  
static assert to guard against this restriction. Also, on gcc and clang with  
BOOST_FIT_STATIC_LAMBDA_FUNCTION, the reinterpret cast happens at compile-  
time.  
  
Paul replied:  
  
> Now there could be an insane implementation where this doesn't work(perhaps  
> the lambdas are not empty for some strange reason), which the library would  
> have to apply a different technique to make it work. However, this is quite  
> unlikely considering that C++ will probably get constexpr lambdas and inline  
> variables in the future.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-01 17:05:57 UTC  
> Url: https://github.com/boostorg/hof/issues/101#issuecomment-190816933  

I wonder if there is some more questions that could be added to the Q/A.

---

## Comment 2

> Username: viboes  
> Created at: 2016-03-01 22:33:26 UTC  
> Url: https://github.com/boostorg/hof/issues/101#issuecomment-190937801  

There are always more questions, but you can start where ever you prefer.

---

## Comment 3

> Username: viboes  
> Created at: 2016-03-01 22:34:56 UTC  
> Url: https://github.com/boostorg/hof/issues/101#issuecomment-190938812  

I would like to see if a issue is fixed/wontfix, ... when it is closed.  
It is always good to refer to the commit that has been done to fix an issue.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-03-01 22:42:57 UTC  
> Url: https://github.com/boostorg/hof/issues/101#issuecomment-190942823  

> There are always more questions, but you can start where ever you prefer.  
  
If you have some more in my mind, please share.  
  
> I would like to see if a issue is fixed/wontfix, ... when it is closed.  
> It is always good to refer to the commit that has been done to fix an issue.  
  
Yes, I believe I can refer to the issue in the commit and it will show up on the issue.
