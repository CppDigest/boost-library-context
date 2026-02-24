# #248 - Implementation headers should be in src/ [Open]

> Username: anarthal  
> Created at: 2024-12-30 17:10:06 UTC  
> Updated at: 2025-01-11 10:53:25 UTC  
> Url: https://github.com/boostorg/charconv/issues/248  

Some headers in `detail` seem to only be used in the library implementation (i.e. the `.cpp` files). I think these should be under `src/` to avoid being distributed when installed, even if they are used in the tests. I've found this out while trying to add C++20 module support to the library, where the mental model for headers in the interface unit and the implementation units is different.  
  
Would you be open to a PR applying the changes I suggest?

---

## Comment 1

> Username: mborland  
> Created at: 2025-01-02 14:03:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/248#issuecomment-2567823364  

Theoretically, almost the entire library could be moved because the only constexpr part is integers which is trivial in comparison to the floating point part. I'm averse to that because then we lose all component testing. Alex just filed a few issues against the parser over Christmas, and I know for his applications he is using the parser directly to get around limitations in the API mandated by the C++ standard. Is there a reason for modules with this library? It seems like if you're using C++20 you could just use `<charconv>` directly.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-01-02 16:37:44 UTC  
> Url: https://github.com/boostorg/charconv/issues/248#issuecomment-2568054304  

I think you can still test as you're doing right now. You can include headers in src/, as you're already doing in some tests atm. So it's just a change in location - no loss in terms of tests.  
  
I know it sounds weird modularizing charconv, but  
- It's the easiest (smallest and with fewer dependencies) compiled library in Boost that I know, so it's good as a proof of concept   
- It's a dependency of libraries that I'd like to ultimately modularize, like MySQL (although we're very far away from that)

---

## Comment 3

> Username: mborland  
> Created at: 2025-01-03 18:28:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/248#issuecomment-2569647707  

As long as @Flamefire can still access the parser in the current detail folder give it a whirl and see how it goes. If need be I think you can easily chop the remaining dependencies out of this library.

---

## Comment 4

> Username: Flamefire  
> Created at: 2025-01-03 19:26:04 UTC  
> Url: https://github.com/boostorg/charconv/issues/248#issuecomment-2569711124  

I don't think you can put that specific header into `src/` anyway, can you?  
  
For context: I need to parse an integer represented in exponential format for which @mborland provided [this example](https://gist.github.com/mborland/774c4cf518d693b105469e1f500b1bbd) using the  `detail::parser`  
  
However I discovered some restrictions:  
- I need to have exact integers, no [rounding](https://github.com/boostorg/charconv/issues/244), no overflow, no fractional (e.g. negative exponents don't make sense in this case).   
- The format is actually "E notation" which is a subset of the exponential notation ("E" as the char and the dot, if any, on 2nd position)  
- I can assume the input is minimal, no leading or trailing zeros  
  
So I implemented a function that converts this notation into a regular integer format and passes that string to `from_chars`: https://github.com/boostorg/locale/blob/c166035c4e4556153e56b48c7a3c5175814fe1ea/src/boost/locale/util/numeric_conversion.hpp#L39  
  
It is inspired by the parser code but a bit simpler.     
It still turned out to be more complex than anticipated. At least it gives me full control over the accepted format while leaving the actual parsing and range checks to `from_chars`.  
  
However me using this in another Boost library provides some good test coverage, see my previous PRs. So I might switch back if this sounds beneficial. But I feel like my use case is too special for the intended use of that interface which seems to be mainly used for the `hex` format. So maintaining it in a way that is "generic enough" might not be worth the effort.  
  
What do you think?

---

## Comment 5

> Username: anarthal  
> Created at: 2025-01-10 16:52:53 UTC  
> Url: https://github.com/boostorg/charconv/issues/248#issuecomment-2583254113  

According to what I've seen, the following headers can be placed in `src/`:  
  
* boost/charconv/detail/compute_float80.hpp  
* boost/charconv/detail/parser.hpp  
* boost/charconv/detail/fallback_routines.hpp  
* boost/charconv/detail/bit_layouts.hpp  
* boost/charconv/detail/significand_tables.hpp  
* boost/charconv/detail/compute_float64.hpp  
* boost/charconv/detail/issignaling.hpp  
* boost/charconv/detail/compute_float32.hpp  
* boost/charconv/detail/buffer_sizing.hpp  
* boost/charconv/detail/dragonbox/*  
* boost/charconv/detail/fast_float/*  
* boost/charconv/detail/ryu/*  
  
It's up to you to decide whether this is worth the effort or not.

---

## Comment 6

> Username: Flamefire  
> Created at: 2025-01-11 10:53:23 UTC  
> Url: https://github.com/boostorg/charconv/issues/248#issuecomment-2585202111  

Of those only `boost/charconv/detail/parser.hpp` could affect me, but as written I'd need it to parse `uint64_t` values with full precision differentiating input between valid and invalid due to wrong characters or overflow/range. And I don't think `parser` can do that or even should as in the context of parsing a floating point number there is no overflow in the significant as rounding is the correct behavior if I'm not mistaken.  
  
So I'm fine with the moving as I have/had to write an own significant-exponent parser that can handle that difference which wasn't too hard with this code as inspiration.  
  
I still suggest to review the code carefully and extend the tests to define and verify the expected behavior, see https://github.com/boostorg/charconv/issues/244 & https://github.com/boostorg/charconv/pull/241
