# #700 - Allow prvalues as attributes [Closed]

> Username: denzor200  
> Created at: 2021-09-19 18:14:33 UTC  
> Updated at: 2025-05-09 23:54:01 UTC  
> Closed at: 2025-05-09 23:53:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/700  

I am using the spirit library in conjunction with the fusion library. I need to parse a structure from a string, given that the elements in the string are in reverse order.  
I use the concept of "view" from the fusion library for this, and I expect the code to look something like this:  
https://godbolt.org/z/h3KneWzGh  
  
As you can see, the build of the example stops with an error.   
The fixed variant contains the creation of a temporary view object, and its build succeeded:  
https://godbolt.org/z/PxdKrsrPh  
  
Essense of my question: can we provide on the library side an ability to use first variant (variant without creating a temporary object)?   
Is there an objective reason why its won't work?

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-09-19 19:59:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-922527986  

I feel you frustration, we even had issues in Qi with Fusion functions returning proxies for adapt adt. Simply allowing rvalue references as output parameter smells very bad, I am pretty sure we will start receiving bug reports where people accidentally create a non-proxy temporary and pass it as attribute. There is no standardized proxy interface that would allow us to detect such types, but it is not a big problem, we can provide a customization point for this. However, I really do not like making output argument rvalues, sadly C++ misses ability to switch between by value/by reference depending on a deduced template parameter type.

---

## Comment 2

> Username: denzor200  
> Created at: 2021-09-23 17:21:04 UTC  
> Updated at: 2021-09-23 17:21:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-926008201  

> sadly C++ misses ability to switch between by value/by reference depending on a deduced template parameter type.  
  
Why not so?  
https://godbolt.org/z/7b5ce4b4b

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-09-23 18:30:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-926056041  

> > sadly C++ misses ability to switch between by value/by reference depending on a deduced template parameter type.  
>   
> Why not so?  
> [godbolt.org/z/7b5ce4b4b](https://godbolt.org/z/7b5ce4b4b)  
  
That works, but only if a goal is to fix x3::parse free function, otherwise it requires to duplicate every parse function what is a very questionable practice and cruelly inconvenient for a public API. SFINAE on a top level function would definitely make compile error messages more cryptic.

---

## Comment 4

> Username: denzor200  
> Created at: 2022-01-27 20:21:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1023606588  

@Kojoley, what do you say about this? https://godbolt.org/z/YqfoYTThs  
Maybe just adding a `prvalue` function to spirit will solve this issue?

---

## Comment 5

> Username: denzor200  
> Created at: 2022-01-31 12:07:56 UTC  
> Updated at: 2022-01-31 12:13:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1025671634  

> @Kojoley, what do you say about this? https://godbolt.org/z/YqfoYTThs  
Maybe just adding a `prvalue` function to spirit will solve this issue?  
  
It's a definetly bad idea because here it UB. This example shows how to reproduce "a reference to deleted object" by use suggested approach:  
https://godbolt.org/z/dEcobKW38  
  
So, this example will be guaranted good only if we just asssign prvalue to constant reference, and only assing without forwarding through `prvalue` function:  
https://godbolt.org/z/1jcM5nG44  
  
I also guess that assing with forwarding through `static_cast<const Attribute&>` will be good but I haven't found confirmation of this yet:  
https://godbolt.org/z/hja5jT7je

---

## Comment 6

> Username: denzor200  
> Created at: 2022-01-31 12:11:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1025675041  

> @Kojoley, what do you say about this? https://godbolt.org/z/YqfoYTThs  
Maybe just adding a `prvalue` function to spirit will solve this issue?  
  
Variant 2: To implement `BOOST_SPIRIT_X3_PRVALUE` macro https://godbolt.org/z/fM8jWxa9e

---

## Comment 7

> Username: denzor200  
> Created at: 2022-07-12 05:28:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1181331349  

> Why not so?  
https://godbolt.org/z/7b5ce4b4b  
  
SFINAE is redundant https://godbolt.org/z/cb6sEzW87

---

## Comment 8

> Username: denzor200  
> Created at: 2022-08-31 10:44:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1232774322  

> I feel you frustration, we even had issues in Qi with Fusion functions returning proxies for adapt adt. Simply allowing rvalue references as output parameter smells very bad, I am pretty sure we will start receiving bug reports where people accidentally create a non-proxy temporary and pass it as attribute. There is no standardized proxy interface that would allow us to detect such types, but it is not a big problem, we can provide a customization point for this. However, I really do not like making output argument rvalues, sadly C++ misses ability to switch between by value/by reference depending on a deduced template parameter type.  
  
https://godbolt.org/z/5Mq645vWE  
It allows rvalue references as output parameter only for `fusion::view`.  
For attempting to pass a non-view as rvalue reference in output parameter it will be a readable compile-time error.  
I do not find any disadvantages of this solution, except that the universal reference as an output parameter can mislead someone.

---

## Comment 9

> Username: Kojoley  
> Created at: 2022-08-31 13:06:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1232913759  

> I do not find any disadvantages of this solution, except that the universal reference as an output parameter can mislead someone.  
  
The disadvantage is to add complexity to support very tiny minority of use cases. For me, it is just not worth of saving from typing something on a separate line.

---

## Comment 10

> Username: denzor200  
> Created at: 2022-08-31 13:49:31 UTC  
> Updated at: 2022-08-31 13:53:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-1232965305  

> The disadvantage is to add complexity to support very tiny minority of use cases. For me, it is just not worth of saving from typing something on a separate line.  
  
You right, nowadays this issue very tiny minority of use cases. But in the nearest future pfr will be used instead of adapt macro and so this issue will be the most populat case to use x3 with structs(i suppose).  
Let's save x3's interface as simple as now.  
  
I do remember that you said about automatic detection reflectable structure, without indication from user by "view". But everything has its time.

---

## Comment 11

> Username: saki7  
> Created at: 2025-05-09 23:53:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/700#issuecomment-2868112019  

Closing as stale. It seems that op and a maintainer have already reached consensus that allowing rvalue references are not the appropriate addition to X3.
