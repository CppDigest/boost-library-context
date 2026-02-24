# #179 Remove <iterator> header from span.hpp [Closed]

> Username: libbooze  
> Created at: 2024-11-25 19:21:50 UTC  
> Updated at: 2024-12-14 12:48:05 UTC  
> Closed at: 2024-12-14 12:48:05 UTC  
> Url: https://github.com/boostorg/core/pull/179  

edit: this description missed use of `std::reverse_iterator`, please see comments bellow  
  
-----------------------------------  
  
  
Due to reasons unrelated to this pull I was looking into how slow/fast to compile `span.hpp` and `<span>` are.   
I have noticed that `<span>` compiles faster and that preprocessed output (`-E` compiler flag) has around 20k lines less.  
It turns our that is mostly due to include of `<iterator>` header. This measurement is limited to just my setup(libstdc++) and results may be different for some other std implementation.  
  
As far as I know `<iterator>` is not needed for span functionality, and tests seem to pass. Note that I am not a boost dev so IDK how to properly test all users of this header, but for example I have tested beast beside running tests in core and all seems fine.  
  
This is not super important change since huge percentage of users will eventually use some part of STL that will drag in `<iterator>` header, but I still believe we should fix this(if nothing is broken by this change) for 2 reasons:  
  
1. There may be a small set of boost users that use only limited subset of boost/std and would appreciate this change. I do not know of any cases like this, but maybe some embedded or driver projects fits this description  
2. I think in general it is nice to IWYU, even if as in this case most programs will still eventually include `<iterator>`

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-11-25 19:52:16 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2498903325  

`<iterator>` is needed for `std::reverse_iterator`.  
  
In this case we can probably get by without that include because `std::array` also uses `std::reverse_iterator` so the declaration of `std::reverse_iterator` must be available after including `<array>`, but that's not strictly guaranteed.  
  
Incidentally, `<array>` is a pretty heavy header in its own right because it often includes `<algorithm>`. It's not clear whether getting rid of `<array>` instead won't be better for compile times.

---

## Comment 2

> Username: libbooze  
> Created_at: 2024-11-25 20:01:31 UTC  
> Updated_at: 2024-11-25 20:15:37 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2498920494  

> `<iterator>` is needed for `std::reverse_iterator`.  
>   
> In this case we can probably get by without that include because `std::array` also uses `std::reverse_iterator` so the declaration of `std::reverse_iterator` must be available after including `<array>`, but that's not strictly guaranteed.  
>   
> Incidentally, `<array>` is a pretty heavy header in its own right because it often includes `<algorithm>`. It's not clear whether getting rid of `<array>` instead won't be better for compile times.  
  
Ah, my mistake, should have checked this better, I noticed typedefs, but I presumed they are all just raw pointers/references.   
As for array: I think `<array>` is needed for this, no?  
```  
template<class T, std::size_t N>  
struct span_is_array<std::array<T, N> > {  
    static constexpr bool value = true;  
};  
```  
As for `<algorithm>` : that is unfortunate, I have no way to check this now for windows quickly, but I checked libc++ and they both seem small, but for msvc I do not know.   
  
> clang++-19 --std=c++26 ... --stdlib=libc++  -E ./test_boost_span.cpp | wc -l  
>   
> 13431  
> clang++-19 --std=c++26 ... -E ./test_boost_span.cpp | wc -l  
>   
> 14943  
  
(libc++ uses some algo headers, but not the full `<algorithm>` one)

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-11-25 20:24:49 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2498962336  

FWIW, my principle is that whatever is directly used in the header must be included in that header. That is, I try to never rely on transitive includes.  
  
If including `<iterator>` is a problem then we could probably reimplement `reverse_iterator`. I don't think there's requirement for `span::reverse_iterator` to be literally `std::reverse_iterator`. Although, this would be already not the first reimplementation (I know of ones in Boost.Iterator and Boost.Move already). So perhaps, there is value in extracting one implementation into Boost.Core.  
  
