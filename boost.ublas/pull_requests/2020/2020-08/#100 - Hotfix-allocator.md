# #100 Hotfix/allocator [Closed]

> Username: bassoy  
> Created at: 2020-08-05 10:35:23 UTC  
> Updated at: 2021-01-04 20:10:06 UTC  
> Closed at: 2020-08-12 06:07:30 UTC  
> Url: https://github.com/boostorg/ublas/pull/100  

This is a hotfix for issue https://github.com/boostorg/ublas/issues/96 which should be merged ASAP.

---

## Review 1 [Commented]

> Username: jwakely  
> Created_at: 2020-08-05 10:40:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/100#pullrequestreview-461542503  

📁 include/boost/numeric/ublas/storage.hpp

```diff
  77 | #endif
  78 |                   for (pointer d = data_; d != data_ + size_; ++d)
  79 |+ #if __cplusplus >= 201703L
```

> Username: jwakely  
> Created_at: 2020-08-05 10:39:41 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465633924  

Strictly speaking, this change is needed for C++11 and above (i.e. `__cplusplus >= 201103L`) because there is no guarantee that a type meeting the Allocator requirements provides a `construct` member.  
  
(The fact nobody noticed this until now suggests nobody tests this code with anything except `std::allocator`, making the allocator support a waste of time.)

> Username: bassoy  
> Created_at: 2020-08-05 12:58:45 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465707530  

