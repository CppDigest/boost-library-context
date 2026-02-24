# #119 - boost::span cannot be constructed with iterators [Open]

> Username: gcerretani  
> Created at: 2022-05-16 16:43:54 UTC  
> Updated at: 2023-07-23 20:45:06 UTC  
> Url: https://github.com/boostorg/core/issues/119  

`std::span` constructors 2 and 3 of [cppreference.com documentation](https://en.cppreference.com/w/cpp/container/span/span) support generic iterators, while `boost::span` accepts only pointers. A possible workaround is to apply the operators `&*` to the iterators.  
  
https://godbolt.org/z/sjEozr6M5  
  
```c++  
#include <span>  
#include <boost/core/span.hpp>  
  
#include <vector>  
  
void foo(const std::vector<int>& v) {  
    const auto std_span = std::span<const int>(v.begin(), v.end()); // fine  
    // const auto boost_span = boost::span<const int>(v.begin(), v.end()); // broken  
    const auto boost_span = boost::span<const int>(&*v.begin(), &*v.end()); // fine with hack  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-16 16:50:45 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127904771  

I don't think adding constructors from iterators is a good idea, unless we can require [contiguous iterators](https://en.cppreference.com/w/cpp/iterator/contiguous_iterator). Which means these constructors would only be available in C++20 and onwards.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-05-16 17:01:26 UTC  
> Updated at: 2022-05-16 17:01:41 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127914732  

It's possible to approximate this by checking for random access and then asserting that `&*last == &*first + (last - first`). Not perfect, but better than users applying `&*` themselves, which will work for non-random-access and which we can't check for validity even at runtime.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-05-16 17:02:46 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127916065  

`&*` -> `boost::to_address`

---

## Comment 4

> Username: Lastique  
> Created at: 2022-05-16 17:22:20 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127935914  

Dereferencing end iterator would be UB.  
  
Also, I don't like a runtime assert. The code should simply not compile, otherwise it's too bug-prone IMHO. At most, we could hardcode `std::vector::iterator` and `std::string::iterator`, but I would rather we didn't.  
  
Let the user be explicit in his intentions - let him explicitly write `&*` or better yet use `.data()`.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-05-16 17:32:55 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127947091  

Hence `to_address`.  
  
As I said, having the user type the `&*` is much more error-prone, not to mention wrong in principle because `&*end()`.

---

## Comment 6

> Username: Lastique  
> Created at: 2022-05-16 17:44:14 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127957573  

Requiring the users to type it makes them aware what they are doing. While accepting iterators and doing it internally could hide a bug. For example, when the span is constructed from an unknown type of range, e.g. in a template function.  
  
Note that the assert may still spuriously pass invalid iterators. E.g. from `std::deque`.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-05-16 17:56:46 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127968636  

Iterators from deque that pass the assert will actually form a valid span.  
  
> Requiring the users to type it makes them aware what they are doing.  
  
Only in theory. In practice, all it does is eliminate the opportunity to check iterator category or assert. No awareness will be obtained.

---

## Comment 8

> Username: Lastique  
> Created at: 2022-05-16 18:03:33 UTC  
> Updated at: 2022-05-16 18:04:16 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127974450  

> Iterators from deque that pass the assert will actually form a valid span.  
  
This would be runtime-dependent. Meaning the code will pass or fail from one run to another, worst kind of bug. When it should just always fail to compile.  
  
> Only in theory. In practice, all it does is eliminate the opportunity to check iterator category or assert. No awareness will be obtained.  
  
I'd like to believe the user is a sane person who knows what and why they are typing.  
  
If we really want these constructors in pre-C++20, let's restrict them to being pointers only.

---

## Comment 9

> Username: pdimov  
> Created at: 2022-05-16 18:05:34 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127976296  

> This would be runtime-dependent. Meaning the code will pass or fail from one run to another. When it should just always fail to compile.  
  
Yes, but we can't reliably reject it at compile time while allowing the correct cases without C++20. It's obviously a tradeoff that errs towards accepting the correct cases in lieu of rejecting the incorrect cases.

---

## Comment 10

> Username: Lastique  
> Created at: 2022-05-16 18:07:21 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127977923  

> If we really want these constructors in pre-C++20, let's restrict them to being pointers only.  
  
Actually, let's add a new `is_contiguous_iterator` trait that will be usable outside `boost::span`. For pre-C++20 it would be true only for pointers.

---

## Comment 11

> Username: Lastique  
> Created at: 2022-05-16 18:09:42 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1127980222  

> Yes, but we can't reliably reject it at compile time while allowing the correct cases without C++20. It's obviously a tradeoff that errs towards accepting the correct cases in lieu of rejecting the incorrect cases.  
  
My point is that it is the user who must explicitly say "yes, I know what I'm doing", not the library assume he does. This is standard practice in C++.

---

## Comment 12

> Username: glenfe  
> Created at: 2022-05-17 05:11:13 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1128418142  

Yes, I did not add these constructors because pre-C++20 it would not be possible to detect contiguous iterators. If users want them in C++20 mode (where we have `std::contiguous_iterator`) I'm happy to have constructors along the lines of:  
  
```  
template<std::contiguous_iterator I>  
requires detail::convertible<std::iter_reference_t<I>, T>::value  
constexpr explicit(E != dynamic_extent)  
span(I f, size_type n) noexcept  
    : s_(std::to_address(f), n) { }  
  
template<std::contiguous_iterator I, std::sized_sentinel_for<I> L>  
requires detail::convertible<std::iter_reference_t<I>, T>::value &&  
    (!std::is_convertible_v<L, size_type>)  
constexpr explicit(E != dynamic_extent)  
span(I f, L l) noexcept(noexcept(l - f))  
    : s_(std::to_address(f), static_cast<size_type>(l - f)) { }  
```

---

## Comment 13

> Username: gcerretani  
> Created at: 2022-05-17 06:46:39 UTC  
> Updated at: 2022-05-17 06:46:56 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1128478252  

Thanks a lot for your comments. The best, in my opinion, would be to allow those constructors also in pre-C++20 mode, leaving the user responsible for the code, but I trust your experiences when you say that this is not C++ standard practice. Eventually wrapping the iterators in `boost::to_address` could an accceptable way to say "I know what I'm doing" in pre-C++20 mode.  
  
It would be great to add few lines about this on the documentation, too.

---

## Comment 14

> Username: pdimov  
> Created at: 2023-07-23 17:06:31 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646893052  

> Actually, let's add a new `is_contiguous_iterator` trait that will be usable outside `boost::span`. For pre-C++20 it would be true only for pointers.  
  
On second thought, this (`boost::core::is_contiguous_iterator`) is a good idea. We can make this trait yield true for the common cases of `std::string::(const_)iterator` and `std::vector<T>::(const_)iterator`, which should take care of the majority of uses.

---

## Comment 15

> Username: glenfe  
> Created at: 2023-07-23 18:10:23 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646919439  

That sounds fine. Also fine if the trait is not public (`boost::detail::span_is_contiguous_iterator` etc.)

---

## Comment 16

> Username: pdimov  
> Created at: 2023-07-23 18:17:34 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646921656  

It should be public so that people can specialize it for their iterators (if they insist.)

---

## Comment 17

> Username: Lastique  
> Created at: 2023-07-23 20:01:19 UTC  
> Updated at: 2023-07-23 20:04:55 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646947136  

I'd rather we didn't specialize `is_contiguous_iterator` for `std::string` and `std::vector` iterators as it would necessitate including the respective standard library headers and that might be too expensive.  
  
Note also, there are `std::string_view`, `std::array`, `std::span`, `std::valarray` and maybe some other components that I'm forgetting about that have contiguous iterators. Supporting all of those would definitely be too expensive, and supporting some begs the question "why those?"

---

## Comment 18

> Username: pdimov  
> Created at: 2023-07-23 20:10:24 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646948880  

Not specializing for string and vector makes the whole thing completely useless. We either do it and specialize, or do nothing at all, nothing else makes any sense.

---

## Comment 19

> Username: Lastique  
> Created at: 2023-07-23 20:15:40 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646949976  

Specializing for iterators is just not scalable. What about non-std iterators?  
  
IMO, specializing only for non-`void` pointers is enough. People who want to construct spans from iterators in pre-C++20 can either use `to_address` or specialize the trait themselves - for those iterators they actually use.

---

## Comment 20

> Username: pdimov  
> Created at: 2023-07-23 20:21:01 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646950948  

No, they can't. It's not possible to specialize the trait for `std::vector<T>::iterator`.  
  
More precisely, it would be technically possible if our trait has an `Enable = void` second parameter, but they'll need to write their own "is_vector_iterator" trait, something that's our responsibility because it's both nontrivial and is O(1) effort for us and O(N) for them.

---

## Comment 21

> Username: Lastique  
> Created at: 2023-07-23 20:45:06 UTC  
> Url: https://github.com/boostorg/core/issues/119#issuecomment-1646955534  

They can specialize for their specific `std::vector` types. Or, if they really insist, they can specialize for `__gnu_cxx::__normal_iterator<T*, Container>`, if they know they only need to support libstdc++. Other standard libraries probably also have similar types that the user could hook into. That's not something that we can or should do in Boost though. Doing it in Boost would at best support `std::vector<T>::iterator`, but not e.g. `std::vector<T, custom_allocator<T>>::iterator`, and even that would be too expensive.
