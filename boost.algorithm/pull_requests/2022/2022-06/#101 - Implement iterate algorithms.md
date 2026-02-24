# #101 Implement iterate algorithms [Open]

> Username: jgopel  
> Created at: 2022-06-05 02:45:17 UTC  
> Updated at: 2022-06-08 17:00:12 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101  

Problem:  
- There is no algorithm that generalizes `std::iota`. Filling a range 2  
  elements at a time is hard, more complex patterns are increasingly  
  difficult.  
  
Solution:  
- Implement `iterate` and `iterate_n` which fill a range based on the  
  execution of a binary operation which takes the previous value.  
    
  h/t to @elbeno for suggesting the algorithm and some feedback on the interface

---

## Comment 1

> Username: grisumbras  
> Created_at: 2022-06-06 03:28:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101#issuecomment-1146996559  

Template parameter names imply that this algorithm operates on a range of output iterators. But there's no such thing. Output iterators aren't required to have equality comparison. The algorithm actually requires a range of forward iterators. Also is it really a major improvement over the following?  
```c++  
std::generate(first, last, [&init, op]{ return init = op(init); })  
```

---

## Comment 2

> Username: jgopel  
> Created_at: 2022-06-06 12:42:27 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101#issuecomment-1147405265  

Hi @grisumbras - thanks for the feedback! I appreciate your concerns and I'd love to arrive at a design that you think is better 🙂  
  
> Template parameter names imply that this algorithm operates on a range of output iterators. But there's no such thing. Output iterators aren't required to have equality comparison.  
  
I used `OutputIterator` as a name here to follow the pattern of template names used throughout the library where the types for the iterators that are read from is `InputIterator` (or `InputIterator1` and `InputIterator2` for the dual range algorithms) and `OutputIterator` for the iterators that are written to.  
  
