# #74 Fix some warnings reported by clang [Merged]

> Username: mloskot  
> Created at: 2018-04-06 23:15:41 UTC  
> Updated at: 2018-04-08 16:01:42 UTC  
> Merged at: 2018-04-07 02:58:21 UTC  
> Closed at: 2018-04-07 02:58:21 UTC  
> Url: https://github.com/boostorg/gil/pull/74  

### Description  
  
Cleans up a bunch of warnings reported for minimalistic  
  
```  
#include <boost/gil.hpp>  
int main() {}  
```  
  
namely:  
  
- unused parameter  
- shadowed typedef  
- implicit conversion changes signedness:  
  int to signed <int>  
  int to unsigned <int>  
  
Note, arithmetic op is performed on `int` as higher rank type with result is in range of return type, safe to cast.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: clang 6

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-04-07 02:58:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/74#pullrequestreview-110244722  

This looks all good. Many thanks !

---

## Review 2 [Commented]

> Username: chhenning  
> Created_at: 2018-04-07 13:52:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/74#pullrequestreview-110233898  

📁 include/boost/gil/algorithm.hpp

```diff
 200 |         gil_function_requires<MutablePixelIteratorConcept<O> >();
 201 |         while (n>0) {
 202 |-             typedef typename iterator_from_2d<IL>::difference_type diff_t;
```

> Username: chhenning  
> Created_at: 2018-04-06 23:27:19 UTC  
> Updated_at: 2018-04-07 13:52:32 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179896819  

Where is diff_t defined?

> Username: mloskot  
> Created_at: 2018-04-07 21:14:07 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928022  

A bit earlier in line 197


📁 include/boost/gil/channel_algorithm.hpp

```diff
 335 |     typedef bits8 type;
 336 |-     type operator()(bits8s  val) const { return val+128; }
 336 |+     type operator()(bits8s  val) const { return static_cast<bits8>(val+128); }
```

> Username: chhenning  
> Created_at: 2018-04-07 00:34:27 UTC  
> Updated_at: 2018-04-07 13:52:32 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179901982  

should this not be static_cast<bits8>(val)+128;

> Username: chhenning  
> Created_at: 2018-04-07 21:22:03 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928191  

Of course, I mean `static_cast<bits8>(val)`

> Username: chhenning  
> Created_at: 2018-04-07 21:23:44 UTC  
> Updated_at: 2018-04-07 21:24:37 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928225  

Also, should we use `INT8_MAX+1` for `128`?

> Username: mloskot  
> Created_at: 2018-04-07 21:31:22 UTC  
> Updated_at: 2018-04-07 21:31:23 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928369  

Precisely, do you mean `static_cast<bits8>(val)+128` ?  
  
AFAIU, since `val` is of type smaller than `int`, it is first promoted to `int`, regardless of signedness.  
The `128` is `int`.  
The two `int` are added.  
Finally, result is truncated to fit into/stored in `unsigned char` type.  
There should be no unexpected results in this particular case, so the cast just makes the result conversion explicit.  
  
Please, correct me if I'm wrong.

> Username: mloskot  
> Created_at: 2018-04-07 21:33:17 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928410  

> `INT8_MAX+1`  
  
Better, `std::numeric_limits<std::int8_t>::max` or similar, but no macros :)

> Username: stefanseefeld  
> Created_at: 2018-04-07 21:35:04 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928467  

+1 on no macros. But also: let's not over-engineer this. Code readability is important, too !  
Sometimes a little inline comment goes a long ways to make the intent explicit (if in doubt).

> Username: chhenning  
> Created_at: 2018-04-07 21:36:58 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928499  

I'm just raising this because we fixed a bug in gil via:  
`type operator()(bits32s x) const { return static_cast<bits32>(x)+(static_cast<bits32>(1)<<31); }`

> Username: chhenning  
> Created_at: 2018-04-07 21:37:34 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928504  

This feels related.

> Username: mloskot  
> Created_at: 2018-04-07 21:46:18 UTC  
> Updated_at: 2018-04-07 21:46:33 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928667  

Yes, correct. But here we have 32-bit integers plus potential for arithmetic overflow, so that is subtly different situation.  
  
Perhaps, to make it clear for the code reader that all the subtleties have been considered, we could fix it this way:  
  
```  
type operator()(bits8s  val) const {  
    return static_cast<bits8>(static_cast<bits32>(val)+128u);  
}  
```  
  
What you think?

> Username: stefanseefeld  
> Created_at: 2018-04-07 21:48:42 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179928719  

That works for me.

> Username: mloskot  
> Created_at: 2018-04-08 16:01:42 UTC  
> Url: https://github.com/boostorg/gil/pull/74#discussion_r179952207  

Good. Refined this PR in #76


---