C++11 standard defines allocator requirements with a `construct` and `destroy` member function, see the [draft version](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3690.pdf#subsubsection.17.6.3.5). So I guess it should stay like this.

> Username: jwakely  
> Created_at: 2020-08-05 13:36:35 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465731524  

No, you're wrong. Look at the last column of that table. The default shown in the last column means it is **not required** because `allocator_traits` can provide it. Look at the `SimpleAllocator` example on the page after the table of requirements.

> Username: amitsingh19975  
> Created_at: 2020-08-05 13:51:17 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465741499  

Jonathan Wakely is right.  
  
"An allocator may constrain the types on which it can be instantiated and the arguments for which its construct member may be called. If a type cannot be used with a particular allocator, the allocator class or the call to construct may fail to instantiate."  
  
These were the quotes in the draft with the example below.

> Username: jwakely  
> Created_at: 2020-08-05 13:54:30 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465743639  

That quote isn't actually relevant here, that's talking about something else. The relevant wording is in paragraph 2, before the table, where it says "A requirement is optional if the last column of Table 28 specifies a default for a given expression."  
  
Since `construct` and `destroy` have defaults in the last column, they are optional.

> Username: amitsingh19975  
> Created_at: 2020-08-05 14:03:39 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465750238  

yep, `construct` and `destroy` are optional as they have a specified default.

> Username: amitsingh19975  
> Created_at: 2020-08-05 14:08:15 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465753545  

Thanks for the information I didn't know about it earlier as I used to create my own allocator similar to the given example and use `std::allocator_traits` instead or I rarely used it.

> Username: jwakely  
> Created_at: 2020-08-05 14:09:52 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465754738  

I've created #101 for this problem, since it predates #96 and affects all of C++11 and later, not just C++20.

> Username: jwakely  
> Created_at: 2020-08-05 14:12:08 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465756296  

N.B. you can also refer to https://en.cppreference.com/w/cpp/named_req/Allocator which makes it clear which parts of the requirements are optional.

> Username: bassoy  
> Created_at: 2020-08-05 14:23:28 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465764898  

You are right. Thanks for clarification and for creating the issue https://github.com/boostorg/ublas/issues/101.

> Username: bassoy  
> Created_at: 2020-08-05 15:08:04 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465797448  

Changed the hotfix accordingly.

---

📁 include/boost/numeric/ublas/storage.hpp

```diff
 154 |                         if (size < size_) {
 155 |                             for (; di != data_ + size; ++di) {
 156 |+ #if __cplusplus >= 201703L
```

> Username: jwakely  
> Created_at: 2020-08-05 10:39:54 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465634045  

Needed since C++11 again.

> Username: bassoy  
> Created_at: 2020-08-05 12:58:55 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465707617  

See previous comment

---

📁 include/boost/numeric/ublas/storage.hpp

```diff
 164 |                         else {
 165 |                             for (; si != p_data + size_; ++si) {
 166 |+ #if __cplusplus >= 201703L
```

> Username: jwakely  
> Created_at: 2020-08-05 10:40:05 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465634161  

C++11

> Username: bassoy  
> Created_at: 2020-08-05 12:59:08 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465707753  

See previous comment

---

📁 include/boost/numeric/ublas/storage.hpp

```diff
 211 | #endif
 212 |                         for (pointer si = p_data; si != p_data + size_; ++si)
 213 |+ #if __cplusplus >= 201703L
```

> Username: jwakely  
> Created_at: 2020-08-05 10:40:17 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465634258  

C++11

> Username: bassoy  
> Created_at: 2020-08-05 12:59:16 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465707840  

See previous comment

---

📁 include/boost/numeric/ublas/storage.hpp

```diff
 191 | #endif
 192 |                             for (pointer di = data_; di != data_ + size; ++di)
 193 |+ #if __cplusplus >= 201703L
```

> Username: jwakely  
> Created_at: 2020-08-05 10:40:23 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465634314  

C++11

> Username: bassoy  
> Created_at: 2020-08-05 12:59:25 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465707942  

See previous comment

---

📁 include/boost/numeric/ublas/storage.hpp

```diff
 172 |                             }
 173 |                             for (; di != data_ + size; ++di) {
 174 |+ #if __cplusplus >= 201703L
```

> Username: jwakely  
> Created_at: 2020-08-05 10:40:33 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465634385  

C++11

> Username: bassoy  
> Created_at: 2020-08-05 12:59:32 UTC  
> Updated_at: 2020-08-12 05:57:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#discussion_r465708004  

See previous comment


---

## Comment 2

> Username: jwakely  
> Created_at: 2020-08-05 10:46:51 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-669122889  

You should really have tests that verify the code can be used with a minimal allocator that **only** provides the members required by a C++11 allocator (i.e. `value_type`, `allocate`, `deallocate`, and `==`/`!=`). All other members should be accessed via `allocator_traits` to handle the case where the allocator doesn't provide them.  
  
For testing libstdc++ I use:  
  
```cpp  
  template <class Tp>  
    struct SimpleAllocator  
    {  
      typedef Tp value_type;  
  
      SimpleAllocator() noexcept { }  
  
      template <class T>  
        SimpleAllocator(const SimpleAllocator<T>&) { }  
  
      Tp *allocate(std::size_t n)  
      { return std::allocator<Tp>().allocate(n); }  
  
      void deallocate(Tp *p, std::size_t n)  
      { std::allocator<Tp>().deallocate(p, n); }  
    };  
  
  template <class T, class U>  
    bool operator==(const SimpleAllocator<T>&, const SimpleAllocator<U>&)  
    { return true; }  
#if __cpp_impl_three_way_comparison < 201907L  
  template <class T, class U>  
    bool operator!=(const SimpleAllocator<T>&, const SimpleAllocator<U>&)  
    { return false; }  
#endif  
```

---

## Comment 3

> Username: bassoy  
> Created_at: 2020-08-05 12:33:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-669166194  

> You should really have tests that verify the code can be used with a minimal allocator that **only** provides the members required by a C++11 allocator (i.e. `value_type`, `allocate`, `deallocate`, and `==`/`!=`). All other members should be accessed via `allocator_traits` to handle the case where the allocator doesn't provide them.  
  
Yes, the code coverage for some Boost.uBlas data types are poor.   
  
  
>   
> For testing libstdc++ I use:  
>   
> ```c++  
>   template <class Tp>  
>     struct SimpleAllocator  
>     {  
>       typedef Tp value_type;  
>   
>       SimpleAllocator() noexcept { }  
>   
>       template <class T>  
>         SimpleAllocator(const SimpleAllocator<T>&) { }  
>   
>       Tp *allocate(std::size_t n)  
>       { return std::allocator<Tp>().allocate(n); }  
>   
>       void deallocate(Tp *p, std::size_t n)  
>       { std::allocator<Tp>().deallocate(p, n); }  
>     };  
>   
>   template <class T, class U>  
>     bool operator==(const SimpleAllocator<T>&, const SimpleAllocator<U>&)  
>     { return true; }  
> #if __cpp_impl_three_way_comparison < 201907L  
>   template <class T, class U>  
>     bool operator!=(const SimpleAllocator<T>&, const SimpleAllocator<U>&)  
>     { return false; }  
> #endif  
> ```  
  
Thanks for providing the simple allocator. We are moving from one-/two- to multi-dimensional types (where one- or two dimensions will be special cases) making many of the current types obsoluete and therefore depcretated. Hence, our main focus will be on developing the multi-dimensional type.

---

## Review 4 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-08-05 13:05:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/100#pullrequestreview-461643159  

Looks good.

---

## Comment 5

> Username: glenfe  
> Created_at: 2020-08-05 17:31:09 UTC  
> Updated_at: 2020-08-06 20:55:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-669327102  

This PR is not good. We don't want to use `__cplusplus >= 201103L`, if anything we would want to use `!defined(BOOST_NO_CXX11_ALLOCATOR)` but instead of all of that:  
  
Edit: On top of that, you can't assume `size_type` and `difference_type` exist for every Allocator either. The same for `max_size()`.  
  
Just include `<boost/core/allocator_access.hpp>` and use the appropriate facilities from there. They do the right thing and support minimal allocators. This also avoids having to have conditional code depending on whether `allocator_traits` is available or not.

---

## Comment 6

> Username: bassoy  
> Created_at: 2020-08-06 15:10:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-669988364  

>   
>   
> This PR is not good. We don't want to use `__cplusplus >= 201103L`, if anything we would want to use `!defined(BOOST_NO_CXX11_ALLOCATOR)` but instead of all of that:  
>   
> Just include `<boost/core/allocator_access.hpp>` and use the appropriate facilities from there. They do the right thing and support minimal allocators. This also avoids having to have conditional code depending on whether `allocator_traits` is available or not.  
  
Thanks @glenfe    
Do we have time left for merging master this into the boost 1.74 release?

---

## Comment 7

> Username: mclow  
> Created_at: 2020-08-06 15:12:45 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-669989893  

The deadline for putting things into the 1.74.0 release was .. yesterday.  
As far as I can tell, this hasn't been tested on `develop`, let alone "is ready to be merged to master". Is that correct?

---

## Comment 8

> Username: glenfe  
> Created_at: 2020-08-06 20:53:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-670186338  

See https://github.com/glenfe/boost.ublas/commit/937da383bd39aea1db85857e15035d0973b58a34  
  
I'll PR from my repository after adding some tests for minimal allocators.

---

## Comment 9

> Username: bassoy  
> Created_at: 2020-08-06 21:41:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-670206262  

@glenfe, I just wanted to update the PR using boost::core::allocator_accessor. :-)   
Thanks for helping out here.

