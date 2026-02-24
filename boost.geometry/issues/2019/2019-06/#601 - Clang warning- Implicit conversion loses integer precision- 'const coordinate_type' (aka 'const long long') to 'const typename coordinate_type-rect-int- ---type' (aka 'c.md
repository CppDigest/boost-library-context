# #601 - Clang warning: Implicit conversion loses integer precision: 'const coordinate_type' (aka 'const long long') to 'const typename coordinate_type<rect<int> >::type' (aka 'const int') [Closed]

> Username: vschoech  
> Created at: 2019-06-21 09:32:20 UTC  
> Updated at: 2019-10-22 17:51:34 UTC  
> Closed at: 2019-10-21 09:50:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/601  

I'm using Apple LLVM version 10.0.0 to compile boost 1.70.0. The following warning (which we declared an error for our project) occurs in geometry:  
  
> /boost_1_70_0/boost/geometry/strategies/cartesian/expand_point.hpp:66:45: Implicit conversion loses integer precision: 'const coordinate_type' (aka 'const long long') to 'const typename coordinate_type<rect<int> >::type' (aka 'const int')  
  
See also:  
https://svn.boost.org/trac10/ticket/13604  
  
#598

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 10:06:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/601#issuecomment-505810211  

Hi @vschoech , this warning on itself is fine and should be there.  
The question is where it comes from. It can either (1) come from inside our library (I assume that), or (2) you call some function with different types and it comes from there.  
  
I did some checks for (1) and in my checks the local types there all match the input types.  
I tried to check (2) but any overlay does not support (yet) different coordinate types, there is a static assertion for that. But maybe you don't apply it with an overlay.  
  
Do you have minimal code to reproduce this warning?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-26 10:09:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/601#issuecomment-505811349  

Closed #598 as duplicate with this one, and also the trac one, conform description above

---

## Comment 3

> Username: vschoech  
> Created at: 2019-10-21 09:50:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/601#issuecomment-544439061  

> Hi @vschoech , this warning on itself is fine and should be there.  
> The question is where it comes from. It can either (1) come from inside our library (I assume that), or (2) you call some function with different types and it comes from there.  
  
Hi @barendgehrels , I can now confirm that we are indeed calling some function with different types, i.e., the problem is rooted in our own code and must be fixed on our end. Thank you very much for looking into it on your end. I apologize for the false alarm! *blush*

---

## Comment 4

> Username: barendgehrels  
> Created at: 2019-10-22 17:51:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/601#issuecomment-545078671  

@vschoech  Hi Volker, OK, no problem, I'm glad it could be closed now.
