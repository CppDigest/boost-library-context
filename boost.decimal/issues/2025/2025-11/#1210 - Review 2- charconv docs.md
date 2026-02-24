# #1210 - Review 2: charconv docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 18:54:58 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-12 18:21:37 UTC  
> Url: https://github.com/boostorg/decimal/issues/1210  

>The `charconv` example contains initialization by a binary floating point.  See  
my interface recommendation that describes that several kinds of such  
conversion exists.  I would also like to refer to the documentation itself  
saying that conversion from binary floating point is not recommended.  For  
these two reasons I recommend using user defined literals in this example and  
not get into the "controversy" of binary<->decimal conversions.  
  
This sounds good to me  
  
>The example uses a character array as output of `to_chars` with a size that  
seems to be arbitrary.  I recommend changing the size either to match the known  
input (the easy way) or to a size that is calculated from the attributes of  
`decimal64_t` to be able to accommodate the longest possible output.  In either  
case I recommend documenting where the number came from, in case of the maximum  
length that could be a welcome introduction into how to calculate that for any  
of the DFP types.  
  
Yep, again, might as well do it right?

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 08:05:01 UTC  
> Url: https://github.com/boostorg/decimal/issues/1210#issuecomment-3495710740  

That's fair. I also have the same struct from Boost.Charconv in here that sets a buffer size for you e.g. `char buffer[boost::decimal::limits<decimal32_t>::max_chars]` which makes it very explicit.

---

## Comment 2

> Username: mborland  
> Created at: 2025-11-07 15:43:58 UTC  
> Url: https://github.com/boostorg/decimal/issues/1210#issuecomment-3503280546  

Better buffer sizing is in: https://github.com/cppalliance/decimal/pull/1227, and then I'll add it in with the various doc/example fixes.