As for `std::array`, I think a forward declaration of `std::array` should suffice. Boost.Move has facilities for this ([`std_ns_begin.hpp`](https://github.com/boostorg/move/blob/5fe1c7a435a7067916516b0235bf9b607dc8ccad/include/boost/move/detail/std_ns_begin.hpp)/[`std_ns_end.hpp`](https://github.com/boostorg/move/blob/5fe1c7a435a7067916516b0235bf9b607dc8ccad/include/boost/move/detail/std_ns_end.hpp)). Maybe those should be extracted and made public as well. CC @igaztanaga.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-11-26 00:13:21 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2499292044  

I'm not sure why we need the special case for `std::array`; the range overload should work for it. @glenfe?

---

## Comment 5

> Username: Lastique  
> Created_at: 2024-11-26 01:04:22 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2499344338  

I suspect that's because there there are slight differences between constructors from `std::array` and ranges. The constructor from ranges is explicit if the extent is not dynamic while the constructors from `std::array` are not. The constraints on the constructors are also different - the one from `std::array` checks the array size.  
  
BTW, I think, the constructor from ranges when the span extent is not dynamic is poorly defined. Specifically, if the span extent is larger than the range size, the constructor will happily succeed and produce a span that is larger than the original range. I would rather such code just not compile at all.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-11-26 01:23:11 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2499378875  

Ah, right, `std::array` is constant size. I use `std::tuple_size` (available in `<utility>`) to detect it without including `<array>`.

---

## Comment 7

> Username: libbooze  
> Created_at: 2024-11-26 09:13:25 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500069768  

>   
> BTW, I think, the constructor from ranges when the span extent is not dynamic is poorly defined. Specifically, if the span extent is larger than the range size, the constructor will happily succeed and produce a span that is larger than the original range. I would rather such code just not compile at all.  
  
If you are referring to [this](https://godbolt.org/z/8q1E5a6oa) scenario `std::span` also seems to accept this, i.e. this compiles fine and exhibits runtime UB.    
```  
    int main(){  
    [[maybe_unused]]int x = 40, y = 41, z= 42;  
    boost::array<int, 4> arr{1,7,0,1};  
    boost::span<int, 7> sp{arr};  
    fmt::print("{}\n",sp);  
    std::span<int, 7> std_sp{arr};  
    fmt::print("{}\n",sp);  
}  
```

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-11-26 10:58:17 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500302783  

Interesting. This doesn't make much sense to me, given the recent push towards increased safety.

---

## Comment 9

> Username: Lastique  
> Created_at: 2024-11-26 11:02:03 UTC  
> Updated_at: 2024-11-26 11:06:32 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500310689  

> If you are referring to [this](https://godbolt.org/z/8q1E5a6oa) scenario `std::span` also seems to accept this, i.e. this compiles fine and exhibits runtime UB.  
  
Yes, and I consider this a bug in the specification.  
  
And we should probably do better in Boost. I.e. either not offer this constructor (which is easy) or restrict it so that it is only enabled when it's safe (which may be more difficult).

---

## Comment 10

> Username: pdimov  
> Created_at: 2024-11-26 11:06:59 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500321179  

Ah, the above only compiles for `std::span` because it uses `boost::array` and `std::span` doesn't know about `boost::array`. It correctly fails to compile with `std::array` because of https://eel.is/c++draft/span.cons#14.4 .

---

## Comment 11

> Username: Lastique  
> Created_at: 2024-11-26 11:08:03 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500323373  

> Ah, the above only compiles for `std::span` because it uses `boost::array` and `std::span` doesn't know about `boost::array`. It correctly fails to compile with `std::array` because of https://eel.is/c++draft/span.cons#14.4 .  
  
Yes, but it doesn't make the constructor from a range safe.

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-11-26 11:09:30 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500326489  

How could the constructor from a range be made safe? The range size isn't known until runtime.

---

## Comment 13

> Username: Lastique  
> Created_at: 2024-11-26 11:19:37 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500362435  

> How could the constructor from a range be made safe? The range size isn't known until runtime.  
  
One idea is https://eel.is/c++draft/span.cons#15.1 should be made not precondition but a constraint. Which would mean `ranges​::​size(r)` must be a constant expression and the range must be statically sized.  
  
Another idea is to perform a runtime size check and throw if the check fails.  
  
In any case, I think the support for static span size is a design misfeature that adds more problems than benefit. This is one of the reasons why I still prefer `iterator_range` in my code.

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-11-26 11:31:52 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500412021  

`ranges::size(r)` is never a constant expression because function arguments (such as `r`) aren't constant expressions.  
  
As for runtime check, the standard makes this a precondition, which means that it could be checked. This would at least mean a BOOST_ASSERT in our case, but it doesn't look like we have it. https://godbolt.org/z/xqhMWEYox

---

## Comment 15

> Username: Lastique  
> Created_at: 2024-11-26 11:43:23 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500456733  

> `ranges::size(r)` is never a constant expression because function arguments (such as `r`) aren't constant expressions.  
  
Ok, let's paraphrase `ranges::size(r)` as `tuple_size_v< R >` or whatever equivalent for ranges there is. The point is to require that the size of the range is at least as large as that of the span's, and this should be doable.  
  
> As for runtime check, the standard makes this a precondition, which means that it could be checked.  
  
Sorry, this reasoning doesn't work. We have at least one implementation that doesn't check, which means the code cannot rely on the check and therefore is not safe. Historically, preconditions were never enforced by implementations other than in special modes, like debug or even stronger "safe" modes that were not intended for production. IMO, the constructor in question is way too error-prone to rely on the user satisfying the precondition.

---

## Comment 16

> Username: Lastique  
> Created_at: 2024-11-26 11:56:15 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500510908  

BTW, the case of `boost::array` is not unique. You could replace it with `std::vector`, for example, and it would still [compile and exhibit UB](https://godbolt.org/z/eWM3bM1TE).

---

## Comment 17

> Username: libbooze  
> Created_at: 2024-11-26 12:01:44 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500535948  

> Ah, the above only compiles for `std::span` because it uses `boost::array` and `std::span` doesn't know about `boost::array`. It correctly fails to compile with `std::array` because of https://eel.is/c++draft/span.cons#14.4 .  
  
Yes, my mistake for not being explicit. I used `boost::array` since it is a compile time sized range that `std::span` does not know about explicitly(like it does for `std::array`).   
  
I was curious if we could detect that size is fixed since it is available at compile time, but it turns out that since they made vector constexpr this will not work, e.g. [this](https://godbolt.org/z/oEffq4WM8) program compiles  
  
```  
template<class Lambda, int=(Lambda{}(), 0)>  
constexpr bool is_constexpr(Lambda) { return true; }  
constexpr bool is_constexpr(...) { return false; }  
  
// toy example, e.g. size_t is not always size() return type...  
template<typename T>  
constexpr std::optional<size_t> constexpr_size() {  
    return is_constexpr([]{return T{}.size();}) ?   
          std::optional{T{}.size()} : std::nullopt;  
}  
  
int main(){  
    static_assert(constexpr_size<std::vector<int>>() == 0);  
    static_assert(constexpr_size<std::array<int,4>>() == 4);  
    static_assert(constexpr_size<boost::array<int,4>>() == 4);  
    // boost::vector is not constexpr friendly  
    static_assert(constexpr_size<boost::container::vector<int>>() == std::nullopt);  
}  
```  
  
It may be possible to make a "good" concept for detecting fixed size ranges so that `boost::array` matches, `boost::static_vector` does not..., but I am not sure how easy is to do and this safety would require C++20 compiler.   
  
  
  
  
  
> BTW, the case of `boost::array` is not unique. You could replace it with `std::vector`, for example, and it would still [compile and exhibit UB](https://godbolt.org/z/eWM3bM1TE).  
  
Yes, I picked `boost::array` since in theory we know it's size at compile time and can reject programs without any runtime overhead, practically we do can not.

---

## Comment 18

> Username: glenfe  
> Created_at: 2024-11-26 12:10:34 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500581582  

A detail reverse_iterator in span is also an option. It would be unfortunate if the appreciation in libraries assuming C++11 or higher now is offset by them reimplementing even parts of C++03 for these reasons.

---

## Comment 19

> Username: Lastique  
> Created_at: 2024-11-26 13:02:03 UTC  
> Updated_at: 2024-11-26 13:02:39 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500746771  

Come to think of it, we might still need to include `<iterator>` even if we reimplement `reverse_iterator` - for `std::random_access_iterator_tag`. Forward-declaring the tag won't work as it needs to be defined for its base classes. So it looks like reimplementing `reverse_iterator` won't work.

---

## Comment 20

> Username: pdimov  
> Created_at: 2024-11-26 13:53:47 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500864566  

> I was curious if we could detect that size is fixed since it is available at compile time, but it turns out that since they made vector constexpr this will not work  
  
Yes, I managed to discover this twice (because I forgot). I occasionally need such a detection, but I haven't been able to find a way to do this reliably, so I settled on an explicit trait: https://github.com/pdimov/hash2/blob/develop/include/boost/hash2/has_constant_size.hpp

---

## Comment 21

> Username: pdimov  
> Created_at: 2024-11-26 13:58:08 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500874581  

> Historically, preconditions were never enforced by implementations other than in special modes, like debug or even stronger "safe" modes that were not intended for production.  
  
Looks like this is going to change: https://github.com/cplusplus/papers/issues/2125  
  
> IMO, the constructor in question is way too error-prone to rely on the user satisfying the precondition.  
  
So you're for the outright removal of this constructor? I myself have never been particularly fond of statically sized spans and the associated complexity they brought, but on the other hand, not matching the standard exactly almost always creates complaints.  
  
In either case, we are missing at least one BOOST_ASSERT here, aren't we, folks.

---

## Comment 22

> Username: libbooze  
> Created_at: 2024-11-26 14:11:23 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500906441  

> Come to think of it, we might still need to include `<iterator>` even if we reimplement `reverse_iterator` - for `std::random_access_iterator_tag`. Forward-declaring the tag won't work as it needs to be defined for its base classes. So it looks like reimplementing `reverse_iterator` won't work.  
  
Yes, shame, would be nice if C++ decades ago decided to use member presence to detect iterator category, e.g.  
```  
struct MyIterator {  
    using bidi_iter = int; // back then it would had to be typedef  
};  
```  
Also would be nice for boost if `std::` headers were more granular, but then again it would make using C++ for most developers more tedious.   
On my machine `std::span` just uses internal header  
  
> __iterator/iterator_traits.h  
  
In any case I think feasibility of getting rid of `<iterator>` is now close to zero, unless boost core would adopt a policy that it is fine to rely on transitive includes in cases when we are talking about standard headers and their **public** API.  
  
Let me elaborate:  
I see few general approaches when deciding what to `#include` in boost core.   
  
1.  Rely on internal header structure of 3 major std implementations for known versions of std implementation, fallback to "public" headers in other cases. Downsides is that this is painful to implement, test, and since we can not predict the future as soon as users upgrade to new compiler fallback will be activated meaning public header will be used, then we may manually verify in new compiler `iterator_traits.h` still is in the same place, bump up the version check and user gets faster compile when they update boost. To me this looks absolutely terrible and I just mention it for completeness, and in case somebody can see a better way.   
2. Do the proper thing of including matching header whenever you use `std::something`. This is what I always do and encourage in user code, issue is the compile time performance, e.g. recently I learned that `boost::array` includes `<algorithm>`. This is nothing against `boost::array` in particular often in my code I need `std::min` and I include `<algorithm>` and I get 30k extra LOC(and it gets worse with newer standards as more stuff is added).   
> ❯ clang++-19 --std=c++26 -I/home/nx01/src/boost_install/modular-boost -E  ./test_include.cpp  | wc -l  
> 31531  
> ❯ clang++-19 --std=c++17 -I/home/nx01/src/boost_install/modular-boost -E  ./test_include.cpp  | wc -l  
> 17358  
  
`<algorithm>` is just one example IIRC there was a proposal for `<smart_ptr>` header that would not include all of `<memory>` but that went nowhere.  
  
3. Allow relying on transitive includes only in the following scenario:   
  
- we are including standard header, meaning we do not have ability to refactor code to make the small part we need available as standalone .h/.hpp file.   
- transitive include must rely on public API, meaning that if for example some `<array>` header implementation includes `<string>` or `<algorithm>` we should not rely on that since nothing in public API of `std::array` mentions `std::string` and even if it mentioned some algorithm from `<algorithm>` there is no guarantee that entire `<algorithm>` is available. This means the following: type we are avoiding include for(in our case `std::reverse_iterator`) must be explicitly part of public API of some class/function in the header we are relying to include the proper type for us.  
- public API on which we rely on can not work with just forward declaration of types we need  
I think this is reasonable compromise, but reviewing this kind of code becomes a bit harder. E.g. even if it compiles reviewer would need to go and check if it is due to some random include structure of implementation or standard actually put the used type in public API of the other header. Also speculatively I think it may be better for compile performance in cases when user eventually uses the std header we are avoiding to include. Let me elaborate my speculation(I have no benchmarks to confirm this).  If we reimplemented std::X in boost and we instantiate boost::X<int> and later code uses std::X<int> we actually ask compiler to do 2x work of template instantiation.  
  
In [this case](https://en.cppreference.com/w/cpp/container/array) `std::array` seems to explicitly specify `std::reverse_iterator` instead of a concept for member types so this is fine.  
  
Sorry for the long comment that turned into policy suggestion, but I believe it would be nice if boost.core picked an approach(either 2. or 3.) or better one I did not consider.  
  
While this analysis may appear to support merging the pull request, actually I am fine with it being rejected and accepting that `span.hpp` include takes 10 ms more than `<span>` because it is better for code quality, I am just curious what experts think is best.

---

## Comment 23

> Username: pdimov  
> Created_at: 2024-11-26 14:28:31 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500948998  

Any "let's make our headers more granular" standard proposal now gets answered with "modules fix everything".  
  
Let's hope modules do indeed fix everything, because standard headers are getting heavier and heavier with each subsequent standard.  
  
On our side, I always include the standard header the standard says needs to be included and seethe. There are some tricks you can legitimately play, such as including `<typeindex>` to get `std::hash` instead of the horrible `<functional>`, but not much.

---

## Comment 24

> Username: Lastique  
> Created_at: 2024-11-26 14:30:22 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2500953743  

> So you're for the outright removal of this constructor?  
  
I think:  
  
1. The constructor should statically check whether the size of the range is large enough to accommodate the span. If it's not large enough, the constructor should be disabled (preferably, with SFINAE).  
2. If the check is not possible at compile time (e.g. if the range is runtime-sized) then either the constructor should be disabled or a runtime check should be performed. Either way is fine with me, but disablement is slightly more preferred.  
3. Regarding runtime checks, yes, `BOOST_ASSERT` is *a bare minimum* of a check. A similar check must be performed in the constructor from iterators. I'm not entirely sure `BOOST_ASSERT` is *enough* of a check as it is removed in release mode. It might be desirable to keep the check even in release mode.  
  
All of the above is regarding the fixed-size span. The dynamically sized span does not need any size checks, obviously.

---

## Comment 25

> Username: libbooze  
> Created_at: 2024-11-26 14:44:22 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501001821  

> Let's hope modules do indeed fix everything, because standard headers are getting heavier and heavier with each subsequent standard.  
  
  
As far as I know real life results of Office experiment are a bit better(around 15%) than PGO. Not great, not terrible, but definately not just include everything you want and it is still 20x faster than headers.  
  
> So you're for the outright removal of this constructor? I myself have never been particularly fond of statically sized spans and the associated complexity they brought, but on the other hand, not matching the standard exactly almost always creates complaints.  
>   
  
To play defense attorney for static  extent `span`:  
I believe it may be possible for compiler to [optimize better ](https://godbolt.org/z/PvzT8hfT4)if it knows size.   
Note that this is not super realistic example, maybe if we used the `std::span` with dynamic extent based on callsite compiler may be able to figure out the iteration count(even if function is in the library if we use LTO) etc. but I think it is a bit of a hint for motivation why this was standardized(I was not involved so I am just guessing).   
It is not the same thing, but I remember Jason Turner showing similar differences between `std::array` and `std::vector`. Again this was long time ago, and compiler optimizations are really hard to predict in real code.   
  
  
re matching standard:  
As a user I would prefer if boost always matched standard, plus optionally better features, e.g. if what we discussed before was feasible(compile time check that sizes match).  
  
re asserts and runtime checks:  
Asserts are great!   
IMHO runtime overhead of safety checks in release should probably be configurable/opt in.  
In my opinion not with one safety flag, i.e. it should be possible to turn on/of check dereference in `optional` independently from checking for array bounds in `span`. Here balance would need to be made between granularity and ease of use. Some projects might dislike performance impact of all checks, but also having 29 different macros to define different runtime checks may be too much for most users.

---

## Comment 26

> Username: libbooze  
> Created_at: 2024-11-26 14:50:37 UTC  
> Updated_at: 2024-11-26 14:57:54 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501017971  

> 2. If the check is not possible at compile time (e.g. if the range is runtime-sized) then either the constructor should be disabled or a runtime check should be performed. Either way is fine with me, but disablement is slightly more preferred.  
  
If I understood [discussion above](https://github.com/boostorg/core/pull/179#issuecomment-2500864566) correctly issue is that we can not detect types with compile time known size using SFINAE/concepts. We must explicitly list them or they must explicitly proclaim themselves to be of constant size. That may be one of reasons(beside explicit) why `std::span` treats `std::array` differently from other types(i.e. it does not use concepts only for constraints). He knows how to check size of `std::array` at compile time, but that is not feasible for open set of types.   
  
  
edit: with concepts it might work, but not sure how reliable it would be since concepts are syntax, not semantic checks.

---

## Comment 27

> Username: Lastique  
> Created_at: 2024-11-26 14:58:43 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501041805  

> Any "let's make our headers more granular" standard proposal now gets answered with "modules fix everything".  
>   
> Let's hope modules do indeed fix everything, because standard headers are getting heavier and heavier with each subsequent standard.  
  
My hope is to live long enough to see modules ubiquitous. Because it doesn't seem like they will be of much help while half of the code base uses includes and the other half modules, yet this is what we can expect to see in the coming decades.  
  
> On our side, I always include the standard header the standard says needs to be included and seethe. There are some tricks you can legitimately play, such as including `<typeindex>` to get `std::hash` instead of the horrible `<functional>`, but not much.  
  
My approach in public headers is:  
  
1. Don't use std if you can. This is especially true for simple stuff like `min`, `max`, `pair`, etc. In some cases, using Boost components over std is reasonable.  
2. Forward-declare std if possible. This should work for specializations like `hash`.  
3. Include the proper header if you must. Yes, sometimes you can relatively safely rely on transitive includes in the standard library, but in the end it makes maintenance more difficult.

---

## Comment 28

> Username: libbooze  
> Created_at: 2024-11-26 15:07:28 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501065173  

> 1. Don't use std if you can. This is especially true for simple stuff like `min`, `max`, `pair`, etc. In some cases, using Boost components over std is reasonable.  
  
Maybe I should have made it more explicit that my suggestion of option 3. only applies to Boost.Core. As you know documentation says:  
  
> not dependent on any other Boost modules except Core itself, Config, Assert, Static Assert, or Predef.  
  
So even if `std::min` had a small header equivalent in boost(e.g. in Boost.Algorithm) only practical option for Core is to add that header to Core. Nothing terrible about that for commonly used primitives, but at certain point it defeats point of Core if it reimplements huge part of boost/std.

---

## Comment 29

> Username: Lastique  
> Created_at: 2024-11-26 15:18:03 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501110266  

> To play defense attorney for static extent `span`:  
> I believe it may be possible for compiler to [optimize better ](https://godbolt.org/z/PvzT8hfT4)if it knows size.  
  
That's fine, but it doesn't mean the fixed and dynamic-sized span should be the same type.

---

## Comment 30

> Username: libbooze  
> Created_at: 2024-11-26 15:21:51 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501127714  

> That's fine, but it doesn't mean the fixed and dynamic-sized span should be the same type.  
  
What would be benefit of split? Users could be thought that `span` is safer than `fixed_span`? I am not saying that is not good reason, I like when "risky" code stands out, e.g. `reinterpret_cast` but I wonder if I you were talking about this or something else?

---

## Comment 31

> Username: Lastique  
> Created_at: 2024-11-26 15:22:47 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501132034  

> If I understood [discussion above](https://github.com/boostorg/core/pull/179#issuecomment-2500864566) correctly issue is that we can not detect types with compile time known size using SFINAE/concepts.  
  
Well, you could check if the static size greater than 0, which would rule out `std::vector` and `std::string`. And constructing a non-empty fixed size `span` from an empty `vector` would fail because of the size check anyway.  
  
However, I can imagine a container that is non-empty on construction, so maybe there needs to be some distinctive feature of fixed-sized ranges after all.

---

## Comment 32

> Username: Lastique  
> Created_at: 2024-11-26 15:25:01 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501142390  

> Maybe I should have made it more explicit that my suggestion of option 3. only applies to Boost.Core.  
  
That was a more general comment of mine, not limited to Boost.Core or even Boost. Yes, Boost.Core has a rule about dependencies, and it should be taken into account, of course.

---

## Comment 33

> Username: libbooze  
> Created_at: 2024-11-26 15:49:04 UTC  
> Updated_at: 2024-11-26 15:55:57 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501218246  

> Well, you could check if the static size greater than 0, which would rule out `std::vector` and `std::string`. And constructing a non-empty fixed size `span` from an empty `vector` would fail because of the size check anyway.  
>   
> However, I can imagine a container that is non-empty on construction, so maybe there needs to be some distinctive feature of fixed-sized ranges after all.  
  
I think zero sized `std::array` is legal, and it consumed produces "empty" span, e.g. I think this is[ silly, but legal program](https://godbolt.org/z/j45r4h573).   
```  
int main(){  
     std::array<int, 0> empty;  
     static_assert(empty.size() == 0);  
     std::span x{empty};  
     assert(x.size() == 0);  
     assert(x.data() == nullptr);  
}  
```  
This is not a hard block obviously, we can be smarter probably, so I am not saying concept for what we are trying to detect is impossible(e.g. if you force people to call their methods for changing size `push_back` and `resize`), but certainly it does not seem trivial.

---

## Comment 34

> Username: Lastique  
> Created_at: 2024-11-26 16:03:21 UTC  
> Updated_at: 2024-11-26 16:06:21 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501253644  

> > That's fine, but it doesn't mean the fixed and dynamic-sized span should be the same type.  
>   
> What would be benefit of split? Users could be thought that `span` is safer than `fixed_span`? I am not saying that is not good reason, I like when "risky" code stands out, e.g. `reinterpret_cast` but I wonder if I you were talking about this or something else?  
  
The main rationale is separation of complexity and use cases. If you look at `boost::span` implementation, you will notice that probably majority of its implementation is predicated on extent being `dynamic_extent` or not. This is an indication that it's basically two classes crammed into one. You may also notice that the size checks we're discussing are also specific to the fixed-size spans.  
  
The interface complexity of span also leaks onto the users, as if you want to have `span` in your interfaces, you may have to deal with the extent as well. For example, if you're writing a generic algorithm that takes a range on input, do you need to treat the fixed-sized spans differently from dynamic-sized spans and other ranges? Do you want to accept the code size bloat coming from the fixed-sized spans with different extent values? What if you want to return a span - which one do you return? And so on.  
  
And also I would say that fixed-sized spans are more rarely used and the use cases are different from the dynamic sized ones. Similarly to how they are different for `array` and `vector`. Which means that the complexity introduced by the span extent is unnecessary most of the time.

---

## Comment 35

> Username: Lastique  
> Created_at: 2024-11-26 16:04:23 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501256144  

> I think zero sized `std::array` is legal  
  
Ah, right, I forgot about that.

---

## Comment 36

> Username: libbooze  
> Created_at: 2024-11-26 16:08:39 UTC  
> Updated_at: 2024-11-26 16:08:55 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2501266343  

> The main rationale is separation of complexity and use cases. If you look at `boost::span` implementation, you will notice that probably majority of its implementation is predicated on extent being `dynamic_extent` or not. This is an indication that it's basically two classes crammed into one. You may also notice that the size checks we're discussing are also specific to the fixed-size spans.  
>   
> The interface complexity of span also leaks onto the users, as if you want to have `span` in your interfaces, you may have to deal with the extent as well. For example, if you're writing a generic algorithm that takes a range on input, do you need to treat the fixed-sized spans differently from dynamic-sizes spans and other ranges? Do you want to accept the code size bloat coming from the fixed-sized spans with different extent values? What if you want to return a span - which one do you return? And so on.  
>   
> And also I would say that fixed-sized spans are more rarely used and the use cases are different from the dynamic sized ones. Similarly to how they are different for `array` and `vector`. Which means that the complexity introduced by the span extent is unnecessary most of the time.  
  
Not experienced enough to answer about generic algorithm, I presume I would just use `std::ranges::range` concept to constrain type I accept and _hope_ that is good enough.   
But in general I agree, only downside I can think of is that users might not like 2 different class names for related types, but I agree that they are different enough to warrant 2 different names.   
Thank you for the explanation.

---

## Comment 37

> Username: libbooze  
> Created_at: 2024-11-27 15:27:48 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504162069  

Hi @pdimov @Lastique   
I have been experimenting regarding the compile time checks for `boost::span` constructors in case when both `span` and source range have fixed size, let me know if you think this kind of checks may be interesting to add to `span.hpp`.  
Please note that this is just early prototype, but hopefully idea is clear enough you can judge it without me making it feature complete and fully tested.  
  
First part is investigation of what causes constexpr evaluation failures(beside obvious stuff like accessing wrong memory or calling non constexpr function).  
As introduction here is a question: does the following compile?  
```  
constexpr auto fun() {  
    int arr[4]{};  
    std::span<int, 6> sp{arr, arr+std::size(arr)};  
    return sp.size();  
}  
  
int main() {  
    constexpr auto val = fun();  
    return val;  
}  
```  
Answer is that it [depends](https://godbolt.org/z/K3j4dPWjh) on if we use libc++ or libstdc++ since libstdc++ has checks(and they are enabled in constexpr mode always). I think this makes sense and this [old comment ](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=71960#c9)seems to suggest it is not an accident.  
But this gives us some hope. Even if we do not commit any UB that would get compiler to fail compilation (e.g. do `return sp.back()`) with libstdc++ we can get constexpr failures just because we break something that is checked (in this case `4==6`).  
Now this may seem useless because in this example we can just do `return sp.back();` to force invalid memory read but it will be useful later.  
  
Second step we need to do is to do a bit of poking of type from which span is constructed (`R&&` passed to constructor), since we have no reflection we basically can guess that integral constant specifying size of range is one of first 2 template parameters. This is not perfect, but should have a reasonably good hit rate, although certainly it is possible that that information is not encoded as integral constant, could be for example integral field in struct used as NTTP argument, or R itself may not even be a template, e.g. somebody made their own `Vec3` class that stores 3 floats.   
This is not perfect, but again is ok heuristic and seems fine as long as failures do not block valid programs to compile.  
  
Third thing is to specify what we know about `R`. If `R` is passed to `span` constructor it must use contiguous iterators. But also we can check few things about R that might indicate it has fixed size: no methods like `push_back`, `resize`, `insert`, etc.   
But this still does not help us to check if hint for R size we got in step two is correct.   
But what we know is that almost all containers/views(unless they are primitive like `array`) that work on contiguous iterators have certain kind of constructor. In particular it is `(T*, T*)` constructor(pair or pointers). There are others common like `(T*, size_t)`, `array` types are default constructible etc,  but that is the easy part. Tricky part is how we can exploit the `(T*, T*)` constructor to check if the number we got from step 2 is a real size R.   
Assume our Extent is 6 and AssumedRExtent we got from step 2 is 4.   
What we do is the following  
  
1. Check if R can be constructed from (data, data + 4) in constexpr way  
2. Check if R can be constructed from (data, data + 6) in constexpr way  
  
If answers are no, no we learn nothing, as maybe it is just a container that is not constexpr friendly. But what is interesting is the following: if `1.` works in constexpr context, and `2.` does not we have a good hint that `R` can not hold the amount of elements we require(6). Reason why one construction might work and other may not is our friend from step 1. Check fired and failed constexpr evaluation(without breaking the compile).   
So only in that case we would stop the compilation. Seems like very very low false positive rate that will catch real world wrong usages.   
  
Full example is [here](https://godbolt.org/z/PGseMG64n).  
  
As mentioned downsides are that we are relying on check in `R` and that libstdc++ does not seem to implement this. I have no checked with msvc stl as it does not work on godbolt.  
  
But still does not seem like a terrible idea to do checks like this even if only for libstdc++. But to be honest seems like a lot of machinery to add to Boost.Core, so I am happy to hear if you have some suggestions to shrink it down. I have not measured compile times impact, godbolt link is super slow due to `fmt` anyway so I would need to remove that first.

---

## Comment 38

> Username: pdimov  
> Created_at: 2024-11-27 15:46:01 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504205846  

I'm not sure this will buy us much, because it's very uncommon for a fixed size `R` to have a constructor taking two pointers (because this constructor can't be statically checked, the same problem we have.)  
  
The only example that comes to mind is `span<T, N>`, and for it we have a special case anyway.

---

## Comment 39

> Username: Lastique  
> Created_at: 2024-11-27 16:28:29 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504304989  

> we basically can guess that integral constant specifying size of range is one of first 2 template parameters.  
  
I think this is too much of a stretch. Template parameters can have any meaning, we can't be guessing. I don't think this is something we can use as a criteria for detecting fixed-size ranges.  
  
Looks like the best we can do right now is to check static sizes for the ranges we know (`std/boost::span`, `std/boost::array`, core language arrays) and for other types perform a runtime size check.

---

## Comment 40

> Username: libbooze  
> Created_at: 2024-11-27 16:32:53 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504314236  

> I'm not sure this will buy us much, because it's very uncommon for a fixed size `R` to have a constructor taking two pointers (because this constructor can't be statically checked, the same problem we have.)  
>   
Well it also checks `std::array`/ `boost::array` kind of types that are default constructible and give non zero `.size()`. Sure `boost::span` special case handles `std::array` , but `boost::span` does not know about `boost::array` or `some_company::Array`. I only mentioned explicitly 2 pointers constructor since that case seemed much harder for me to test at compile time since default constructing a type and calling `.size()` is not difficult.  
So as I think we discussed yesterday   
this compiles:  
```  
int main() {  
    boost::array<int,2> arr;  
    boost::span<int,4> sp(arr);  
}  
```  
this does not compile  
```  
int main() {  
    std::array<int,2> arr;  
    boost::span<int,4> sp(arr);  
}  
```  
  
> The only example that comes to mind is `span<T, N>`, and for it we have a special case anyway.  
  
Not always true if I read current code correctly. True for `boost::span`, not for `std::span` or any other span type with same semantics. `span.hpp` does not know about `std::span`. You may say it is insane to mix spans so `boost::span` will never be assigned form `std::span`, but in large codebases I would not be surprised to see this, especially assignment from some internal implementation of `array` that is not `std::array`.   
  
Beside other span and array implementations I think this might also catch some wrong usages of `boost::static_vector` although I would need to check if that is currently detected, e.g. assigning to span of size 10 the static_vector whose fixed capacity is 8. This is not a perfect check since that vector still at runtime may only have 2 elements, but it is best we can do at compile time.  
  
Don't get me wrong, I do **not** want `span.hpp` to include `boost::array` header or `boost::static_vector` header, I was just saying that current handling of size mismatches at compile time relies on knowing exactly what source type is(both in `span.hpp` and `<span>` implementations), checks like above move it a bit more into a concept kind of way of checks, although I admit that checks are very very clunky.   
  
In any case if you think this is an overkill I kind of agree, I just wish we could do proper checks without such a complex mechanism needed. At least I learned some new C++ tricks in past few days. :)

---

## Comment 41

> Username: pdimov  
> Created_at: 2024-11-27 16:38:40 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504325968  

We can forward-declare anything from Boost (e.g. boost::array), so we don't need to include any headers; only std:: is a problem.  
  
I think that I also tried `R().size() == R().max_size()` as the condition to check in the general case, but that also didn't work for some reason, which I've now forgotten.

---

## Comment 42

> Username: pdimov  
> Created_at: 2024-11-27 16:46:34 UTC  
> Updated_at: 2024-11-27 16:46:54 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504341700  

Can't we just add a deleted overload that is enabled when `R().size() != extent`?

---

## Comment 43

> Username: pdimov  
> Created_at: 2024-11-27 16:47:57 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504344514  

And maybe another when `R().max_size() < extent`.

---

## Comment 44

> Username: libbooze  
> Created_at: 2024-11-27 16:49:36 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504347790  

> Can't we just add a deleted overload that is enabled when `R().size() != extent`?  
  
Yes, but that will not work for all cases.  
  
As mentioned above that works for `array` like things that default construct to provide `.size()` that is not 0. But `span<int,4>` can not be constructed without tricks I mentioned above(we try to construct it from C array of the size of our extent and see if it works for hints to see if it can handle our size).

---

## Comment 45

> Username: libbooze  
> Created_at: 2024-11-27 16:53:58 UTC  
> Updated_at: 2024-11-27 17:06:41 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504356622  

> We can forward-declare anything from Boost (e.g. boost::array), so we don't need to include any headers; only std:: is a problem.  
  
I would say not `std` only although that is main source. Any "unknown" type hits same problem, e.g. maybe people are still using GSL [span](https://github.com/microsoft/GSL/blob/main/include/gsl/span). I have never used gsl span so maybe it is not great example, point is that there is probably a lot of types floating around.   
  
> And maybe another when `R().max_size() < extent`.  
  
Great suggestion, did not think of that since most of time it is some huge number(e.g. for `std::vector`), but for fixed size/capacity containers it is actually super useful in this case.

---

## Comment 46

> Username: Lastique  
> Created_at: 2024-11-27 17:12:08 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504394899  

> Can't we just add a deleted overload that is enabled when `R().size() != extent`?  
  
This will be problematic if `R` does not support a `constexpr` default constructor. Which may be dependent on the element type, so e.g. `std::array< std::function< void() >, 10 >` won't work. I don't think we can use `std::declval` in this case as a workaround.  
  
I suppose, for types that don't pass this check, for one reason or another, we would just do a runtime size check. So, the overload can be considered as just an optimization to eliminate the runtime overhead.

---

## Comment 47

> Username: libbooze  
> Created_at: 2024-11-27 17:19:11 UTC  
> Updated_at: 2024-11-27 17:22:18 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504408121  

> I don't think we can use `std::declval` in this case as a workaround.  
  
True in my experiments, that is first thing I tried, then I felt silly because obviously you  can not call methods on "invented" instance, you need to really construct the object.   
As for nonconstructible element: in my example link I used `std::thread` element type and in that case we just say we do not know what to do(so we would allow construction).   
Also there are no traits like `std::is_constexpr_constructible_v`  so we need to manually check that.

---

## Comment 48

> Username: pdimov  
> Created_at: 2024-11-27 17:33:29 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504433293  

For types that don't pass the check, the overload will be dropped, and the "normal" one will be selected.  
  
`r.size()` will be constant expression when https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2280r4.html is implemented, which is GCC 14+ and Clang none so far. But if we have C++23, `boost::span` isn't going to be particularly relevant.

---

## Comment 49

> Username: pdimov  
> Created_at: 2024-11-27 17:51:37 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504469439  

Something like this: https://godbolt.org/z/YGnj8GW1E

---

## Comment 50

> Username: libbooze  
> Created_at: 2024-11-27 18:17:59 UTC  
> Updated_at: 2024-11-27 18:25:02 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504535299  

> For types that don't pass the check, the overload will be dropped, and the "normal" one will be selected.  
>   
> `r.size()` will be constant expression when https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2280r4.html is implemented, which is GCC 14+ and Clang none so far. But if we have C++23, `boost::span` isn't going to be particularly relevant.  
  
Not sure that will help, as paper mentions static member function. Sure `boost::array::size()` or `std::span<int, 4>::size()` philosophically  speaking could be static but they are not.   
Or you meant something else?  
  
  
> Something like this: https://godbolt.org/z/YGnj8GW1E  
  
This looks very nice! It does not solve the problem of `span`  but for sure it is great improvement. But does it not also have problem we discussed [yesterday](https://github.com/boostorg/core/pull/179#issuecomment-2500864566), e.g. that `std::vector` is constructible and gives 0 `size()`?  
e.g. [this should compile](https://godbolt.org/z/asPoxMaGn) but does not when I bump `--std` to version where `std::vector` is constexprified.   
```  
void f6()  
{  
    std::vector<int> v(1);  
    span<int, 4> sp(v);  
}  
```  
In my example I just say if result of `size() == 0` it is as if I failed to get the size info, I presume same logic could be done here?

---

## Comment 51

> Username: pdimov  
> Created_at: 2024-11-27 18:35:31 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504561853  

> Not sure that will help, as paper mentions static member function.  
  
No, the function doesn't have to be static. See https://godbolt.org/z/r7or63G8n. This only works with GCC 14.

---

## Comment 52

> Username: pdimov  
> Created_at: 2024-11-27 18:38:07 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504565499  

> But does it not also have problem we discussed [yesterday](https://github.com/boostorg/core/pull/179#issuecomment-2500864566), e.g. that `std::vector` is constructible and gives 0 `size()`?  
  
It does, yes. I suppose we need the `!= 0` check here.

---

## Comment 53

> Username: libbooze  
> Created_at: 2024-11-27 18:51:10 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504584727  

> It does, yes. I suppose we need the `!= 0` check here.  
  
If you want I can make the pull request. It will take a lot more time than if you did it, but I would be quite happy to do it. Will make sure to add tests, not sure how to test compile failures, probably there are some examples I can copy from.

---

## Comment 54

> Username: pdimov  
> Created_at: 2024-11-27 18:55:02 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504589999  

@glenfe is the maintainer of `boost::span`, so it's up to him whether to accept something like this or not. (If not, you shouldn't waste time on the pull request.)

---

## Comment 55

> Username: pdimov  
> Created_at: 2024-11-27 19:45:49 UTC  
> Updated_at: 2024-11-27 19:46:03 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504662282  

On a not entirely unrelated note,  
```  
clang-linux.compile.c++ bin.v2/libs/core/test/fclose_deleter_test.test/clang-linux-19/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/fclose_deleter_test.o  
In file included from libs/core/test/fclose_deleter_test.cpp:19:  
In file included from ./boost/move/unique_ptr.hpp:27:  
./boost/move/adl_move_swap.hpp:190:15: error: no member named 'swap' in namespace 'std'  
  190 |    using std::swap;  
      |          ~~~~~^  
1 error generated.  
  
    "clang++-19"   -std=c++11 -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -Wextra -Werror -g -fvisibility=hidden -m64 --target=x86_64-pc-linux -stdlib=libc++ -DBOOST_ALL_NO_LIB=1   -I"." -I"/__w/core/core/boost-root/libs/assert/include" -I"/__w/core/core/boost-root/libs/core/include" -I"/__w/core/core/boost-root/libs/move/include" -I"/__w/core/core/boost-root/libs/smart_ptr/include" -I"/__w/core/core/boost-root/libs/throw_exception/include" -I"/__w/core/core/boost-root/libs/type_traits/include"  -c -o "bin.v2/libs/core/test/fclose_deleter_test.test/clang-linux-19/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/fclose_deleter_test.o" "libs/core/test/fclose_deleter_test.cpp"  
  
...failed clang-linux.compile.c++ bin.v2/libs/core/test/fclose_deleter_test.test/clang-linux-19/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/fclose_deleter_test.o...  
```  
the logic in Boost.Move that tries to play games to get `std::swap` without including the correct header fails with libc++-19. :-)

---

## Comment 56

> Username: igaztanaga  
> Created_at: 2024-11-27 19:53:32 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504672650  

Looks like now lives in https://github.com/llvm/llvm-project/commits/main/libcxx/include/__utility/swap.h

---

## Comment 57

> Username: igaztanaga  
> Created_at: 2024-11-27 20:31:23 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504725180  

See https://github.com/boostorg/move/commit/b7a04d320104dff1f0ed335addadbb65e9a08797 for a fix. Let me know if we should merge this into master.

---

## Comment 58

> Username: Lastique  
> Created_at: 2024-11-27 20:52:38 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504751834  

> See [boostorg/move@b7a04d3](https://github.com/boostorg/move/commit/b7a04d320104dff1f0ed335addadbb65e9a08797) for a fix. Let me know if we should merge this into master.  
  
I think it's worth merging, provided that the tests pass.

---

## Comment 59

> Username: igaztanaga  
> Created_at: 2024-11-27 21:06:45 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2504769676  

Regression tests ok: https://github.com/boostorg/move/actions/runs/12057774672/job/33623124544, asking for permission to merge to release managers.

---

## Comment 60

> Username: libbooze  
> Created_at: 2024-12-02 15:10:02 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2511798423  

> Something like this: https://godbolt.org/z/YGnj8GW1E  
  
Hi @pdimov  I am not really familiar with C++11 TMP, so I wonder if you know a better way to `static_assert` this (instead of user getting ambiguous error).  
Best I could think of is 2 step check:  
  
1. `enable_if` to check if type has `.size()` member fn(maybe only invocable at runtime)  
2. use dummy argument conversion to detect if `.size()` is available at compile time  
  
[Example](https://godbolt.org/z/daPWPaqWs)

---

## Comment 61

> Username: libbooze  
> Created_at: 2024-12-03 15:29:03 UTC  
> Url: https://github.com/boostorg/core/pull/179#issuecomment-2514885116  

@glenfe since you are maintainer of span:  
do you think it is worthwhile to add check @pdimov suggested?  
To recap:  
This is unrelated to this pull request.  
For `(R&&)` constructor we will for `R` types that are default constructible at compile time and have `.size()` and/or `.max_size()`  that is callable at compile time check that results are compatible with extent of span(if it is not dynamic). This will not catch all bad uses, but will catch some.  
Solution will work in C++11, so no need to bump requirements on users wrt language version.

---
