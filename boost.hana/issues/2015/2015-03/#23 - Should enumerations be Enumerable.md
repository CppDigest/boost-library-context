# #23 - Should enumerations be Enumerable? [Closed]

> Username: ldionne  
> Created at: 2015-03-22 16:31:52 UTC  
> Updated at: 2015-09-14 19:43:16 UTC  
> Closed at: 2015-09-13 01:16:32 UTC  
> Url: https://github.com/boostorg/hana/issues/23  

This was triggered a while ago by a discussion on the [Boost.Devel](http://thread.gmane.org/gmane.comp.lib.boost.devel/255045) mailing list. We could provide a macro allowing an enumeration to be defined, while at the same time giving it a model of the `Enumerable` concept. I'll consider adding this if enough people want it.

---

## Comment 1

> Username: m-j-w  
> Created at: 2015-09-13 09:48:13 UTC  
> Url: https://github.com/boostorg/hana/issues/23#issuecomment-139857146  

Louis, I should take the opposition on that, not necessarily on the removal of the concept, but the removal of `succ` and `pred`, for simple reason: When working with values and generic algorithms, you would want to get the next and previous possible value of a data type, like in a simple for loop where you do a `for (int i = 0; i < 10; ++i) {}`. Removing them seems like removing value-based algorithm capability and restricting the library to index-based algorithms.   
  
As such, I would in fact propose the opposite direction of this commit, taking the point of your commit message into account: Re-introduce an `inc` and `dec` method, provide a general overload to `operator++` and `operator--`, and add a `max_val` and `min_val` to the datatype to define bounds. That way you would have implicitely defined an interval, stride etc.  
  
One might argue, that `operator++` simply is `plus(v, one(v))`, but `one` is not necessarily the same value. Suppose a user wants to implement generic `stride`s, like over even numbers as an extension to `range`, then `inc` and `dec` would equal 2, still with unit 1 to fulfil the laws of the ring.  
  
That would then give you the opportunity to `for (myT i = zero(myT()); i < max_val(myT()); ++i)`, for some type that has been somehow modified during compile-time to fit your actual run-time needs - a purpose I would assume to be quite common, as in iterate over all possible values and check whether they are included in a run-time sequence. Whether that defines a concept of an `Enumerable` or simply extends `Iterable` or something like `Value_Iterable`, I don't know. Perhaps `Incrementable` would suit best.  
  
Besides, I would in fact vote for `inc` and `dec` to remove ambiguity. I read `pred` always as shorthand for predicate, like it is frequently used as template parameter, and am I bit irritated.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-09-14 14:41:26 UTC  
> Url: https://github.com/boostorg/hana/issues/23#issuecomment-140102264  

First, I agree with you that getting rid of `Enumerable` is taking the easy road instead of fixing the issue at the root by adding bounds. However, the thing is that I'd like Hana's concepts to be a little less spread out, because it's a large library in terms of concepts right now. In the same vein, I've been thinking about removing the abstract algebra based numerical hierarchy in favor of the "dumb" `IntegralConstant` concept. Sure, it's not as clean and general, but it's simpler and it's sufficient for most metaprogramming tasks. I've been thinking that maybe these would belong to another library focusing on the numerical hierarchy itself.  
  
Basically, I'm torn between going for abstraction and generality or reducing the scope of the library.  
  
Also, if I decide to keep `Enumerable`, I do agree that `inc` and `dec` would make more sense than `pred` and `succ`, and I'll rename them.

---

## Comment 3

> Username: m-j-w  
> Created at: 2015-09-14 19:43:16 UTC  
> Url: https://github.com/boostorg/hana/issues/23#issuecomment-140186510  

Dear Louis, I think your objective changed a little towards stripping Hana down to a version which would soon reach a stable state for a version 1.0. In that, simplifying the algebraic/mathematical parts appears reasonable to focus first on the other issues – like the references in tuples.  
  
However, I find the basic idea to implement elementary algebraic and functional programming aspects quite compelling. And that is also a unique selling point of Hana. On the other hand, I’m fully agreeing with the point of the present concepts being rather bloated. It also took me quite a while to understand the rather crazily intertwined concepts. Either way, if there is any basic math operator overload, then all should be supported, including ++ and --, since these are part of the fundamental C++ vocabulary. If these are missing, one gets easily confused.  
  
Beside that, and coming back to the matter of concepts in general: I see basically three ways, where the first should be an absolute minimal solution:  
  
1)  Move features you want to remove to a different branch, something like _retired_ or _hibernating_code_ - to remind yourself and others on the matter and those possibilities. Just deleting is bad since that knowledge is then lost.  
  
2)  Simplify the concepts into something like _Algebraic_ which implements possibly all basic operator overloads (+,-,*,/, +=, -=, *=, /=, ++, --). Then, the concept would simply assume that they exist, and it would be the users responsibility to fully implement all or some of those. An algorithm would then have to check with `when<…>` whether the required methods are implemented. Most algorithms should only require one or two features of a data type. Similarly, _Orderable_, _Comparable_ could be combined, etc.   
  
3)  Alternatively, you could really strip everything down and provide one _Scalar_ or _Element_ concept, one _Container_ and a _Functional_ concept, to use those solely to check a some sort of top level category. Probably an _Indices_ concept should then follow, to provide _range_, _stride_, …  
An algorithm could then simply complain about "this is not gonna work with a _Scalar_, just with a _Container_", or "I expect some _Indices_ there". These concepts could then really define minimal implementations, plus some possible refinements. If the user provides those refinements, he would get specialized versions of algorithms, which could go into the library at some later point in time. The _Algebraic_ or _Math_ concept could in this scenario be a tutorial on how to use and extend Hana.  
  
For myself, I was considering to use Hana for several possible aspects in a future code:  
-   Re-arrange algebraic/calculus operations at compile-time to get efficient run-time code, on the basis that most of the computations evaluate to zero and could be filtered out a-priori.  
-   Abstraction of underlying data structures from other but similar libraries (popular example: python data objects, but also other proprietary stuff).  
-   Improvement of array/vector storage layout AoS, SoA, AoSoA, and on top of that choosing the most efficient algorithm for the hardware, from a selection of specific implementations.  
  
Yes, there are already several solutions for each of these. Most libraries are extremely bloated, require several years of experience, and some don’t even make use of C++11. Thus, for me, both the mathematic as well as the tuple-related stuff is equally relevant. But I could more easily add mathematics than heterogeneous containers. And in case you wonder, the interesting aspect of Hana is that it provides some sort of interface between most of the above aspects.  
  
So, please keep in mind: Users will use a library for a huge variety of applications and purposes, not only those intended or expected by the library developer… best example: C++ templates ;-)
