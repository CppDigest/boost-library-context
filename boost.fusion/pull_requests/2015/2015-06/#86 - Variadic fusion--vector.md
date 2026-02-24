# #86 Variadic fusion::vector. [Closed]

> Username: Flast  
> Created at: 2015-06-27 16:17:50 UTC  
> Updated at: 2015-07-15 16:55:37 UTC  
> Closed at: 2015-07-15 07:35:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/86  

Extracted from #57.  
  
Tested:  
- GCC 5.1.0 gnu++98 / gnu++11 / gnu++14  
- Clang 3.6.1 gnu++98 / gnu++11 / gnu++14  
- MSVC 9 / 10 / 11 / 12  
  - Note: All of MSVCs are fallback into preprocessed vector.

---

## Comment 1

> Username: Flast  
> Created_at: 2015-06-27 18:44:17 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116115426  

compile time performance: https://gist.github.com/Flast/0b5ae15049f465387e34

---

## Comment 2

> Username: K-ballo  
> Created_at: 2015-06-27 22:20:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116163264  

Those numbers look bad, but it is mostly due to IO. My measurements without it are (see [here](https://gist.github.com/K-ballo/38382223ef5bca3e0948)):  
  
Baseline (just `#includes`):  
  
> TOTAL                 :   0.40             77442 kB  
  
Vector of 50 elements:  
  
> TOTAL                 :   0.43 (0.03)      85071 kB (7629kB)  
  
Vector of 100 elements:  
  
> TOTAL                 :   0.48 (0.08)      94262 kB (16820kB)  
  
Vector of 200 elements:  
  
> TOTAL                 :   0.61 (0.21)      118673 kB (41231kB)  
  
These look good, but not great. I'll see if I can identify the weak spots (the baseline is awfully high btw, but it's pulling like 900 other headers or so). This is only for default construction, as I removed the IO; we should be checking others (most notably element access, maybe iteration too?).  
  
This doesn't work with VS2013, the preprocessed version is picked instead. This would work with VS2013 with reasonably little effort, mostly providing a `vector_data<>` specialization as it has trouble expanding empty packs. I can do this work if you want and send a patch your way.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-06-27 23:08:59 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417326  

I thought we will not be be worried about void_ anymore? Let's try as much as possible to minimize the code.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2015-06-27 23:09:22 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417329  

I'm not a fan of this approach. If I write an overload or specialization for `vector<Ts...>` I may end up getting noise due to the tag. But I'm not sure how important that use case is, either.

---

## Comment 5

> Username: K-ballo  
> Created_at: 2015-06-27 23:09:52 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417334  

Looks like you could get rid of this entire specialization if you specialize `construct_vector` below.

---

## Comment 6

> Username: djowel  
> Created_at: 2015-06-27 23:10:09 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417335  

How about move semantics?

---

## Comment 7

> Username: djowel  
> Created_at: 2015-06-27 23:11:57 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417344  

We can simplify this by leveraging decltype(auto), instead. Maybe we should target C++14 instead of C++11.

---

## Comment 8

> Username: djowel  
> Created_at: 2015-06-27 23:14:35 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417356  

IIRC, vector_data is a workaround for C++03 compilers (or older). I think this can be removed and simplified in a C++11/14 world.

---

## Comment 9

> Username: djowel  
> Created_at: 2015-06-27 23:18:56 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417380  

I agree. Also, the named vectorN types are there for pre C++11 optimization only. Perhaps we might consider deprecating these?

---

## Comment 10

> Username: K-ballo  
> Created_at: 2015-06-27 23:19:00 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417382  

I'd suggest against it. The result type has to be computed anyways, so this would create an unnecessary dependency on C++14, as well as creating a compilation time worry as "incomplete" functions need to be instantiated as soon as anyone touches them.

---

## Comment 11

> Username: K-ballo  
> Created_at: 2015-06-27 23:22:20 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417415  

I'll hold my analysis until this is decided, as handling of trailing `void_` is my first concern.

---

## Comment 12

> Username: djowel  
> Created_at: 2015-06-27 23:24:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116165412  

Overall, I think this is a good first step, but there are still lots of room for improvement. Perhaps we can work on these incrementally in small steps. I move that we target C++14 instead of C++11 so we can leverage more important constructs and idioms. I also move that we remove all artefacts of C++03 such as void_ and the result-type computations. In fact, if we do it this way, the result_of mechanism can be made redundant with decltype. We can still provide it for backward compatibility with simple decltype forwarders to the actual code.

---

## Comment 13

> Username: djowel  
> Created_at: 2015-06-27 23:26:59 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417433  

Is there a problem with requiring C++14 instead of C++11? It's 2015 anyway ;-)

---

## Comment 14

> Username: djowel  
> Created_at: 2015-06-27 23:28:15 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33417440  

> creating a compilation time worry as "incomplete" functions need to be instantiated as soon as anyone touches them.  
  
Please explain a bit more...

---

## Comment 15

> Username: K-ballo  
> Created_at: 2015-06-27 23:31:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116165590  

> Overall, I think this is a good first step, but there are still lots of room for improvement. Perhaps we can work on these incrementally in small steps.  
  
Agreed  
  
> I move that we target C++14 instead of C++11 so we can leverage more important constructs and idioms.  
  
I don't see a reason for that.  
  
> I also move that we remove all artefacts of C++03 such as void_ and the result-type computations. In fact, if we do it this way, the result_of mechanism can be made redundant with decltype. We can still provide it for backward compatibility with simple decltype forwarders to the actual code.  
  
I'm all for removing C++03 artifacts. As an example, I could chop of ~20Mb of memory usage by introducing very complex machinery to fast-track the handling of trailing `void_`s when there is no such type. I can only imagine that removing said machinery would be even better.  
  
I have concerns with regards to result-type computations, I value the `result_of` namespace. Building it on top of `decltype` is an option, but only if no function uses return type deduction, as that would mean early instantiations. Result-types computations have to be done either way, even if one were to use `decltype(auto)`, so they are not going away anytime soon and they have to be defined somewhere.

---

## Comment 16

> Username: djowel  
> Created_at: 2015-06-27 23:57:36 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116166775  

> I'm all for removing C++03 artifacts. As an example, I could chop of ~20Mb of memory usage by introducing very complex machinery to fast-track the handling of trailing void_s when there is no such type. I can only imagine that removing said machinery would be even better.  
>   
> I have concerns with regards to result-type computations, I value the result_of namespace. Building it on top of decltype is an option, but only if no function uses return type deduction, as that would mean early instantiations. Result-types computations have to be done either way, even if one were to use decltype(auto), so they are not going away anytime soon and they have to be defined somewhere.  
  
I think the issue here is which is faster: decltype(auto) of foo(arg) vs. foo::template apply<Arg>::type which in many cases also contain some icky metaprogramming to get its results. You got me thinking on early instantiations though, but I am not sure if that is a problem because in many cases, fusion functions are very simple, like the example above.   
  
Anyway, in the end, the compile time is the guide. So, forget this for now. I'll get back to this later and probably play around with another branch to compare the difference in either approach. maybe a combination of both approach will be a good idea.  
  
PS> decltype(auto) in X3 did shave off some CT.

---

## Comment 17

> Username: K-ballo  
> Created_at: 2015-06-28 00:21:33 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116170106  

> I think the issue here is which is faster: decltype(auto) of foo(arg) vs. foo::template apply::type which in many cases also contain some icky metaprogramming to get its results.  
  
Consider the following use case, which represents Fusion:  
  
```  
template <typename ...Ts>  
typename result_of_something<Ts...>::type something(Ts&&... vs) {  
  return typename result_of_something<Ts...>::type(vs...);  
}  
template <typename ...Ts>  
decltype(auto) something(Ts&&... vs) {  
  return typename result_of_something<Ts...>::type(vs...);  
}  
```  
  
The use of `decltype(auto)` here trades off one lookup (no extra instantiation due to memoization) versus a whole function instantiation. The icky metaprogramming is present in both, and the cost of the metaprogramming itself is the same in either case.  
  
> You got me thinking on early instantiations though, but I am not sure if that is a problem because in many cases, fusion functions are very simple, like the example above.  
  
Instantiations cascade, their body "size" is not really relevant.  
  
> Anyway, in the end, the compile time is the guide. So, forget this for now. I'll get back to this later and probably play around with another branch to compare the difference in either approach. maybe a combination of both approach will be a good idea.  
  
I can think of a handful of places where `decltype(auto)` could be used with no harm, but you'd be giving up C++11 for a few extra typed chars, along with SFINAE. Furthermore, those places I have in mind are `at` implementations and the like, where you would be causing instantiations of an entire overload set even on a single element access.  
  
> PS> decltype(auto) in X3 did shave off some CT.  
  
I've been keeping track of (most of) those changes, and as far as I could tell `decltype` shaved off some CT, not `decltype(auto)`. Otherwise I would have raised these concerns then.

---

## Comment 18

> Username: djowel  
> Created_at: 2015-06-28 00:37:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116172624  

> I think the issue here is which is faster: decltype(auto) of foo(arg) vs. foo::template apply::type which in many cases also contain some icky metaprogramming to get its results.  
> Consider the following use case, which represents Fusion:  
>   
> template <typename ...Ts>  
> typename result_of_something<Ts...>::type something(Ts&&... vs) {  
>   return typename result_of_something<Ts...>::type(vs...);  
> }  
> template <typename ...Ts>  
> decltype(auto) something(Ts&&... vs) {  
>   return typename result_of_something<Ts...>::type(vs...);  
> }  
> The use of decltype(auto) here trades off one lookup (no extra instantiation due to memoization) versus a whole function instantiation. The icky metaprogramming is present in both, and the cost of the metaprogramming itself is the same in either case.  
  
Ah that's not what I meant. result_of_something<Ts...>::type can be dug up and down the line, there will no longer be any icky metaprogramming, instead, only function calls. The point here is to get rid of result_of_something in the first place. Anyway, I don't have time to present some code at this point. I'll leave this for now and experiment later.   
  
> You got me thinking on early instantiations though, but I am not sure if that is a problem because in many cases, fusion functions are very simple, like the example above.  
> Instantiations cascade, their body "size" is not really relevant.  
>   
> Anyway, in the end, the compile time is the guide. So, forget this for now. I'll get back to this later and probably play around with another branch to compare the difference in either approach. maybe a combination of both approach will be a good idea.  
> I can think of a handful of places where decltype(auto) could be used with no harm, but you'd be giving up C++11 for a few extra typed chars, along with SFINAE. Furthermore, those places I have in mind are at implementations and the like, where you would be causing instantiations of an entire overload set even on a single element access.  
>   
> PS> decltype(auto) in X3 did shave off some CT.  
> I've been keeping track of (most of) those changes, and as far as I could tell decltype shaved off some CT, not decltype(auto). Otherwise I would have raised these concerns then.  
  
Good points. It's a mix, so I can't really tell. At the very least, seems we can agree about decltype.

---

## Comment 19

> Username: K-ballo  
> Created_at: 2015-06-28 01:05:10 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116179863  

> I think the issue here is which is faster: decltype(auto) of foo(arg) vs. foo::template apply::type which in many cases also contain some icky metaprogramming to get its results.  
>     Consider the following use case, which represents Fusion:  
>   
> ```  
> template  
> typename result_of_something::type something(Ts&&... vs) {  
> return typename result_of_something::type(vs...);  
> }  
> template  
> decltype(auto) something(Ts&&... vs) {  
> return typename result_of_something::type(vs...);  
> }  
> The use of decltype(auto) here trades off one lookup (no extra instantiation due to memoization) versus a whole function instantiation. The icky metaprogramming is present in both, and the cost of the metaprogramming itself is the same in either case.  
> ```  
>   
> Ah that's not what I meant. result_of_something::type can be dug up and down the line, there will no longer be any icky metaprogramming, instead, only function calls. The point here is to get rid of result_of_something in the first place. Anyway, I don't have time to present some code at this point. I'll leave this for now and experiment later.  
  
Any snippet you can think of can be expressed as `auto something() -> decltype(expr)`, which does not incur the implicit instantiation on touch cost. This is particularly easy for Fusion, because its functions tend to be very simple.  
  
> ```  
> PS> decltype(auto) in X3 did shave off some CT.  
> I've been keeping track of (most of) those changes, and as far as I could tell decltype shaved off some CT, not decltype(auto). Otherwise I would have raised these concerns then.  
> ```  
>   
> Good points. It's a mix, so I can't really tell. At the very least, seems we can agree about decltype.  
  
Certainly, `decltype` is a nice metaprogramming tool ever since [N3276](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2011/n3276.pdf) removed the requirement on return type instantiation. Automatic return type deduction not only brings return type instantiation back, it brings whole body instantiation and not only in `decltype` expressions but in every expression its name appears (ADL included).

---

## Comment 20

> Username: djowel  
> Created_at: 2015-06-28 01:27:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116181828  

> Any snippet you can think of can be expressed as auto something() -> decltype(expr), which does not incur the implicit instantiation on touch cost. This is particularly easy for Fusion, because its functions tend to be very simple.  
  
Agreed! 100% very good points.

---

## Comment 21

> Username: Flast  
> Created_at: 2015-06-28 03:37:47 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33418503  

> Also, the named vectorN types are there for pre C++11 optimization only. Perhaps we might consider deprecating these?  
  
Hmm... I think that vectorN is commonly used rather than trailing void_.

---

## Comment 22

> Username: Flast  
> Created_at: 2015-06-28 03:38:08 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33418504  

Good catch!

---

## Comment 23

> Username: Flast  
> Created_at: 2015-06-28 03:54:46 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33418588  

If Spirit V2 removing use of trailing void_, we can remove workaround for void_ sooner.

---

## Comment 24

> Username: djowel  
> Created_at: 2015-06-28 04:21:10 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33418698  

Let's do it together. There's no reason to delay this further. Let's do it now and I'll adjust Spirit to follow Fusion.

---

## Comment 25

> Username: djowel  
> Created_at: 2015-06-28 04:23:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-116191045  

Unless Agustin (or anyone else) has any more comments, I'd like to merge this now. Let us then incrementally improve what we have.

---

## Comment 26

> Username: djowel  
> Created_at: 2015-06-28 06:47:40 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33419427  

Keep in mind that vectorN was needed only because of the lack of variadics in C++03. So I had to somehow map each concrete instantiation of vectorN to a vector. There can be a better implementation here. vectorN is a C++03 implementation artefact, IMO.

---

## Comment 27

> Username: K-ballo  
> Created_at: 2015-06-28 13:28:36 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33422715  

An implicitly deduced return type function has a placeholder as return type, forming something of an "incomplete" function declaration. The function declaration is only completed within the definition, once `return` is seen. Any use of such functions, even those in which a declaration would suffice, need to cause an instantiation in order to have a definition for it to "complete" the declaration.  
  
**7.1.6.4 [dcl.spec.auto]/12**  
  
Return type deduction for a function template with a placeholder in its declared type occurs when the definition is instantiated even if the function body contains a **return** statement with a non-type-dependent operand. [_Note:_ Therefore, any use of a specialization of the function template will cause an implicit instantiation. Any errors that arise from this instantiation are not in the immediate context of the function type and can result in the program being ill-formed.  —_end note_] [_Example_:  
  
```  
template <class T> auto f(T t) { return t; }  // return type deduced at instantiation time  
typedef decltype(f(1)) fint_t;                // instantiates f<int> to deduce return type  
template<class T> auto f(T* t) { return *t; }  
void g() { int (*p)(int*) = &f; }             // instantiates both fs to determine return types,  
                                              // chooses second  
```  
  
 —_end example_]

---

## Comment 28

> Username: djowel  
> Created_at: 2015-06-28 20:22:42 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33426530  

Thanks, @K-ballo

---

## Comment 29

> Username: Flast  
> Created_at: 2015-06-29 02:58:33 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33431640  

OK, I'll remove void_ support in this PR.

---

## Comment 30

> Username: Flast  
> Created_at: 2015-06-29 03:07:55 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33431885  

Ah sorry, I cannot remove it now, because fusion::set and fusion::nview are require void_ yet.

---

## Comment 31

> Username: djowel  
> Created_at: 2015-06-29 03:09:40 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33431916  

Ah, OK. Let's do it later.

---

## Comment 32

> Username: Flast  
> Created_at: 2015-06-29 04:26:58 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33433298  

OK, I agree on deprecating vectorN in C++11/14, but we should provide vectorN for a while (e.g. until 1.60.0) though.

---

## Comment 33

> Username: Flast  
> Created_at: 2015-06-29 12:08:20 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33456988  

Can I alias vectorN to vector (without size checking)? It will break some specializations, but we can remove evil numbered_vector_tag.

---

## Comment 34

> Username: djowel  
> Created_at: 2015-07-01 00:44:36 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33639861  

Hmmm, I don't think that is good. Let's just stick to the current plan (deprecating vectorN in C++11/14, but we should provide vectorN for a while).

---

## Comment 35

> Username: Flast  
> Created_at: 2015-07-01 01:23:13 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33641302  

I meant in C++11 and later, not for C++03.  
In this case, we can throw the tag away 298eeef9c9942436e6c32210407815aac049a6b0, but need a patch for Spirit like this Flast/spirit@4d8899fdfe26237d6d983a7ac5528d00f1416ec7.

---

## Comment 36

> Username: djowel  
> Created_at: 2015-07-01 02:26:57 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33643734  

Ah OK, that sounds good. We have to be very clear on the deprecation and announce it.

---

## Comment 37

> Username: Flast  
> Created_at: 2015-07-02 14:42:47 UTC  
> Updated_at: 2015-07-02 15:02:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#discussion_r33784563  

OK, I'm writing a compatibility notice, but we can commit it later (i.e. bugfix / docfix duration). Can I append [the change](https://github.com/boostorg/fusion/commit/298eeef9c9942436e6c32210407815aac049a6b0) into this PR? I want to publish all variadic containers within 1.59.0 Beta.

---

## Comment 38

> Username: Flast  
> Created_at: 2015-07-02 15:16:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-118064926  

`numbered_vector_tag` has been removed.

---

## Comment 39

> Username: djowel  
> Created_at: 2015-07-02 21:56:21 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-118179040  

Looks like we're all set for merge. I'll let this sit for another day for you and K-ballo to take one last look, then merge tomorrow.

---

## Comment 40

> Username: Flast  
> Created_at: 2015-07-07 15:21:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-119237523  

I think it's late for 1.59 release, since this is a **major** change: master branch will be closed for _all_ change (i.e. **minor** change) today! Also, (I think) develop will be reserved for bugfixing of 1.59 beta.  
  
Therefore it is good way to continue to review, close this PR and reopen to other branch, based on current develop (e.g. create `experimental` branch), for 1.60 release.

---

## Comment 41

> Username: Flast  
> Created_at: 2015-07-15 02:08:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-121457889  

@djowel could you open a new branch, based on current develop? I cannot send a PR to non-existing branch.

---

## Comment 42

> Username: djowel  
> Created_at: 2015-07-15 03:08:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-121470642  

I'm sorry, what is non-existing?

---

## Comment 43

> Username: djowel  
> Created_at: 2015-07-15 06:37:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-121507732  

OK: https://github.com/boostorg/fusion/tree/variadics

---

## Comment 44

> Username: Flast  
> Created_at: 2015-07-15 07:35:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/86#issuecomment-121516427  

Thanks!

---
