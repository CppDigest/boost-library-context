# #50 Tuple transform [Closed]

> Username: HDembinski  
> Created at: 2020-05-20 15:58:25 UTC  
> Updated at: 2020-05-24 14:18:14 UTC  
> Closed at: 2020-05-24 14:18:14 UTC  
> Url: https://github.com/boostorg/mp11/pull/50  

Adds `tuple_transform`, which applies a callable to each tuple element and stores the results in another tuple. Also works with std::pair, but not with std::array.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2020-05-20 16:01:58 UTC  
> Updated_at: 2020-05-21 15:23:20 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-631568801  

I would be grateful for a first review before going on.  
  
To-do:  
- [x] Replace dummpy tuple_transform_cx.cpp with proper implementation  
- [x] Write docs

---

## Comment 2

> Username: HDembinski  
> Created_at: 2020-05-20 16:35:53 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-631588130  

Should tuple_transform also work with std::array?

---

## Comment 3

> Username: HDembinski  
> Created_at: 2020-05-20 16:45:27 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-631593566  

To say it again here: the caveat of this implementation is the platform-dependent evaluation order of the functor. The evaluation order varies with different versions of gcc, for instance.

---

## Comment 4

> Username: pdimov  
> Created_at: 2020-05-20 18:28:36 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-631647127  

> Should tuple_transform also work with std::array?  
  
Probably not.

---

## Comment 5

> Username: pdimov  
> Created_at: 2020-05-20 18:29:30 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-631647610  

> To say it again here: the caveat of this implementation is the platform-dependent evaluation order of the functor.  
  
It's not clear that there should be any evaluation order guarantee here in any case.

---

## Comment 6

> Username: HDembinski  
> Created_at: 2020-05-21 13:35:17 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632089704  

Ok, then I will proceed with this implementation. A specialization for std::array could be explicitly written if so desired.

---

## Comment 7

> Username: pdimov  
> Created_at: 2020-05-21 13:43:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632093894  

I'm not sure about the decay though.

---

## Comment 8

> Username: HDembinski  
> Created_at: 2020-05-21 15:22:14 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632146700  

How do I build the docs?  
```  
WARNING: Unable to construct doc/mp11.html of type HTML with these properties: [...]  
WARNING: Considered these as possible generators:  
WARNING: boostbook.docbook-to-onehtml with source types { DOCBOOK } and requirements { }  
```

---

## Comment 9

> Username: HDembinski  
> Created_at: 2020-05-21 15:22:45 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632146975  

> I'm not sure about the decay though.  
  
On second thought, I agree. I removed it.

---

## Comment 10

> Username: pdimov  
> Created_at: 2020-05-21 15:25:47 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632148640  

> How do I build the docs?  
  
You need Asciidoctor (and probably `using asciidoctor ;` in your user-config.jam).

---

## Comment 11

> Username: HDembinski  
> Created_at: 2020-05-21 15:32:30 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632155701  

> > To say it again here: the caveat of this implementation is the platform-dependent evaluation order of the functor.  
>   
> It's not clear that there should be any evaluation order guarantee here in any case.  
  
You are right. My mental model was "this should work like [std::transform](https://en.cppreference.com/w/cpp/algorithm/transform)". I naively assumed that std::transform evaluates the iterator ranges in order, but now that I looked at the wording carefully, it also does not guarantee any particular evaluation order either. They protect themselves with the statement that the functions may not have side effects.

---

## Comment 12

> Username: HDembinski  
> Created_at: 2020-05-21 15:35:14 UTC  
> Updated_at: 2020-05-21 15:45:07 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632159104  

I think the next step is to generalize this to N tuples and N-ary functions, do you agree?  
Following `std::transform`, I implemented this with argument order (tuple, functor), but the generalization to N tuples would require (functor, tuples...).  
  
In Boost Histogram, I actually need the generic form.

---

## Comment 13

> Username: HDembinski  
> Created_at: 2020-05-21 15:43:14 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632166273  

Come to think of it, `tuple_for_each` could have been generalized like this, too, if the order was (functor, tuple) instead of (tuple, functor).

---

## Comment 14

> Username: pdimov  
> Created_at: 2020-05-21 16:23:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632188911  

> I implemented this with argument order (tuple, functor), but the generalization to N tuples would require (functor, tuples...).  
  
Yep. I was going to switch the argument order anyway with this in mind, but it'd be better if you do that. See `mp_transform`.

---

## Comment 15

> Username: pdimov  
> Created_at: 2020-05-21 16:24:59 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632189606  

> I naively assumed that std::transform evaluates the iterator ranges in order, but now that I looked at the wording carefully, it also does not guarantee any particular evaluation order either.  
  
Only `std::for_each` guarantees evaluation order.

---

## Comment 16

> Username: pdimov  
> Created_at: 2020-05-21 16:28:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632191934  

More remarks: your tests don't use the value of the argument; since the two-element case is already tested with `pair`, `tuple` should test another size, perhaps three.

---

## Comment 17

> Username: HDembinski  
> Created_at: 2020-05-23 06:04:21 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-632991870  

Ok, I will fix the tests and add the generalization to N tuples. Should we pass the callable as const reference to indicate that it should not have side effects? Or by value as the standard does in std::transform?

---

## Comment 18

> Username: HDembinski  
> Created_at: 2020-05-23 07:48:00 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-633002783  

We can also accept the tuples as const ref only. This would simplify the implementation.

---

## Comment 19

> Username: HDembinski  
> Created_at: 2020-05-23 07:49:33 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-633002923  

I think const ref for the callable makes more sense than passing by value. I don't understand why `std::transform` and friends accept the callable by value.

---

## Comment 20

> Username: HDembinski  
> Created_at: 2020-05-23 11:31:32 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-633032100  

It is ready for review again.

---

## Comment 21

> Username: HDembinski  
> Created_at: 2020-05-23 13:48:38 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-633057318  

msvc-12.0 does not like my code:   
https://ci.appveyor.com/project/pdimov/mp11/builds/33068700/job/2n7hi3e1prct4twn?fullLog=true#L6890  
The error message is telling me nothing... do you know what is going on?

---

## Comment 22

> Username: pdimov  
> Created_at: 2020-05-23 16:02:58 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-633083126  

Don't worry about it.

---

## Comment 23

> Username: pdimov  
> Created_at: 2020-05-24 14:18:14 UTC  
> Url: https://github.com/boostorg/mp11/pull/50#issuecomment-633237918  

Squash-merged in https://github.com/boostorg/mp11/commit/dcf0d7ebdfb6607e98ee44f52655a23b12476acc. Thanks.

---
