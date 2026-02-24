# #1001 - Rationale for treating double as distinct in comparisons? [Closed]

> Username: ecorm  
> Created at: 2024-04-22 21:53:19 UTC  
> Updated at: 2024-04-26 22:42:27 UTC  
> Closed at: 2024-04-26 22:42:27 UTC  
> Url: https://github.com/boostorg/json/issues/1001  

The documentation for `value::operator==` has this to say:  
  
> Two values are equal when they are the same kind and their referenced values are equal, or when they are both integral types and their integral representations are equal.   
  
When it comes to comparisons, a `double` value is therefore treated as a distinct kind, whereas integral values are treated as the same kind.  
  
Can someone explain the rationale for this behavior? Is it because very large integers cannot be represented exactly when converted to `double`?  
  
I should clarify that the decision to do the comparison this way seems reasonable, it's just I would like to gain deeper insight into the reasoning.  
  
This might have been discussed in the formal review, but mailing list archives are not very searchable. I'll try searching them anyway in the meantime.  
  
The nlohmann/json library does this:  
  
> Compares two JSON values for equality according to the following rules:  
>  
>    Two JSON values are equal if  
> (1) neither value is discarded, or  
> (2) they are of the same type and their stored values are the same according to their respective operator==.  
>    Integer and floating-point numbers are automatically converted before comparison.

---

## Comment 1

> Username: ecorm  
> Created at: 2024-04-22 22:28:24 UTC  
> Updated at: 2024-04-23 18:42:08 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2071056576  

A corner case where the Boost.Json comparison behavior could be problematic is when a Javascript peer accidentally serializes a semantically floating point value into an integer without a decimal point, because the number just happens to be one that's exactly representable as an integer.  
  
`{"voltage": 3.10104}` -> `document.at("voltage") == 3.0` -> `false` // OK  
  
`{"voltage": 3.0}` -> `document.at("voltage") == 3.0` -> `true` // OK  
  
`{"voltage": 3}` -> `document.at("voltage") == 3.0` -> `false` // Oops  
  
Where "voltage" is a fluctuating value that could coincidentally be exactly 3 (e.g. a 12-bit analog-to-digital converter).

---

## Comment 2

> Username: ecorm  
> Created at: 2024-04-22 22:33:26 UTC  
> Updated at: 2024-04-22 22:33:47 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2071061537  

I just realized in my example above that nobody should do such a floating-point equality comparsion without a +/-epsilon.

---

## Comment 3

> Username: ecorm  
> Created at: 2024-04-22 22:43:54 UTC  
> Updated at: 2024-04-23 18:31:31 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2071072094  

I guess a good rationale for treating signed/unsigned integers as the same kind in comparisons is when using literals on the right hand side:  
  
`{"id": 42}` -> `42` parsed as unsigned integer  
`document.at("id") == 42` // Literal `42` is signed!

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-04-23 08:52:04 UTC  
> Updated at: 2024-04-23 08:53:01 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2071765249  

The rationale is that the stored values are different and thus certain member functions would have different behaviours. It would be strange to have equal values behave differently.  
  
If the user wants a more lax comparison, it can be acheived: https://godbolt.org/z/4vz9h63MG. If you can use C++ concepts, than here is a slightly cleaner version: https://godbolt.org/z/TahMGMG8o.  
  
BTW, `42` would parse as `int64_t`. And `"42"` would parse as `json::string`.

---

## Comment 5

> Username: ecorm  
> Created at: 2024-04-23 18:40:06 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2073166874  

> The rationale is that the stored values are different and thus certain member functions would have different behaviours.  
  
But `int64_t` and `uint64_t` are stored differently, yet they are treated as the same kind in comparisons. This does not adequately explain why `double` is treated as a different kind in comparisons.  
  
> It would be strange to have equal values behave differently.  
  
It is currently "strange" that `assert(json::value(42) == json::value(42.0)` fails.  
  
> BTW, 42 would parse as int64_t. And "42" would parse as json::string.  
  
I incorrectly assumed 42 would parse as `uint64_t` because that's what the current JSON library I'm using does (only negative integers get parsed as `int64_t`). The quoted "42" in my example was a brain fart. :-)

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-04-23 18:44:03 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2073175880  

> But `int64_t` and `uint64_t` are stored differently, yet they are treated as the same kind in comparisons. This does not adequately explain why `double` is treated as a different kind in comparisons.  
  
You got me there. I guess, it's for the convenience of `value(1u) == 1`.

---

## Comment 7

> Username: ecorm  
> Created at: 2024-04-23 18:50:20 UTC  
> Url: https://github.com/boostorg/json/issues/1001#issuecomment-2073189990  

>>    BTW, 42 would parse as int64_t. And "42" would parse as json::string.  
>  
> I incorrectly assumed 42 would parse as uint64_t because that's what the current JSON library I'm using does (only negative integers get parsed as int64_t). The quoted "42" in my example was a brain fart. :-)  
  
Ah, I think I've stumbled upon the rationale right there. Treating signed/unsigned integers as the same kind in comparisons eliminates the guesswork and messiness in dealing with how the library parses positive/negative integers into `uint64_t` or `int64_t`.