---

## Comment 10

> Username: bassoy  
> Created_at: 2020-08-06 21:50:54 UTC  
> Updated_at: 2020-08-06 21:59:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-670209537  

>   
>   
> The deadline for putting things into the 1.74.0 release was .. yesterday.  
> As far as I can tell, this hasn't been tested on `develop`, let alone "is ready to be merged to master". Is that correct?  
  
The `unbounded_array` is unit tested in AppVeyor and Travis. However, the test coverage might not be very high here.

---

## Comment 11

> Username: glenfe  
> Created_at: 2020-08-11 01:26:39 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-671671079  

@bassoy See https://github.com/boostorg/ublas/pull/103/files .

---

## Comment 12

> Username: bassoy  
> Created_at: 2020-08-11 21:48:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-672296252  

> @bassoy See https://github.com/boostorg/ublas/pull/103/files .  
  
Thanks @glenfe.  
Please note that this PR now also includes the `boost::allocator_access` as suggested and also includes `boost::numeric::ublas::forward_iterator_base` while the PR https://github.com/boostorg/ublas/pull/103 includes test cases and  some minor modifications in `vector.h` and `storage_sparse.hpp`.  
  
To be consistent with the latter modifications, using `boost::allocator_size_type` and `boost::allocator_difference_type` I suggest to use these types also in `matrix.h` and then to merge it with the master branch.

---

## Comment 13

> Username: glenfe  
> Created_at: 2020-08-12 01:02:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-672414724  

PR #103 adjusts all places which use allocators, not just `storage.hpp`. What is it missing?

---

## Comment 14

> Username: bassoy  
> Created_at: 2020-08-12 05:49:37 UTC  
> Updated_at: 2020-08-12 05:49:56 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-672615229  

> PR #103 adjusts all places which use allocators, not just storage.hpp. What is it missing?  
  
For the sake of consistency, using `boost::allocator_size_type` and `boost::allocator_difference_type` I suggest to use these types also in `matrix.h`.  
  
I found three places in `matrix.h`  
https://github.com/boostorg/ublas/blob/a31e5cffa85f58b64a39fa7c4a1bd3bd9228b069/include/boost/numeric/ublas/matrix.hpp#L3463  
https://github.com/boostorg/ublas/blob/a31e5cffa85f58b64a39fa7c4a1bd3bd9228b069/include/boost/numeric/ublas/matrix.hpp#L3938  
https://github.com/boostorg/ublas/blob/a31e5cffa85f58b64a39fa7c4a1bd3bd9228b069/include/boost/numeric/ublas/matrix.hpp#L3463

---

## Comment 15

> Username: glenfe  
> Created_at: 2020-08-12 05:58:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-672623115  

@bassoy   
> For the sake of consistency, using boost::allocator_size_type and boost::allocator_difference_type I suggest to use these types also in matrix.h.  
  
In #103 they are being used also in `matrix.hpp`.

---

## Comment 16

> Username: glenfe  
> Created_at: 2020-08-12 06:00:37 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-672624148  

If you look at #103 **carefully**, you'll see that it modifies the following files:  
  
* matrix.hpp  
* storage.hpp  
* storage_sparse.hpp  
* vector.hpp

---

## Comment 17

> Username: bassoy  
> Created_at: 2020-08-12 06:07:24 UTC  
> Url: https://github.com/boostorg/ublas/pull/100#issuecomment-672627764  

> @bassoy  
>   
> > For the sake of consistency, using boost::allocator_size_type and boost::allocator_difference_type I suggest to use these types also in matrix.h.  
>   
> In #103 they are being used also in `matrix.hpp`.  
  
Great. I will close this PR and switch to the PR https://github.com/boostorg/ublas/pull/103

---
