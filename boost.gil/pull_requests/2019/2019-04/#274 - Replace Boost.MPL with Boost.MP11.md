# #274 Replace Boost.MPL with Boost.MP11 [Merged]

> Username: mloskot  
> Created at: 2019-04-02 22:35:14 UTC  
> Updated at: 2019-04-15 21:00:57 UTC  
> Merged at: 2019-04-14 20:13:46 UTC  
> Closed at: 2019-04-14 20:13:46 UTC  
> Url: https://github.com/boostorg/gil/pull/274  

### Description  
  
- Use type traits and features of C++11, then use Boost.MP11.  
- Remove unused and unnecessary metafunctions in `detail` namespace.  
- Remove explicit access to ::type as no longer necessary with MP11.  
- Replace `boost::is_integral` with `gil::detail::is_channel_integral` to avoid UB.  
- Clean up and reformat code according to the current guidelines.  
  
Legacy tests have been updated where necessary to accommodate switch to MP11.  
  
### Status  
  
Ready to start review process.  
  
I will try make it easier to review/track changes and submit updates as new commits,  
but sometimes I may need to rebase and force-push update. Sorry.  
  
Two files have not been updated and it needs to be decided what to do:  
  
- `promote_integral.hpp` which is external source borrowed from Boost.Geometry.   
- `extension/dynamic_image/reduce.hpp` which is compiled on demand by `#define BOOST_GIL_REDUCE_CODE_BLOAT=1`  
  
### References  
  
* Among others, implements #93  
* Closes #229   
  
### Tasklist  
  
- [x] Decide what to do with `promote_integral.hpp`. See #280  
- [x] Decide what to do with `extension/dynamic_image/reduce.hpp`. See #281  
- [x] Solve failing checksum test in `test/legacy/image.cpp` for `bgr121_*` (fixed in f45dd04aca1996ec8d1315cf495f9e5a78387f02)  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-04-09 19:13:34 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-481397103  

The failing checksum test should now be fixed in f45dd04aca1996ec8d1315cf495f9e5a78387f02  
  
It's funny how implicit namespace can make refactoring a bit more difficult :-)  
Simply, old `boost::is_integral` vs new `std::is_integral` led to call to completely different `channel_converter_unsigned_impl` specialization than expected.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-04-09 19:14:51 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-481397512  

You make me curious. Please explain !

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-04-09 19:39:20 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-481405363  

@stefanseefeld Let's try. Below, I'm using references to the current implementation based on Boost.MPL so it should be easier to follow.  
  
Consider this template:  
  
https://github.com/boostorg/gil/blob/8d2a90bf1b31e6d917c2a3e3ee60b09de250e56d/include/boost/gil/channel_algorithm.hpp#L103-L105  
  
Notice, this template which uses unqualified `is_integral`.  
  
For the `bgr121_*` test cases in the [legacy/image.cpp](https://github.com/boostorg/gil/blob/8d2a90bf1b31e6d917c2a3e3ee60b09de250e56d/test/legacy/image.cpp#L372-L380), `SrcChannelV` is `unsigned char` and `DstChannelV` is `packed_channel_value`.  
  
Now, for the `packed_channel_value`, there is this `boost::is_integral` specialization  
  
https://github.com/boostorg/gil/blob/8d2a90bf1b31e6d917c2a3e3ee60b09de250e56d/include/boost/gil/channel.hpp#L639-L643  
  
which is 'called' during specialization of base `channel_converter_unsigned_impl` template presented earlier.  
  
The Boost.MPL to Boost.MP11 convesrion in this PR replaces the `boost::is_integral` with `std::is_integral` and the specializations are now in `std` namespace, but that unqualified 'call' to `is_integral` as parameter to  `channel_converter_unsigned_impl` above still referred to `boost::is_integral`.  
Since there was no specialization of it for the `packed_channel_value`, the result was `false` instead of `true` and it led to using this default definition of channel converter:  
  
https://github.com/boostorg/gil/blob/8d2a90bf1b31e6d917c2a3e3ee60b09de250e56d/include/boost/gil/channel_algorithm.hpp#L118-L126  
  
instead of this  
  
https://github.com/boostorg/gil/blob/8d2a90bf1b31e6d917c2a3e3ee60b09de250e56d/include/boost/gil/channel_algorithm.hpp#L135-L138  
  
that, for the pair of channels in the `bgr121_*` tests (`SrcChannelV` is `unsigned char` and `DstChannelV` is `packed_channel_value`) eventually calls:  
  
https://github.com/boostorg/gil/blob/8d2a90bf1b31e6d917c2a3e3ee60b09de250e56d/include/boost/gil/channel_algorithm.hpp#L175-L183  
  
I hope it makes sense :-)

