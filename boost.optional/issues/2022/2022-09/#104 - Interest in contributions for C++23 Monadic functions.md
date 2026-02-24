# #104 - Interest in contributions for C++23 Monadic functions? [Closed]

> Username: cmazakas  
> Created at: 2022-09-02 22:01:12 UTC  
> Updated at: 2022-09-06 23:03:39 UTC  
> Closed at: 2022-09-06 22:43:26 UTC  
> Url: https://github.com/boostorg/optional/issues/104  

https://en.cppreference.com/w/cpp/utility/optional  
  
C++23 added some new monadic functions.  
  
I see these already exist under the names `value_or_eval`, `map` and `flat_map`.  
  
The contribution here would be adding the C++23 compatible member function names and then correspondingly updating the existing tests. Is there any interest in getting something like this included? I'd be volunteering here.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2022-09-03 06:51:04 UTC  
> Url: https://github.com/boostorg/optional/issues/104#issuecomment-1236062169  

Hi. Thank you for your interest in the library, and for your contribution offer. If I understand correctly, we are talking about adding new names to an already existing functionality, right?  
  
It is worth giving it a thought, however note that Boost.Optional does not aim at 100% compatibility with `std::opitonal`, for instance:  
  
 * it has tag `boost::none` rather than `boost::nullopt`,  
 * it has tag `in_place_init` rather than `in_place` (which is reserved in Boost for in-place factories),  
 * it doesn't have converting constructors between `optional<U>` and `optional<T>`, which cause consistency bugs in `std::optional`  
  
Not to mention that `boost::optional` offers a bigger interface.  
So, the users already have to be aware which `optional` they are using.

---

## Comment 2

> Username: cmazakas  
> Created at: 2022-09-03 21:58:04 UTC  
> Url: https://github.com/boostorg/optional/issues/104#issuecomment-1236204231  

> If I understand correctly, we are talking about adding new names to an already existing functionality, right?  
  
Yup! This would be so `boost::optional` is a drop-in replacement and vice versa, acting as a polyfill for eventual C++23 code.  
  
> it doesn't have converting constructors between optional<U> and optional<T>, which cause consistency bugs in std::optional  
  
Would you like this as well?

---

## Comment 3

> Username: akrzemi1  
> Created at: 2022-09-03 22:14:34 UTC  
> Url: https://github.com/boostorg/optional/issues/104#issuecomment-1236206106  

> > it doesn't have converting constructors between optional<U> and optional<T>, which cause consistency bugs in std::optional  
>   
> Would you like this as well?  
  
Nope. This converting constructor has a number of gotchas, and is therefore bug prone.

---

## Comment 4

> Username: cmazakas  
> Created at: 2022-09-06 22:43:26 UTC  
> Url: https://github.com/boostorg/optional/issues/104#issuecomment-1238724578  

Alright, so sounds like a no-go which is cool to me. In the interim, if there's anything you'd like contributions for, just let me know!  
  
I'm going to go ahead and close this issue as it seems resolved to me.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2022-09-06 23:03:39 UTC  
> Url: https://github.com/boostorg/optional/issues/104#issuecomment-1238734471  

Sure. thanks. For instance, we have this one: https://github.com/boostorg/optional/issues/105
