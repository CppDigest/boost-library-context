# #97 - What is concept `range_like` good for? [Closed]

> Username: akrzemi1  
> Created at: 2024-02-19 22:50:51 UTC  
> Updated at: 2024-03-15 21:30:50 UTC  
> Closed at: 2024-03-15 21:30:50 UTC  
> Url: https://github.com/boostorg/parser/issues/97  

[Concepts page](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/concepts.html) has the definition of  
  
```c++  
template<typename T>  
concept range_like =  
    std::ranges::range<T> || utf_pointer<std::remove_cvref_t<T>>;  
```  
  
Which is strange on its own, and additionally is never referenced in the docs. The inspection of the source code shows that it is used in the implementation of `replace_view` but the docs do not reflect that.  
  
It doesn't make sense to define a concept that is used only once in a rarely used function.  
  
On the other hand, funciton `parse` has:  
  
```c++  
typename Enable = std::enable_if_t<detail::is_parsable_range_like_v<R>>  
```  
  
rather than using a concept. And it exposes an implementaiton detail in the interface.  
  
It looks like you need one good reusable concept, even if only for documentation purposes, that doesn't have `_like` in the name, but is called something like `parser_input`, and apart from syntactic properties, it also has semantic properties:  
  
```c++  
template <typename I>  
concept parser_input = c_string<I> || bounded_array<I> || std::ranges::range<I>;  
// if I is a c_string, then the underlying arrayis a NTBS, and `end()` is the pointer pointing to the trailing 0.  
// If I is a bounded_array then the array is NTBS, and `end()` is the pointer pointing to the trailing 0.  
// otherwise `end()` is determined as for any other STD range.  
```  
  
I do not think adding `utf8` in the concept names adds any value. You do not need to distinguish between UTF and non-UTF inputs, the docs already state that UTF is implied.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-20 00:33:48 UTC  
> Updated at: 2024-02-20 03:25:28 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1953314024  

> [Concepts page](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/concepts.html) has the definition of  
>   
> ```c++  
> template<typename T>  
> concept range_like =  
>     std::ranges::range<T> || utf_pointer<std::remove_cvref_t<T>>;  
> ```  
  
First off, that `utf_pointer` concept is just `parsable_pointer`, so I should never have introduced `utf_pointer`.  I've just changed it on a branch I opened for the Boost review.  
  
> Which is strange on its own, and additionally is never referenced in the docs. The inspection of the source code shows that it is used in the implementation of `replace_view` but the docs do not reflect that.  
  
Well, it is mentioned in the docs: https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/concepts.html  
  
... but I agree that's not the best place for it.  I'll update the doc page for the algorithms and views.  
  
> It doesn't make sense to define a concept that is used only once in a rarely used function.  
>   
> On the other hand, funciton `parse` has:  
>   
> ```c++  
> typename Enable = std::enable_if_t<detail::is_parsable_range_like_v<R>>  
> ```  
>   
> rather than using a concept. And it exposes an implementaiton detail in the interface.  
  
This is the core of the issue.  That's not the constraint on `parse()`, or at least not in C++20 mode.  The problem is that the concept constraints vanish when I enable them in the doc build, because Doxygen and/or Boostbook does not handle them; they just vanish.  I've therefore tried to call out the concept constraints in the tutorial explicitly.  
  
> It looks like you need one good reusable concept, even if only for documentation purposes, that doesn't have `_like` in the name, but is called something like `parser_input`,   
  
I think `_like` is appropriate here.  It's not a range, but it's like one enough that we can treat it as a range.  This terminology is pretty likely to make it into the standard library too: https://wg21.link/P2728  
  
> and apart from syntactic properties, it also has semantic properties:  
>   
> ```c++  
> template <typename I>  
> concept parser_input = c_string<I> || bounded_array<I> || std::ranges::range<I>;  
> // if I is a c_string, then the underlying arrayis a NTBS, and `end()` is the pointer pointing to the trailing 0.  
> // If I is a bounded_array then the array is NTBS, and `end()` is the pointer pointing to the trailing 0.  
> // otherwise `end()` is determined as for any other STD range.  
> ```  
  
This is different from range_like, in that bounded_array is superfluous.  The rest of this can be found in https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parsers_and_their_uses.html .  
  
> I do not think adding `utf8` in the concept names adds any value. You do not need to distinguish between UTF and non-UTF inputs, the docs already state that UTF is implied.  
  
Addressed above.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-20 16:38:35 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1954610481  

When I look at the reference page:  
https://tzlaine.github.io/parser/doc/html/boost/parser/parse_idm27047.html  
  
I do not see any mention of the concept.  
You can add it as a remark:  
  
> *Remarks:* this function does not participate in the overload resolution unless `parser_input<R>` is `true`.  
  
I insist on the name like `parser_input` or `text_input` rather than `range_like`, because you are not expecting a range of *any* type: only ranges for selected few character types.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-02-21 21:38:30 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1958006018  

One more thing. Why do you SFINAE out the `parse()` overload when `R` is not a range, rather than making the program ill-formed via a `static_assert`?   
  
Do you expect any other overload to kick in?  
Or do you need this to be detectable for any reason?

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-02-22 08:18:59 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1958925493  

Well, you get much better error messages when the overload fails.  In that case, the compiler will explain why, eventually telling you where the contraint failed.  With a static_assert you get none of that.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2024-02-22 09:04:29 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1958995434  

You mean "better" as in "more verbose", I suppose?  
But this error message is in the context of screenfuls of other hints produced by the compiler related to the problem and contributes to the impression of overwhelming compiler errors.  
  
Personally if I have seen "because the argument specified as input text does not satisfy concept `text_input`".

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-02-22 23:52:55 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1960529147  

I mean, "more informative".  You call it "more verbose," but it's the same thing.  If I static_assert(), all I can tell you is "One of the constraints failed; good luck."  If I let the contraint fail where the compiler can see that, the compiler actually tells you which exact atomix constraint(s) failed.  It's more verbose, but it's vital information.  Without that, I would have to write out the constraints myself, pass them the type in question, and *then* look for the actual problem.  
  
The concept error messages suck right now.  Maybe one day they'll get better.  But they do contain important signal in all that noise.

---

## Comment 7

> Username: akrzemi1  
> Created at: 2024-02-23 00:11:29 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1960547377  

https://godbolt.org/z/3xhnh9sPj

---

## Comment 8

> Username: tzlaine  
> Created at: 2024-02-23 00:26:47 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1960560429  

https://godbolt.org/z/jG5P8KaTb

---

## Comment 9

> Username: akrzemi1  
> Created at: 2024-02-23 07:46:40 UTC  
> Updated at: 2024-02-23 07:47:03 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1960866447  

Thanks. So it looks the difference becomes apparent when you use an alternative when defining concept. A difference you wouldn't see if you specified valid operations rather than enumerating types.

---

## Comment 10

> Username: akrzemi1  
> Created at: 2024-02-23 19:12:34 UTC  
> Url: https://github.com/boostorg/parser/issues/97#issuecomment-1961855038  

Ok, I filed a number of things in this issue, but I want to withdraw the one about `static_assert` versus concepts.   
  
Sorry, you are right, and I have confused two things. The long error messages that frustrate me in my use case are not due to using concepts instead of `static_asserts`, but due to other things deep in the implementation. Concepts are fine :)