---

## Review 4 [Commented]

> Username: Kojoley  
> Created_at: 2019-04-10 23:22:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/274#pullrequestreview-225267314  

📁 include/boost/gil/bit_aligned_pixel_reference.hpp

```diff
  35 | public:
  41 |-     using byte_t = typename mpl::if_c<Mutable,unsigned char,const unsigned char>::type;
  36 |+     using byte_t = typename mp11::mp_if_c<IsMutable, unsigned char, unsigned char const>;
```

> Username: Kojoley  
> Created_at: 2019-04-10 23:22:16 UTC  
> Updated_at: 2019-04-13 12:28:18 UTC  
> Url: https://github.com/boostorg/gil/pull/274#discussion_r274207469  

A bogus `typename`, is not it?

> Username: stefanseefeld  
> Created_at: 2019-04-10 23:38:29 UTC  
> Updated_at: 2019-04-13 12:28:18 UTC  
> Url: https://github.com/boostorg/gil/pull/274#discussion_r274212134  

Indeed. Good catch !

> Username: mloskot  
> Created_at: 2019-04-11 07:48:19 UTC  
> Updated_at: 2019-04-13 12:28:18 UTC  
> Url: https://github.com/boostorg/gil/pull/274#discussion_r274297929  

@Kojoley Thanks for spotting this. I've removed this one and a few more left over.


---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-04-10 23:32:40 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-481910207  

That's not my business, but I wonder how you are going review this ;)  
  
P.S. There are bunch of changes like replacing boost with std type_traits (mpl::true_/false_ also in this category) or silencing warnings that could be done separately.

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-04-11 08:18:15 UTC  
> Updated_at: 2019-04-11 08:28:01 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-482016419  

@Kojoley   
> I wonder how you are going review this ;)  
  
It was well understood MPL to MP11 will propose large changeset that may be difficult if not impossible to carefully review diff by diff.  
That is why I've spent significant amount of time updating/adding tests prior to this work.   
That is why I've added so many CI builds for as many compilers and versions as possible.  
  
This is a big jump and, as every thorough refactoring of non-trivial codebase, it will introduce issues. So, we must continue adding more tests and more detailed tests with hope to fish for the issues - we will spend 3, 6, 9, ... months before we decide we are happy to release it.  
  
Finally, despite of the risks, I believe this does not deteriorate any GIL qualities; on the contrary.  
  
_"You want to change things, you have to break things"_ :-)  
  
> There are bunch of changes like replacing boost with std type_traits (mpl::true_/false_ also in this category) or silencing warnings that could be done separately.  
  
