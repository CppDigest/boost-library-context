# #76 std::allocator<void> is deprecated in C++17 [Open]

> Username: DanielaE  
> Created at: 2018-01-02 09:53:04 UTC  
> Updated at: 2020-06-12 12:19:27 UTC  
> Url: https://github.com/boostorg/asio/pull/76  

It is used as default argument for allocator template parameters. Replace it by emulating 'void' by an empty struct 'none'. Without that, heaps of deprecation warnings will fall onto humble users when compiling with MSVC 15 in C++17 mode.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: bebuch  
> Created_at: 2018-04-12 20:10:13 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-380929484  

Are you sure? If I understand it correctly, only the explicit specialization is deprecated because it is no longer needed without all these members depending on a complete type. `std::allocator< void >` should continue to work.

---

## Comment 2

> Username: DanielaE  
> Created_at: 2018-04-13 05:50:21 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-381030912  

That's not my understanding of P0174R2 (merged into the IS) and P0636 (synopsis of changes in the IS). At least MSVC flags any use of `std::allocator<void>` (or its types or members) as deprecated (with subsequent consequences to the compilation process) when compiling in C++17 mode.

---

## Comment 3

> Username: bebuch  
> Created_at: 2018-04-16 13:34:40 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-381601125  

I suppose you're right. Thanks!

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-06-11 13:32:35 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-642651925  

@bebuch is actually right here, and MSVC is doing the wrong thing by warning. That the specialization for void doesn't exist does not mean that allocator<void> isn't still a useful proto-allocator (that will be rebound anyway). But supporting MSVC users is important, so workarounds are necessary.

---

## Comment 5

> Username: DanielaE  
> Created_at: 2020-06-11 15:59:55 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-642770522  

It's use as a proto-allocator, and therefore the use of its `rebind` type is deprecated in C++17 [depr.default.allocator]. This alone warrants the deprecation warning. The whole specialization `std::allocator<void>` is removed from C++20 anyway, because it fails to meet the [_Cpp17Allocator_ requirements](http://eel.is/c++draft/requirements#allocator.requirements). Therefore the code wouldn't compile anymore and the question of related deprecation warnings being wrong or right become moot.

---

## Comment 6

> Username: glenfe  
> Created_at: 2020-06-11 18:29:55 UTC  
> Updated_at: 2020-06-11 18:34:26 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-642856787  

No, `std::allocator<void>` is still usable in C++20 even though the specialization is removed from the standard. It is still a valid proto-allocator today.  You can confirm this with other members of the C++ standards committee (such as @jwakely).  In C++11 or above (including C++20) users will use `std::allocator_traits` to rebind the allocator to the correct `value_type` and this works even if the allocator is `std::allocator<void>`.

---

## Comment 7

> Username: glenfe  
> Created_at: 2020-06-11 18:33:21 UTC  
> Updated_at: 2020-06-11 18:52:53 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-642858443  

As an example,  the following program will compile in C++11 up to C++20: https://gcc.godbolt.org/z/YCbPD3  
  
```  
#include <memory>  
  
template<class A>  
struct S {  
    S(const A& a = A())  
        : a_(a) { }  
  
    typename std::allocator_traits<A>::template rebind_alloc<int> a_;  
};  
  
int main()  
{  
    S<std::allocator<void> > s;  
}  
```

---

## Comment 8

> Username: glenfe  
> Created_at: 2020-06-11 18:45:59 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-642864455  

Furthermore, even Microsoft realized that the `allocator<void>` deprecation warning was wrong and it was removed in future versions of MSVC: https://developercommunity.visualstudio.com/content/problem/500588/boost-asio-reports-stdallocator-is-deprecated-in-c.html  
  
> Thanks for filing this issue! Yes, the deprecation warning for `std::allocator<void>` is bogus. C++17 didn't deprecate `allocator<void>`, it deprecated the explicit specialization of `allocator<void>`.

---

## Review 9 [Commented]

> Username: glenfe  
> Created_at: 2020-06-11 18:54:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/76#pullrequestreview-429197657  

📁 include/boost/asio/associated_allocator.hpp

```diff
  26 | namespace detail {
  27 | 
  28 |+ struct none { BOOST_ASIO_CONSTEXPR none() = default; };
```

> Username: glenfe  
> Created_at: 2020-06-11 18:54:27 UTC  
> Url: https://github.com/boostorg/asio/pull/76#discussion_r439002273  

Even if doing this for MSVC2015 users (as I point out the bogus warning was removed in MSVC2017 or later), there's no need to create such a `none` type. Just use `allocator<char>` instead of `allocator<void>`.


---

## Comment 10

> Username: jwakely  
> Created_at: 2020-06-11 19:13:02 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-642877209  

I agree with everything Glen said. The _specialization_ `allocator<void>` was removed, but that doesn't mean you can't use that type, it just means it uses the primary template instead of an explicit specialization.  
  
Unfortunately this had been communicated very poorly by the standard committee and lots of people seem confused.

---

## Comment 11

> Username: DanielaE  
> Created_at: 2020-06-12 07:16:24 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-643110874  

Thanks @jwakely and @glenfe , for elaborating on this subject and the clarification. Much appreciated!  
  
At the time of writing, this was the still belated result of my research in regards to `allocator<void>` and the dire need of our codebase to compile again with the prescribed build rules and the compilers available at that time. Boost.ASIO (or vanilla ASIO) is one the libraries we cannot do without. The changeset above brought us back into business with all of our customer projects from the past ten years and is still part of our in-house Boost distro.  
  
Chris never replied to my emails regarding allocators in vanilla ASIO, the PR got hardly any attention, and admittedly I've completely lost track of this old PR of mine. The C++ world has moved on, and we are slowly but steadily moving away from Boost wherever possible. It is unclear if and when we catch up from 1.68 to recent Boost and audit all of our changes required to make it work with regards to company policies, and rework open PRs from years ago.  
  
Now that I've got your attention: does it make sense at all to further pursue this PR? If I understand correctly, Boost.ASIO is (for the most part) the result of a mechanical transformation from Chris Kohlhof's ASIO sources. This is where I wanted the issue to be discussed and resolved in the first place back in 2017. The PR to the Boost derivative was more like a desperate measure with as little code impact as possible, to be replaced by a better solution originating in vanilla ASIO.

---

## Comment 12

> Username: glenfe  
> Created_at: 2020-06-12 12:19:27 UTC  
> Url: https://github.com/boostorg/asio/pull/76#issuecomment-643241054  

I'm all for having every possible C++ implementation be warning free, even if that means working around implementation defects. That said, as you already know, ASIO is a special case looking at the backlog of pull requests. I wouldn't spend more time on ASIO, beyond just pinging Chris, but if you have any such PRs open on other (especially not-actively-maintained ) Boost libraries. I'll be happy to move them through.

---
