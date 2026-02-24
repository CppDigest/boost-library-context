# #208 - comments on `url_view::collect` [Closed]

> Username: akrzemi1  
> Created at: 2022-06-24 19:34:04 UTC  
> Updated at: 2022-07-16 01:49:18 UTC  
> Closed at: 2022-07-16 01:49:18 UTC  
> Url: https://github.com/boostorg/url/issues/208  

While the docs give an example on how to use  `url_view::collect()`, they do not show what is the use case it is supposed to address, and it is difficult -- at leat for me -- to come up with a motivating use case. Why would the following not be sufficient?  
  
```c++  
std::make_shared<const urls::url>(view);  
```  
  
How is `url_view` better than `const url` in this use case?  
  
The name `collect` does not suggest what is going on. If `std::make_shared` is not good enough, why not  the following instead?  
  
  
```c++  
urls::url::make_shared(view);  
```  
  
The benefit of this interface is that class `url_view` does not have to be aware of the existence of class `url`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-24 20:16:49 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1165904631  

Good point. We need to improve that.  
  
The benefit of `collect` over `make_shared`/`allocate_shared` is using a single allocation instead of two+ allocations.  
It requires access to the URL representation to do that.  
  
`collect` is intentionally a little vague because there is no intuitive name. So users must consult the docs.  
The problem is the documentation describes what it does but doesn't describe this benefit. I'll open a PR for that.  
  
Maybe `collect` could be intentionally not vague, but it's not easy to come up with an intuitive name.   
Some people have suggested: .alias(), .fix(), clone(), clone_view(), persist(), const_shared_from_this(), make_const_shared(), ...  
None of these names are very intuitive and `collect` is reasonably fine.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-24 21:40:15 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1165960975  

Then name `collect` is Peter's idea, and he didn't like any of the alternatives.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-06-24 21:47:38 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1165965397  

`persist` is good.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-24 21:50:01 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1165967090  

`persist` is a less common word but if Peter is happy then I am happy

---

## Comment 5

> Username: pdimov  
> Created at: 2022-06-24 21:54:21 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1165970523  

`persist` is probably better than `collect`.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-06-25 18:59:14 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1166344276  

So the actionable suggestions are:  
  
- rename to `persist`   
- better goal-oriented documentation

---

## Comment 7

> Username: akrzemi1  
> Created at: 2022-06-25 20:42:50 UTC  
> Updated at: 2022-06-25 20:51:17 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1166358494  

> The benefit of `collect` over `make_shared`/`allocate_shared` is using a single allocation instead of two+ allocations. It requires access to the URL representation to do that.  
  
Whoa, this is brilliant!   
But it is not easy to appreciate, even after your explanation, because when we talk about making two allocations instead of one in the context of `make_shared`, the immediate connotation is doing one allocation for the `T` and for the reference counter. But this is more that that.  
  
> So the actionable suggestions are:  
>  
>  * rename to persist  
>  * better goal-oriented documentation  
  
I would add to the list:  
  
 * Explain in the docs that this is for having only one allocation: for the reference counter, the character buffer and the pointed-to type.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-06-25 20:59:24 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1166360528  

Oh yeah I guess you're right about that. It is one instead of three. And to your other point, I used `url_view` because it has less API surface than `url const`, even though those additional APIs are in theory uninvocable (because the members are const). This means you only have to include url_view.hpp to use `shared_ptr<url_view const>`, which is easier on compilation time.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-06-25 21:00:54 UTC  
> Url: https://github.com/boostorg/url/issues/208#issuecomment-1166360751  

@akrzemi1 I often lay out data as a fixed-size struct followed by a variable length collection of bytes or characters - this is a recurring design theme of mine. I have encapsulated the logic for achieving this via single normal allocation into this implementation detail:  
https://github.com/CPPAlliance/url/blob/4c2d8f71aa2b18bd6e3bc5f075127be8922b97eb/include/boost/url/detail/over_allocator.hpp#L43
