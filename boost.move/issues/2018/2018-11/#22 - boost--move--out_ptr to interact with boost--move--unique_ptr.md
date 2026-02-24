# #22 - boost::move::out_ptr to interact with boost::move::unique_ptr [Closed]

> Username: ThePhD  
> Created at: 2018-11-12 23:59:08 UTC  
> Updated at: 2018-11-19 01:18:09 UTC  
> Closed at: 2018-11-19 01:08:14 UTC  
> Url: https://github.com/boostorg/move/issues/22  

My name is JeanHeyd Meneide and I was proposing a utility class to both boost and the standard library called [`out_ptr`](https://thephd.github.io/vendor/future_cxx/papers/d1132.html). Originally, I had put out an interest post on the boost mailing list for it to be its own library, but I recently learned of `boost::move`. A few boost folks at the C++ San Diego Standards Meeting during a Boost outing (Glen F., Zach L. and others) gave me some advice: because of the need to customize `out_ptr`/`inout_ptr` for `unique_ptr` (including a `std::unique_ptr` and `boost::move::unique_ptr`) as a friended type, it might be better to simply have this utility under the `boost::move` library so the specialization and friending can just be done in-library.  
  
The short of it is that the library enables you to treat a regular smart pointer like something you can pass to a typical `T**` function commonly seem in COM and C APIs. E.g., some fictional C api called `ficapi`, the usage would look like so if it was within `boost::move`:  
  
````  
boost::move::unique_ptr<int, ficapi_int_deleter> p;  
// signature: void ficapi_create_int(int** p_handle);  
ficapi_handle_create(boost::move::out_ptr(p));   
// RAII initializes a stored int*, takes address,   
// hands it to function  
// then uses destructor to call .reset()  
ficapi_opaque_handle rawp = p.get();  
assert(rawp != nullptr);  
assert(ficapi_handle_get_data(rawp) == ficapi_get_dynamic_data());  
````  
  
The proposal has motivating examples, design decisions, benchmarks and similar, but if something seems unclear do let me know and I will do my best to explain it.  
  
It has gone through 2 rounds of design review by the Standards Committee and is going to be seen in the C++ Standards Kona meeting to see if it passes in forwarding to the Wording Group, and I have had other companies use it and have used it myself. I only propose it now because only recently have I found out that this type is seeing quite a lot of use outside of just my use cases and that people are constantly re-inventing it.  
  
Is this a utility that you think can go into `boost::move`? If so, I'd like to make a pull request to the library adding the utility and getting a review on it from the `boost::move` maintainers.

---

## Comment 1

> Username: Flamefire  
> Created at: 2018-11-13 09:35:05 UTC  
> Updated at: 2018-11-13 09:35:20 UTC  
> Url: https://github.com/boostorg/move/issues/22#issuecomment-438198946  

As it is gonna be in a future C++ standard (if not accepted for C++20 I expect it to be reworked and added to a future one) I'd definitely like to see it in Boost. I'm not a maintainer though.

---

## Comment 2

> Username: ThePhD  
> Created at: 2018-11-15 03:02:25 UTC  
> Url: https://github.com/boostorg/move/issues/22#issuecomment-438899894  

As a side note, another candidate library for this might be the Smart Pointer ([Boost.Smartptr](https://github.com/boostorg/smart_ptr)). It will still require coordination with the Boost.Move lib's `unique_ptr` for the speed customization, however.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-11-18 21:16:19 UTC  
> Url: https://github.com/boostorg/move/issues/22#issuecomment-439726527  

Looks like Boost.Smartptr would be a better choice. I guess friendship avoids the UB, but it would be better to specify which operation is needed in unique_ptr and maybe make it public to allow interoperability with other smart pointers.

---

## Comment 4

> Username: ThePhD  
> Created at: 2018-11-19 01:08:14 UTC  
> Url: https://github.com/boostorg/move/issues/22#issuecomment-439743637  

I will move to putting this in Boost.Smartptr. Thanks for the feedback!  
  
As a side note, if it does make it into Boost.Smartptr, I will also need to swing back around here to get the speed optimization for using this. In particular, we would need a `.get_ref()` function which returns a _reference to the internal `::pointer` type_.  
  
However, that's horrible design, so adding `boost::out_ptr_t` and `boost::inout_ptr_t` as friends would probably be a much better design that does not leak internals to the world!  
  
You don't have to do anything: I will make a pull request with such functionality and let you approve it should this make it into Boost.Smartptr. Thanks for your advice!
