# #362 - msb return type [Closed]

> Username: mglisse  
> Created at: 2021-08-27 20:39:30 UTC  
> Updated at: 2021-12-18 09:16:17 UTC  
> Closed at: 2021-12-18 09:16:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362  

Hello,  
I notice that functions like `msb` that return a number of bits (not bytes or limbs) are documented to return `unsigned int` always. This means that a number that takes 600MB of memory is already too large for this function on most platforms. Is this limitation documented somewhere, and is it on purpose?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-12-04 13:52:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362#issuecomment-986030590  

Sorry to take so long to reply to this: it's by accident, I didn't expect numbers that large!  I guess we can only deal with numbers which occupy 1/8th of the available address space, the unsigned int should be a size_t at the very least, and maybe a uintmax_t.

---

## Comment 2

> Username: mglisse  
> Created at: 2021-12-04 14:12:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362#issuecomment-986034504  

I guess I mostly care about making it more than 32 bits on 64-bit CPUs, the 1/8th limitation doesn't bother me that much.  
(no need for apologies, you are doing a good job)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-12-16 18:12:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362#issuecomment-996060122  

OK the referenced PR is working well, and computations and bit fiddling on cpp_int involving slightly more than 2^32 bits are just about possible if you don't mind the wait.  
  
However, what on earth do we do about `numeric_limits<>::digits`?  The std says it's an `int`, but that's no longer large enough for our purpose.  On the other hand changing it to `ptrdiff_t` breaks code (including ours).  
  
Thoughts?  @mglisse @ckormanyos

---

## Comment 4

> Username: mglisse  
> Created at: 2021-12-16 18:59:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362#issuecomment-996093374  

Sorry for causing such a huge patch.  
  
> However, what on earth do we do about `numeric_limits<>::digits`? The std says it's an `int`, but that's no longer large enough for our purpose.  
  
Yes, the standard traits were not really designed for bignum, and any code that may use bignums should use numeric_limits very carefully...  
For unlimited numbers, I would just use 0 (the default value), this entry is meaningless. Well, possibly not 100% meaningless, for instance mpz_t is currently limited to something like 2^40 digits, but I think trying to set digits to 2^40 would not be very helpful. Not quite as explosive as having `max()` cause an OOM...  
The main issue is if someone uses a bounded-size number where the log of the max value does not fit `int`? Hopefully that's not very common. I am still tempted to return 0 in that case, but I can see why someone might prefer INT_MAX or a non-standard type like `long long` to represent the true value.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-12-16 21:22:01 UTC  
> Updated at: 2021-12-16 21:23:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362#issuecomment-996203574  

> what on earth do we do about `numeric_limits<>::digits`? The std says it's an int, but that's no longer large enough for our purpose. On the other hand changing it to ptrdiff_t breaks code (including ours).  
  
This is indeed a tough one. My subjective determination is that there is no resolution for this issue yet. The standard would have to eventually cover big integral types which would, as a prerequisite, require some modification or variation of numeric limits.  
  
> I can see why someone might prefer `INT_MAX` or a non-standard type like `long` `long` to represent the true value.  
  
Whatever we do, it's going to end up wrong, in the sense that it is non-standardized. My question would be what is the least wrong? My opinion is based almost solely on types having fixed widths like a million or a billion digits. In those cases I have found the latter suggestion the least wrong of the suggestions --- i.e., changing the type(s) of the `digits`, `max_digits`, etc. to something other than `int`and larger than `int` --- which will still break code all over.  
  
How bad is it to keep it at `int`? Disable warning(s) all over the place. That might be the more conservative way to break as little as possible.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-12-17 19:32:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/362#issuecomment-996983752  

> How bad is it to keep it at int? Disable warning(s) all over the place. That might be the more conservative way to break as little as possible.  
  
That's what I went for, seems to work OK so far...
