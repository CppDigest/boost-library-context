# #231 Use Boost.Variant instead of GIL's own variant implementation. [Merged]

> Username: sdebionne  
> Created at: 2019-02-04 12:55:31 UTC  
> Updated at: 2019-02-07 09:06:58 UTC  
> Merged at: 2019-02-05 00:47:09 UTC  
> Closed at: 2019-02-05 00:47:09 UTC  
> Url: https://github.com/boostorg/gil/pull/231  

Tentatively trying to fix #131 and working around GCC bug [87107](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87107)  
  
Since Boost.Variant already supports lambda visitors, this makes #227 probably unnecessary.  
  
### References  
  
#131  
#227  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-02-04 22:29:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/231#pullrequestreview-199837900  

Looks good to me. No new warnings seem to appear (at least on Travis), that's good.  
There is a trade-off obviously and GIL now directly requires Boost.Variant, but I think the benefits outweight that extra dep.  
  
@sdebionne  Thanks for the contribution!  
  
If this is merged, I think it should close #131 as fixed :-)  
  
Let's see what @stefanseefeld says

📁 .appveyor.yml

```diff
  95 |   - git submodule --quiet update --init libs/test
  96 |   - git submodule --quiet update --init libs/type_traits
  97 |+   - git submodule --quiet update --init libs/variant
```

> Username: mloskot  
> Created_at: 2019-02-04 22:24:01 UTC  
> Updated_at: 2019-02-04 22:29:20 UTC  
> Url: https://github.com/boostorg/gil/pull/231#discussion_r253670687  

Yes, we need this here.  
  
Alternatively, you can do `python tools/boostdep/depinst/depinst.py gil` and this is what CircleCI does, but on AppVeyor and Travis, I decided to maintain explicit lists of direct and transitive dependencies for now - it let's us keep an eye on the current deps situation.


📁 include/boost/gil/extension/dynamic_image/any_image_view.hpp

```diff
  60 |- class any_image_view : public variant<ImageViewTypes> {
  61 |-     using parent_t = variant<ImageViewTypes>;
  60 |+ class any_image_view : public make_variant_over<ImageViewTypes>::type {
```

> Username: mloskot  
> Created_at: 2019-02-04 22:26:47 UTC  
> Updated_at: 2019-02-04 22:29:20 UTC  
> Url: https://github.com/boostorg/gil/pull/231#discussion_r253671510  

Digression: This file needs to get the format right at one point :)


📁 include/boost/gil/extension/dynamic_image/apply_operation.hpp

```diff
  30 | BOOST_FORCEINLINE
  31 | auto apply_operation(variant<Types>& arg, UnaryOp op)
  32 |+ #if defined(BOOST_NO_CXX14_DECLTYPE_AUTO) || defined(BOOST_NO_CXX11_DECLTYPE_N3276)
```

> Username: mloskot  
> Created_at: 2019-02-04 22:27:22 UTC  
> Updated_at: 2019-02-04 22:29:20 UTC  
> Url: https://github.com/boostorg/gil/pull/231#discussion_r253671701  

Good stuff.


📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
   1 |- //
```

> Username: mloskot  
> Created_at: 2019-02-04 22:27:45 UTC  
> Updated_at: 2019-02-04 22:29:20 UTC  
> Url: https://github.com/boostorg/gil/pull/231#discussion_r253671816  

Wonderful! Happy to see this code go :)


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-02-05 01:26:58 UTC  
> Url: https://github.com/boostorg/gil/pull/231#issuecomment-460479959  

I made mistake while editing squashed commits message, this should read `C++14` in  
  
`Adds support C++11 decltype(auto)`  
  
I don't feel comfy about amending the pushed commit. Sorry about that.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-02-05 19:38:28 UTC  
> Url: https://github.com/boostorg/gil/pull/231#issuecomment-460774633  

@stefanseefeld, @sdebionne   
While adding CI build w/ C++14, #233, I realised, are we having any tests for the `dynamic_image` extension or I'm just not seeing them?   
  
I think the only executable compiled with any `#include <boost/gil/extension/dynamic_image/*>` is the `example/dynamic_image.cpp`.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2019-02-05 20:20:07 UTC  
> Url: https://github.com/boostorg/gil/pull/231#issuecomment-460788445  

`gil/test/image.cpp` seems to contain some tests involving the `dynamic_image` extension.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-02-07 09:06:58 UTC  
> Url: https://github.com/boostorg/gil/pull/231#issuecomment-461338813  

@stefanseefeld Right. Let's take it for now as enough :)

---