[`copy_if()` and its variants](https://github.com/boostorg/algorithm/blob/00c6f1d6c10508d06598466a15190fc9812c9100/include/boost/algorithm/cxx11/copy_if.hpp#L33) as well as [`transform_inclusive_scan()` and its variants](https://github.com/boostorg/algorithm/blob/00c6f1d6c10508d06598466a15190fc9812c9100/include/boost/algorithm/cxx17/transform_inclusive_scan.hpp#L40) demonstrate the pattern I tried to follow well. Looking at the implementation for `iota()`, which is perhaps the most closely related to this algorithm, [one uses `ForwardIterator`](https://github.com/boostorg/algorithm/blob/00c6f1d6c10508d06598466a15190fc9812c9100/include/boost/algorithm/cxx11/iota.hpp#L28) and [the other uses `OutputIterator`](https://github.com/boostorg/algorithm/blob/00c6f1d6c10508d06598466a15190fc9812c9100/include/boost/algorithm/cxx11/iota.hpp#L55). There are also implementations, [such as `is_palindrome()`](https://github.com/boostorg/algorithm/blob/00c6f1d6c10508d06598466a15190fc9812c9100/include/boost/algorithm/is_palindrome.hpp#L38) which use the type name to name the iterator category.  
  
It seems like there is no clear practice that's being followed throughout the codebase. I would argue that the type of iterator should be checked by the interface, not simply held in the name. To that end - does a boost wrapper around the [iterator concepts](https://en.cppreference.com/w/cpp/header/iterator) exist? They could be added as constraints and checked where available.  
  
> Also is it really a major improvement over the following?  
  
I'll start by stating that that's a matter of opinion, but I would argue that it is an improvement. This library explicitly targets C++03 where lambdas aren't available - it's a clear win over writing a function object in that case. Even in standards where lambdas exist though, for me this API more clearly captures intent than `generate`. Perhaps an improvement here would be to give it a better name - perhaps what I've called `iterate` is actually the stateful case of `generate`?

---

## Comment 3

> Username: elbeno  
> Created_at: 2022-06-06 13:54:51 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101#issuecomment-1147474329  

A few observations:  
  
- `std::generate` uses forward iterators, `std::generate_n` uses an output iterator; probably `iterate`/`iterate_n` should do likewise?  
- since C++20, std::accumulate moves the value through the algorithm; IMO `iterate`/`iterate_n` should do the same, although obviously not available before C++11 - presumably there is a boost macro?  
- (docs/reqs) `UnaryOperation` here is required to take the same type as it returns, or at least have the one implicitly convertible to the other.  
- was there a reason to do `n -= 1` rather than `--n`? IMO decrement is preferred since it's a lower strength operation than generalized subtraction.  
- the code should probably defend against overloaded `operator,` in the for loops by casting one side to `void`; I'm not sure what the boost practice is for that.  
  
The question "is iterate preferable to generate?" has a similar flavour to the question "is accumulate preferable to for_each?" In both cases the same effect as the "dedicated" function can be achieved with the "more general" function and stateful lambda expressions. But I consider both accumulate and iterate to be important upgrades in intent and in composability. Both also allow a more declarative coding style.  
  
iterate is an unfold, just as accumulate is a fold, and comparing iterate to accumulate (and contrasting with generate/for_each) is useful. iterate wins over generate in a couple of important ways:  
  
- it returns the iterated value computed: this is important for composability, when we consider what happens after the call returns: maybe a further call? Folds and unfolds model incremental computations by nature.  
- stateful lambdas inhibit parallelism in general: this isn't directly a problem comparing generate to iterate because both are defined to proceed in series (just like accumulate and for_each both are), but we can imagine a parallelizable drop-in replacement for iterate, with extra requirements, just as reduce behaves that way for accumulate. e.g. it seems clear that generalized addition is the operation that unlocks parallelism of iota, where the serial version requires only increment. I don't yet know how requirements for a parallelized unfold might be stated in C++, but having iterate as the first step here seems good.  
  
One last thing: the name iterate is taken from Haskell. I haven't conducted a survey of other languages to see what the prevailing name of least surprise, if any, would be.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2022-06-06 19:01:52 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101#issuecomment-1147787334  

> It seems like there is no clear practice that's being followed throughout the codebase.  
  
The pattern is evidently that the name of the template parameter type corresponds to the named requirement. And regardless, using `OutputIterator` for something that is a mutable forward iterator is not ideal.  
  
With regards to the name of the functions. `iterate` is a very generic name. How about calling it `generate`, since that's what it appears to be?

---

## Comment 5

> Username: jgopel  
> Created_at: 2022-06-08 03:34:02 UTC  
> Updated_at: 2022-06-08 03:34:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101#issuecomment-1149418525  

I was misunderstanding the concern on iterator requirements - I've updated that to a state that I think is correct.  
  
> since C++20, std::accumulate moves the value through the algorithm; IMO iterate/iterate_n should do the same, although obviously not available before C++11 - presumably there is a boost macro?  
  
I have not been able to find a boost macro for move, unfortunately. If one exists, I'll happily pepper it throughout as I agree that that makes the behavior more flexible.  
  
> was there a reason to do n -= 1 rather than --n? IMO decrement is preferred since it's a lower strength operation than generalized subtraction.  
  
No - just a code style I've been trying out. (Roughly, the idea is that if prefix `++` is better than postfix `++` in terms of communication of intent about reuse, then `+= 1` is even more clear that the purpose of the operation is incrementing and that the result of that is not needed.) I've updated to `--` as I agree that it's the right choice in the generic case.  
  
> One last thing: the name iterate is taken from Haskell. I haven't conducted a survey of other languages to see what the prevailing name of least surprise, if any, would be.  
  
I did some digging on this and couldn't find any other languages with similar named facilities where it wasn't called `iterate`. I have reached out to some people for suggestions there - I'll post here if anything comes back.

---

## Comment 6

> Username: jgopel  
> Created_at: 2022-06-08 17:00:12 UTC  
> Url: https://github.com/boostorg/algorithm/pull/101#issuecomment-1150167190  

An update on the naming:  
  
As far as I can tell, this algorithm is only named in functional language standard libraries. Haskell, Clojure, OCaml, and Scala (and perhaps more, those were the ones that I found) have this algorithm and all of them call it `iterate`.

---
