# #96 - std::data() and std::span support [Closed]

> Username: BurningEnlightenment  
> Created at: 2019-03-31 13:25:31 UTC  
> Updated at: 2025-09-04 00:44:28 UTC  
> Closed at: 2025-09-04 00:44:28 UTC  
> Url: https://github.com/boostorg/uuid/issues/96  

C++17 introduced [`std::data()`](https://en.cppreference.com/w/cpp/iterator/data) as a means to access the underlying memory block of contiguous container like entities in a _generic_ way. Furthermore C++20 will introduce [`std::span`](https://en.cppreference.com/w/cpp/container/span) which has a converting template [constructor](https://en.cppreference.com/w/cpp/container/span/span) requiring `std::data(cont)` to be well formed (side note: we do satisfy the other requirements already). This would let me (and probably others) remove some special handling of uuid when doing IO.  
  
Supporting it is as easy as adding the following two public member function to the `uuid` class:  
```cpp  
BOOST_CONSTEXPR value_type* data() BOOST_NOEXCEPT { return data; }  
BOOST_CONSTEXPR value_type const* data() const BOOST_NOEXCEPT { return data; }  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2019-04-18 20:17:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-484674099  

Sounds reasonable to me.

---

## Comment 2

> Username: mclow  
> Created at: 2019-04-25 12:56:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-486660694  

I have no problem with a const version, but giving non-const access to the underlying data seems like it would open up for trouble. On the other hand, `uuid` provides non-const iterators, so I guess that's more or less the same.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-25 13:28:48 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-486672674  

As a data point `std::uuid` proposal (see [here](https://github.com/mariusbancila/stduuid) and [here](https://mariusbancila.ro/blog/2018/02/27/stduuid-a-cpp-library-for-universally-unique-identifiers/)) is moving towards making UUID binary representation opaque. This is a good change compared to Boost.UUID as it offers more room for optimization. Granted, this proposal is not accepted yet (to my knowledge), but it might still be beneficial to use that knowledge.  
  
Although `boost::uuid` provides iterators, they are not the problem. They are pointers now, but they could potentially be something else, which would hide the underlying representation. The real problem is that `boost::uuid::data` is public. I suppose, you could argue that supporting `std::data` and `std::span` don't make things worse, but I still think that it would be better for Boost.UUID to move towards concealing its internal representation. Maybe deprecate public `boost::uuid::data` at some point and make it private after the deprecation period. `std::data` and `std::span` would make that effort impossible, unless you give literally no guarantees about the contents they return, which would not be useful.  
  
So, in short, I'm not a Boost.UUID maintainer, but I would prefer Boost.UUID evolve in a different direction, which would make `std::data` and `std::span` not possible or practical.

---

## Comment 4

> Username: BurningEnlightenment  
> Created at: 2019-04-25 13:58:52 UTC  
> Updated at: 2019-04-25 13:59:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-486684390  

@Lastique at least the current stduuid implementation offers  a [`as_bytes() -> gsl::span<>`](https://github.com/mariusbancila/stduuid/blob/master/include/uuid.h#L365) method which exposes the internal representation. However, what optimizations will be enabled by an opaque representation?  
  
> Maybe deprecate public `boost::uuid::data` at some point  
  
actually this would be also required for `std::data` because of the name clash.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-04-25 15:03:06 UTC  
> Updated at: 2019-05-15 15:22:35 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-486711877  

> at least the current stduuid implementation offers a `as_bytes() -> gsl::span<>` method which exposes the internal representation.  
  
I think, that was discussed in std-proposals list. I'm hesitant to speak for the participants of that discussion, but I got the impression that hiding internal representation was considered the right way to go, which would make `as_bytes` impossible in the current form.  
  
> However, what optimizations will be enabled by an opaque representation?  
  
Most importantly, it will remove the need to deal with endianness. For example, if you look at the [`operator<`](https://github.com/boostorg/uuid/blob/be5876a0fc9c2f7ad92eb95ba12e62d381e9068e/include/boost/uuid/detail/uuid_x86.ipp#L98) implementation, it could have been more efficient if native endianness was allowed. Potentially, it would simplify conversions between `uuid` and strings when optimizing with SIMD. It will also allow to use types other than bytes for storage (e.g. natively supported larger integer types) and enforce alignment of the storage for better performance.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-04-22 15:30:18 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2069903104  

I don't think we'll ever be able to offer `data()` because we already have a public member `data` and there's no clean upgrade path from here to there.

---

## Comment 7

> Username: Lastique  
> Created at: 2024-04-22 15:41:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2069945181  

We could deprecate the public `data` member and eventually make it private and rename. Users are able to access the UUID bytes with iterators.  
  
One downside of this transition is that `boost::uuid` will no longer be POD, but it will still be a trivial type. Not much of a loss, IMO, considering that other desired changes would also require it (e.g. make the default constructor zero initialize the UUID rather than leaving it uninitialized).

---

## Comment 8

> Username: pdimov  
> Created at: 2024-04-22 15:51:50 UTC  
> Updated at: 2024-04-23 10:28:27 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2069985669  

Changing existing uses of `data` to use iterators wouldn't be a mechanical replacement and will be error-prone. It's better to provide a replacement that works as-is.  
  
I suppose we could add  
```  
using octets_type = uint8_t[16];  
  
octets_type& octets() noexcept;  
octets_type const& octets() const noexcept;  
```  
and then `u.data` can be mechanically replaced with `u.octets()`, minimizing the potential of surprises.

---

## Comment 9

> Username: pdimov  
> Created at: 2024-04-23 10:28:11 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2071955158  

> Users are able to access the UUID bytes with iterators.  
  
I'm not sure if we want to encourage that any further; P0959 (correctly IMO) points out that `uuid` is a value, rather than a container, so iterator-based access is arguably a mistake.  
  
`as_bytes` seems like the correct interface here, but unfortunately, `std::span` is C++20.  
  
Although if we move away from an iterator interface, we shouldn't need `data()` either. :-)  
  
> Most importantly, it will remove the need to deal with endianness.  
  
No, I don't think this would be wise. UUIDs are well defined portable entities with standard (also portable) string representations, so the operations depending on the native endianness would be horrible design (and isn't really required, as platforms generally have `bswap` and `movbe`/`movle` instructions nowadays.)

---

## Comment 10

> Username: Lastique  
> Created at: 2024-04-23 12:59:04 UTC  
> Updated at: 2024-04-23 12:59:27 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072234002  

Well, even if UUID is considered as a distinct object, the APIs where it is interpreted as an array of bytes are not uncommon. `boost::uuid` should provide a way to access UUID bytes, if not through iterators then through some other API. `data()` is a sensible API for that. I mentioned iterators earlier because that's what we provide now, and those could serve as a stopgap while we transition to `data()`.  
  
> the operations depending on the native endianness would be horrible design (and isn't really required, as platforms generally have `bswap` and `movbe`/`movle` instructions nowadays.)  
  
One example of such operation that was mentioned is `operator<`, which could be more efficient with native-endian representation. `bswap` and `movbe`/`movle` don't help as they don't support vectors. (There is `pshufb` that could be used to swap bytes within a vector, but (a) it would require an extra vector constant and a memory access, which is why I didn't use it in the current implementation, and (b) I don't think architectures other than x86 have an efficient equivalent, although I may be missing it.)

---

## Comment 11

> Username: pdimov  
> Created at: 2024-04-23 13:14:47 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072296492  

Yes I know that `operator<` can be made more efficient on x86 at the expense of yielding an unspecified result. I just don't think it should be. `operator<` should give the portable, documented lexical order.  
  
An efficient `less` operation, if present, should be provided under a different name, although I'm unsure of its utility.

---

## Comment 12

> Username: pdimov  
> Created at: 2024-04-23 13:22:00 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072311934  

> (b) I don't think architectures other than x86 have an efficient equivalent  
  
I know next to nothing about ARM, but from a cursory look, one should be able to synthetize a byteswap for __uint128_t in a vector register with a sequence of `vrev` and `vtrn`.

---

## Comment 13

> Username: pdimov  
> Created at: 2024-04-23 13:27:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072324319  

Although I doubt that the result will be more efficient than the scalar code (https://godbolt.org/z/5KoPPoP8n).

---

## Comment 14

> Username: Lastique  
> Created at: 2024-04-23 13:39:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072350166  

> Yes I know that `operator<` can be made more efficient on x86 at the expense of yielding an unspecified result. I just don't think it should be. `operator<` should give the portable, documented lexical order.  
  
No, my point was that the result could be portable _and_ the `operator<` could be efficient as well, if the representation was allowed to be platform-specific. And it doesn't preclude us from exposing a portable, big-endian byte sequence as well - whether through iterators or something else.

---

## Comment 15

> Username: pdimov  
> Created at: 2024-04-23 13:44:25 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072361516  

I suppose you could store the bytes in little-endian order, yes, which would make `data()` / `octets()` / `as_bytes()` access impossible and make the iterators `reverse_iterator<uint8_t*>`.  
  
That's an interesting option, but I'm not sure whether it's worth it because it penalizes everything else in order to benefit `operator<`.

---

## Comment 16

> Username: Lastique  
> Created at: 2024-04-23 13:45:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072363547  

Although, I admit, `data()` doesn't fit as that API for accessing portable big-endian byte sequence.

---

## Comment 17

> Username: Lastique  
> Created at: 2024-04-23 13:48:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072371280  

> I suppose you could store the bytes in little-endian order, yes, which would make `data()` / `octets()` / `as_bytes()` access impossible and make the iterators `reverse_iterator<uint8_t*>`.  
>   
> That's an interesting option, but I'm not sure whether it's worth it because it penalizes everything else in order to benefit `operator<`.  
  
Yes.  
  
In my experience, I use UUIDs as keys quite often, and serialize them to bytes less often, so the tradeoff makes sense to me. And even then, where I serialize UUIDs I know I will be reading them on the same kind of machine, so I wouldn't even need the big-endian byte sequence, platform-specific representation would be fine. But this last part is specific to my use case, I suppose.

---

## Comment 18

> Username: Lastique  
> Created at: 2024-04-23 14:02:51 UTC  
> Updated at: 2024-04-23 14:05:42 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072403231  

I think, we could have a member function like `std::array<std::uint8_t, 16u> portable_bytes() const noexcept` that would return the big-endian byte sequence. Generating this array could be optimized as well - it's either byte swap instructions or simple copy of the internal representation.  
  
We could also have a similar initializing constructor doing the reverse.

---

## Comment 19

> Username: pdimov  
> Created at: 2024-04-23 14:15:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072434302  

> In my experience, I use UUIDs as keys quite often, and serialize them to bytes less often, so the tradeoff makes sense to me.  
  
That's true for most people, but today, no sane person should/would use `std::set<uuid>` when `boost::unordered_flat_set<uuid>` exists. The performance difference would surely be enormous.

---

## Comment 20

> Username: pdimov  
> Created at: 2024-04-23 14:45:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072534951  

It occurs to me that another advantage of having opaque representation and value-based accessors would be to enable better `constexpr` support, as `reinterpret_cast` is not allowed there so one needs to play games with `is_constant_evaluated` in the implementations of the comparisons.

---

## Comment 21

> Username: pdimov  
> Created at: 2024-04-23 15:00:41 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2072598492  

Something else I noticed - having `data` be `uint8_t[16]` causes MSVC to activate stack protection when there are local `uuid` variables, which occasionally penalizes simple functions (e.g. the straightforward implementation of `is_nil` as `return *this == uuid{};`.

---

## Comment 22

> Username: BurningEnlightenment  
> Created at: 2024-04-25 09:26:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2076754836  

> Something else I noticed - having `data` be `uint8_t[16]` causes MSVC to activate stack protection when there are local `uuid` variables.  
  
IIRC stack protection generally activates for POD types (as defined by MS ABI) larger than 8 bytes, i.e. `uuid` would be affected regardless of internal layout. Am I misremembering something?  
  
In retrospect, I see this as a misguided feature request on my part. I've come to the conclusion that accessing the internal representation for small-ish value types offers very little added value compared to explicit serialization functions.  
  
> I think, we could have a member function like `std::array<std::uint8_t, 16u> portable_bytes() const noexcept` that would return the big-endian byte sequence.   
  
I suppose a complementary `void serialize_to(std::span<std::byte, 16U>) const noexcept` would be appreciated by people trying to serialize `uuid`s  which are part of larger structures.

---

## Comment 23

> Username: pdimov  
> Created at: 2024-04-25 11:10:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2076934929  

No, stack protection is only activated by arrays on the stack, not POD types.  
  
I think that having `uint8_t const* data() const noexcept` is still preferable to having a public member `data`, so we need to figure out a way to get there, somehow. (Especially if we keep iterators, since the elements are at present contiguous, it's much better to have `data()` than not so that generic code can take the contiguous path.)  
  
The alternative is I suppose removing both the public `data` member _and_ iterator access, but this will be too disruptive to existing users.  
  
One option is to have a variable `data` of a "magic" type that has both a conversion to `uint8_t (&)[16]` and `operator()`, so that `data()` can be used in a backward-compatible way. I'm going to try that out one of these days.

---

## Comment 24

> Username: BurningEnlightenment  
> Created at: 2024-04-25 12:18:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077048128  

> No, stack protection is only activated by arrays on the stack, not POD types.  
  
In the specific case of `is_nil` it seems to me that the stack protection correlates with the usage of SSE intrinsics in `operator==`: https://godbolt.org/z/EoW715M8M  
  
So I went to the MSVC docs and they state (s. [learn.microsoft.com](https://learn.microsoft.com/en-us/cpp/build/reference/gs-buffer-security-check?view=msvc-170#gs-buffers)):  
> A buffer overrun security check is performed on a GS buffer. A GS buffer can be one of these:  
> * [...]  
> * A data structure whose size is more than 8 bytes and contains no pointers.  
  
> I think that having `uint8_t const* data() const noexcept` is still preferable to having a public member `data`.  
  
To clarify my previous statement: I do agree with this part. However, I think the alternative (remove container interface) would be cleaner if a deprecation and upgrade path over multiple releases was feasible. OTOH I do appreciate backwards compatibility :/

---

## Comment 25

> Username: pdimov  
> Created at: 2024-04-25 12:40:02 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077086860  

I think the code I was trying was https://godbolt.org/z/nbjGKvrMW, and while "msvc x64 latest" does manage to optimize out the security cookie here, "x86 latest" (https://godbolt.org/z/xE7Y3nPG8) and "x64 19.29" (https://godbolt.org/z/oMT8qd6T1) do not.  
  
I suppose we shouldn't worry too much about that, then.  
  
(SSE2 doesn't seem to cause any trouble either: https://godbolt.org/z/P4nsz5Gns, https://godbolt.org/z/Pvbsdc6GP.)  
  
After giving this a bit of thought, it seems to me that what we need to be driving towards is something like  
  
```  
struct uuid  
{  
    using octets_type = std::array<std::uint8_t, 16>;  
  
    static uuid from_octets( octets_type const& ); // or maybe by value, rsi:rdi  
    static uuid from_uint64( std::uint64_t high, std::uint64_t low );  
    static uuid from_uint128( __uint128_t ); // if available  
  
    octets_type as_octets() const noexcept;  
    std::pair<std::uint64_t, std::uint64_t> as_uint64() const noexcept;  
    __uint128_t as_uint128() const noexcept; // if available  
};  
```  
  
We'll annoy the people who want to poke directly into the octets (`u.data[3] |= 0xF0;` or something), but I don't think this is much of a loss.  
  
However, one disadvantage of the above is that you won't be able to serialize an array of `uuid` with a single call, which is going to be a significant performance hit _provided that anyone needs to do that_, which I'm not sure about.

---

## Comment 26

> Username: pdimov  
> Created at: 2024-04-25 12:45:10 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077096080  

One example of the above optimization that comes to mind is [N3980](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3980.html) which defines a function `hash_append` that feeds a byte representation of the object being hashed to a hash algorithm. In `uuid`'s case, it can directly feed the entire object to `hash_append`, and if it's marked as (or inferred to be) "contiguously hashable", an array of `uuid` will automatically activate the optimization and be processed in a single `hash_append` call.

---

## Comment 27

> Username: Lastique  
> Created at: 2024-04-25 13:07:01 UTC  
> Updated at: 2024-04-25 13:07:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077138784  

I do not think `from_octets(octets_type const&)` is a good interface. This method should accept a pair of pointers/iterators or `iterator_range` instead of an array so that the user doesn't have to do the extra copy when he deserializes an UUID from a larger stream of bytes. And the `from_*` methods should be explicit constructors instead, I think. You normally add these named factory methods when there are multiple constructors that are ambiguous, which is not the case here.  
  
I also don't think constructing or producing 64/128-bit integers is good, as an UUID is not an integer or pair of integers. I think, such operations would be too confusing. (Also, `std::pair` is a poor choice anyway, as it necessitates `<utility>` and provides poor `first`/`second` names for its members.)

---

## Comment 28

> Username: pdimov  
> Created at: 2024-04-25 13:28:48 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077183702  

The ability to construct a thing from other things does not imply that the first thing is the same as the other things. For example, you propose `explicit uuid( uint8_t const* first, uint8_t const* last )` (which I don't quite like because UB when `last-first != 16`), but `uuid` is not a pair of pointers.  
  
first/second is not an issue because the intended use is `auto [high, low] = u.as_uint64();`.

---

## Comment 29

> Username: pdimov  
> Created at: 2024-04-25 13:42:43 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077227142  

Interestingly, that's probably the reason for my intuitive preference for `from_` functions over constructors - in addition to being symmetric with the `as_` functions, they also make it clear that `uuid` is not one of those things, it can just be created from them.

---

## Comment 30

> Username: BurningEnlightenment  
> Created at: 2024-04-25 13:51:25 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077246446  

Another thought: There should be an explicit warning about `std::bit_cast` if the internal representation diverges from the canonical one.  
  
> (which I don't quite like because UB when last-first != 16), but uuid is not a pair of pointers.  
  
C++20 users would probably appreciate an `explicit uuid(std::span<std::uint8_t, 16> octets)` regardless of the overloads available for earlier language revisions.

---

## Comment 31

> Username: Lastique  
> Created at: 2024-04-25 13:55:45 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077257226  

> The ability to construct a thing from other things does not imply that the first thing is the same as the other things. For example, you propose `explicit uuid( uint8_t const* first, uint8_t const* last )`, but `uuid` is not a pair of pointers.  
  
UUID is a sequence of bytes, and the constructor would reflect this pretty well. It's the next best thing we have after `std::span` and `iterator_range`.  
  
> first/second is not an issue because the intended use is `auto [high, low] = u.as_uint64();`.  
  
This use case will also work if you return a `struct { uint64_t high, low; };` but it will provide better names if one wants to use the struct directly and removes the need for `<utility>`.  
  
> C++20 users would probably appreciate an `explicit uuid(std::span<std::uint8_t, 16> octets)`  
  
Yes, an overload with `std::span` would be desirable. But the same effect should be achievable with older C++ versions, hence my request for a pair of iterators or `iterator_range`.

---

## Comment 32

> Username: pdimov  
> Created at: 2024-04-25 14:02:45 UTC  
> Updated at: 2024-04-25 14:02:57 UTC  
> Url: https://github.com/boostorg/uuid/issues/96#issuecomment-2077275830  

As far as standard headers go, `<utility>` is very cheap and avoiding its inclusion is generally pointless. But we could define our own struct, of course.