I attempted to remove only those traits or some other traits (e.g. #107), but due to tight coupling of MPL and Boost.TypeTraits (and tight coupling of GIL to those two), it was not practically feasible or required some 'mapping' of traits to C++11 equivalents which would have been clear/remove-refactored again once switching to MP11.  
  
I fished for some places where it was possible to apply C++11 features w/o breaking existing MPL-based infrastructure (e.g. #215, #225 and some more). Regardless, number of places where C++11 traits could be used directly was insignificant in comparison to size of the overall changeset required anyway.  
  
----  
  
I'm always advocate surgical strikes at code that introduce manageable diffs, but sometimes it is not feasible (esp. w/ limited time and manpower).

---

## Comment 7

> Username: mloskot  
> Created_at: 2019-04-11 08:35:30 UTC  
> Updated_at: 2019-04-11 13:11:56 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-482022037  

### Build Status Update  
  
This PR passes all these builds (compilers info in [README.md](https://github.com/boostorg/gil/blob/787d5cf90b2bfa5f77bc6db8d25601bd74fa0eb4/README.md)):  
* AppVeyor (C++14 w/ Boost `develop`)  
* AzP (C++11 and C++14 w/ Boost 1.68)  
* CircleCI (C++11 w/ Boost `master`)  
* TravisCI (C++11 w/ Boost `develop`)  
  
  
The only failing build is the [AzP one for C++17 w/ Boost 1.68 using VS2017](https://dev.azure.com/boostorg/gil/_build/results?buildId=337):  
  
```  
3>c:\boost\include\boost-1_68\boost\mp11\detail\mp_plus.hpp(28): error C2059: syntax error: '...' [D:\a\1\s\_build\test\legacy\legacy_pixel_iterator.vcxproj]  
         c:\boost\include\boost-1_68\boost\mp11\detail\mp_fold.hpp(47): note: see reference to class template instantiation 'boost::mp11::detail::mp_plus_impl<V,T1>' being compiled  
                 with  
                 [  
                     V=std::integral_constant<int,0>,  
                     T1=std::integral_constant<size_t,2>  
                 ]  
         c:\boost\include\boost-1_68\boost\mp11\detail\mp_fold.hpp(47): note: see reference to alias template instantiation 'boost::mp11::mp_plus<std::integral_constant<int,0>,T1>' being compiled  
                 with  
                 [  
                     T1=std::integral_constant<size_t,2>  
                 ]  
         d:\a\1\s\include\boost\gil\bit_aligned_pixel_reference.hpp(123): note: see reference to class template instantiation 'boost::mp11::detail::mp_fold_impl<ChannelBitSizes,std::integral_constant<int,0>,boost::mp11::mp_plus>' being compiled  
                 with  
                 [  
                     ChannelBitSizes=boost::mp11::mp_list<std::integral_constant<size_t,2>,std::integral_constant<size_t,3>,std::integral_constant<size_t,2>>  
                 ]  
         d:\a\1\s\include\boost\gil\bit_aligned_pixel_reference.hpp(118): note: see reference to alias template instantiation 'boost::mp11::mp_fold<boost::mp11::mp_list<std::integral_constant<size_t,2>,std::integral_constant<size_t,3>,std::integral_constant<size_t,2>>,std::integral_constant<int,0>,boost::mp11::mp_plus>' being compiled  
```  
  
I'm suspecting this is a problem with MP11 from the older release of Boost 1.68.  
If this is confirmed, then I will disable this build until AzP builds switch to Boost 1.70 (once released).  
  
### Results  
  
There is an issue in MP11 from Boost 1.68 related to fold expressions (thanks to @pdimov for confirmation). Here is MWE:  
  
```  
#include <boost/version.hpp>  
static_assert(BOOST_VERSION == 106800, "");  
#include <boost/mp11.hpp>  
#include <cstdint>  
#include <type_traits>  
int main()  
{  
    using V = std::integral_constant<int, 0>;  
    using T1 = std::integral_constant<std::size_t, 2>;  
    using R = boost::mp11::mp_plus<V,T1>;  
    R r;  
    return 0;  
}  
```  
  
![image](https://user-images.githubusercontent.com/80741/55959841-e41ef180-5c6b-11e9-91e9-6409093daa93.png)  
  
### Plan  
  
Update the Azure Pipelines builds to follow the grand rule of _"test develop against develop and master against master"_, as the AppVeyor builds do.

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-04-13 12:30:06 UTC  
> Updated_at: 2019-04-13 12:30:47 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-482805061  

I've rebased the PR onto `develop` to completed the plan from https://github.com/boostorg/gil/pull/274#issuecomment-482022037 and integrate the latest CI update from #279

---

## Review 9 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-04-14 13:34:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/274#pullrequestreview-226395897  

This is fantastic. Thanks for all your hard work on this !

---

## Comment 10

> Username: mloskot  
> Created_at: 2019-04-14 18:00:54 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-483030207  

I think I'll merge tonight :) Thrilled!

---

## Comment 11

> Username: mloskot  
> Created_at: 2019-04-14 20:21:36 UTC  
> Url: https://github.com/boostorg/gil/pull/274#issuecomment-483054986  

Ka-Boom!

---
