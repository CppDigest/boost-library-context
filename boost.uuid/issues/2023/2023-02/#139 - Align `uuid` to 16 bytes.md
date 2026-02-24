# #139 - Align `uuid` to 16 bytes [Closed]

> Username: Lastique  
> Created at: 2023-02-13 21:08:44 UTC  
> Updated at: 2024-07-04 15:45:14 UTC  
> Closed at: 2024-07-04 15:45:14 UTC  
> Url: https://github.com/boostorg/uuid/issues/139  

I propose adding `BOOST_ALIGNMENT(16)` attribute to the `uuid` type definition.  
  
Aligning `uuid` type to 16 bytes can be beneficial for SIMD algorithms (e.g. `uuid` comparison operators), even on modern CPUs. Accessing vectors that are split across cache line or page boundaries can incur a significant performance penalty. Without aligning the `uuid` type this can happen in non-obvious and difficult to rectify cases. For example, in a `flat_map<uuid, object*>` with 64-bit pointers, every other element of such flat map will have a misaligned `uuid` key, and the lookup function will likely be affected by the aforementioned penalty.  
  
Adding alignment to `uuid` will result in added padding in users' structs where previously UUIDs were unaligned. Where such increase in size is important, it is often easy to work around by reordering data members to place more data in the padding. However, this would be an ABI breaking change, so it should be mentioned in the release notes.

---

## Comment 1

> Username: mclow  
> Created at: 2023-02-13 21:21:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-1428702844  

> However, this would be an ABI breaking change, so it should be mentioned in the release notes  
  
This should absolutely be called out.

---

## Comment 2

> Username: jeking3  
> Created at: 2023-07-07 22:16:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-1626277230  

Would people need a BOOST_UUID_NO_ALIGNMENT opt-out?

---

## Comment 3

> Username: Lastique  
> Created at: 2023-07-08 07:40:47 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-1626898185  

On July 8, 2023 1:16:36 AM Jim King ***@***.***> wrote:  
  
> Would people need a BOOST_UUID_NO_ALIGNMENT opt-out?  
  
I think a macro that affects ABI like that would be quite dangerous, while   
its usefulness is questionable. I would not provide it.

---

## Comment 4

> Username: pdimov  
> Created at: 2024-04-23 10:49:56 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-2071990510  

This sounds like something we would want, but  
  
> Adding alignment to `uuid` will result in added padding in users' structs where previously UUIDs were unaligned.  
  
is an issue, as is having an extended alignment (which has been known to cause problems with e.g. `std::vector`.)  
  
An interesting compromise would be having an alignment of 8, which won't help SSE2 loads, but will help uint64_t (and uint32_t) based access.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-04-23 10:55:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-2071999624  

Actually, the correct alignment wouldn't be a fixed 8, but `alignas(std::uint64_t)` (which would be 4 on 32 bit platforms).  
  
Or maybe `alignas(__uint128_t)` on platforms having `__uint128_t`, although that's going to be the same in practice.

---

## Comment 6

> Username: Lastique  
> Created at: 2024-04-23 12:46:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-2072209668  

The added benefit if 16-byte alignment, besides allowing aligned memory accesses, which is important for compilers making decisions on whether to generate vector instructions, is that an UUID will be guaranteed not to cross a page boundary. Even on CPUs with relatively fast unaligned loads and stores, memory accesses that cross page boundary are extra expensive.  
  
I think, 8-byte alignment is not enough, and the benefits of 16-byte alignment outweigh the cons.  
  
> is an issue, as is having an extended alignment (which has been known to cause problems with e.g. `std::vector`.)  
  
To my knowledge, memory allocators on 64-bit platforms (Windows, Linux, BSDs) align their allocations by 16 bytes minimum. So requiring 16-byte alignment doesn't count as "extended" alignment on those platforms.

---

## Comment 7

> Username: Lastique  
> Created at: 2024-07-04 15:45:14 UTC  
> Url: https://github.com/boostorg/uuid/issues/139#issuecomment-2209262264  

Per https://github.com/boostorg/uuid/pull/148#issuecomment-2209121169.
