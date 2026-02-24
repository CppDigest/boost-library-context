# #23 Casts for std::unique_ptr and std::shared_ptr [Merged]

> Username: koraa  
> Created at: 2015-12-17 14:49:12 UTC  
> Updated at: 2016-09-10 19:21:41 UTC  
> Merged at: 2016-09-10 18:10:44 UTC  
> Closed at: 2016-09-10 18:10:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23  

I am currently trying to implement the smart_ptr casts for both std::shared_ptr and std::unique_ptr.  
  
It is not finished yet: this PR serves to track the progress and to discuss the code structure and design of those features, since I am pretty sure there are some improvements needed there :)  
  
I started a mailing list thread about this a while ago: http://lists.boost.org/Archives/boost/2015/10/225912.php  
  
Most of the core functionality is implemented, that includes all the casts for std::shared_ptr and std::unique_ptr and tests.  
  
Currently I am considering the following changes before merge:  
- Tests for reinterpret_cast (it was untested before)  
- Tests for intrusive_ptr  
- Moving casts for scoped_ptr  
- Refactoring the tests; at the moment I am using std::function to generate pointers for the moving casts. I am not sure whether that's OK. Besides, there is a lot of code duplication between the moving cast tests and the old tests, that could be removed.  
- Document when the approach seams stable

---

## Comment 1

> Username: koraa  
> Created_at: 2015-12-21 11:59:57 UTC  
> Updated_at: 2015-12-21 12:08:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-166283167  

Since this came up in the mailing list, here is a motivating example: http://melpon.org/wandbox/permlink/R3QWA7MaxZ5o1ocG  
  
Demonstrates use cases for static_pointer_cast(std::unique_ptr<...>&&) and dynamic_pointer_cast(std::unique_ptr<...>&&)

---

## Comment 2

> Username: Flast  
> Created_at: 2015-12-22 04:19:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-166501810  

How do you think about allowing unsafe cast, which I replied you http://lists.boost.org/Archives/boost/2015/10/225991.php.  
  
> Since this came up in the mailing list, here is a motivating example: http://melpon.org/wandbox/permlink/R3QWA7MaxZ5o1ocG  
  
It also has same problem, like this http://melpon.org/wandbox/permlink/zUICUvPcQFoHmT5I.

---

## Comment 3

> Username: koraa  
> Created_at: 2015-12-22 10:22:56 UTC  
> Updated_at: 2015-12-22 10:28:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-166575646  

Hi @Flast ,  
I looked at your code before writing this PR and I thought the problem was mainly associated with not making the casts nothrow. Sorry I missed the bit about the destructors :)  
  
In the modified version of my example, I see that the destructor of the non_trivial_dtor_listener is not being called. That's because the base class was declared without a virtual constructor. afaik you run into the same problem when using any other pointer type – destructor just need to be virtual for runtime polymorphism.  
  
I do agree that this is slightly unsafe, but it is also the semantic you would expect when working with virtuals in c++.  
  
In your original example. I see the same case as above and I also see, that you can not move the deleter of a unique pointer. As I understand it, being able to cast the deleter would be nice feature, but it isn't a problem for an implementation that simply doesn't provide that feature – i.e. only works with std::default_delete on parameter and return value.

---

## Comment 4

> Username: koraa  
> Created_at: 2015-12-22 10:28:48 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-166576539  

@Flast Maybe we can actually add a static_assert to warn about that case (downcasting to something with a non virtual destructor)

---

## Comment 5

> Username: Flast  
> Created_at: 2015-12-24 14:06:40 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-167116524  

> Maybe we can actually add a static_assert to warn about that case (downcasting to something with a non virtual destructor)  
  
Agreed. [`boost::has_virtual_destructor`](http://www.boost.org/doc/libs/1_60_0/libs/type_traits/doc/html/boost_typetraits/reference/has_virtual_destructor.html) will help.

---

## Comment 6

> Username: koraa  
> Created_at: 2015-12-24 17:28:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-167139875  

@Flast There is also a std implementation, http://www.cplusplus.com/reference/type_traits/has_virtual_destructor/ . I though it would be better to use this, since it would pull less requirements. Is this correct?

---

## Comment 7

> Username: Flast  
> Created_at: 2015-12-26 07:32:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-167292382  

> I though it would be better to use this, since it would pull less requirements. Is this correct?  
  
IMO, Boost.TypeTraits is better than std's: you should check `BOOST_NO_CXX11_HDR_TYPE_TRAITS` to use `<type_traits>`.

---

## Comment 8

> Username: pdimov  
> Created_at: 2016-09-10 15:01:18 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-246116392  

I created a feature branch for this - feature/unique_ptr_casts. Still needs a few more tests, will add them later.

---

## Comment 9

> Username: koraa  
> Created_at: 2016-09-10 19:21:41 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/23#issuecomment-246131038  

Thank you very much! I didn't really know which test cases to write :)

---
