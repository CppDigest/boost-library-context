# #650 fix: is_equal_to_sixteen in PNG I/O was less-than test [Merged]

> Username: striezel  
> Created at: 2022-04-25 18:03:44 UTC  
> Updated at: 2022-05-03 07:33:59 UTC  
> Merged at: 2022-04-26 19:27:22 UTC  
> Closed at: 2022-04-26 19:27:23 UTC  
> Url: https://github.com/boostorg/gil/pull/650  

### Description  
  
While `is_equal_to_sixteen` should (as the name suggests) check for equality to 16, it actually checked whether the bit depth was less than 16.  
  
Since Boost.MP11 does not contain an `mp11::mp_equal` but only `mp11::mp_less`, the equality check is performed by making sure that  
* the bit depth is not less than 16,  
* 16 is not less than the bit depth,  
* and both of those hold true.  
  
It's a bit laborious, but it works.  
  
### References  
  
The wrong comparison was introduced by PR #274 in https://github.com/boostorg/gil/commit/5611bd58071873d6346fe3cc05fe86c72f697717#diff-e2c958c778b464b94146992b6aeb7c814839ddbcbbb901f9f4175e797fa543d2 when `boost::mpl` was replaced with `boost::mp11`. In that commit the original `mpl::equal_to` was replaced by `mp11::mp_less`. To be fair though, unlike MPL the MP11 library does not contain an equivalent to `mpl::equal_to`.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-04-25 18:11:59 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1108885350  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/650?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#650](https://codecov.io/gh/boostorg/gil/pull/650?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6a77ba5) into [develop](https://codecov.io/gh/boostorg/gil/commit/caf92fa94ba4b5960b7431dd9c7e391400aa293d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (caf92fa) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #650      +/-   ##  
===========================================  
- Coverage    78.77%   78.76%   -0.01%       
===========================================  
  Files          117      117                
  Lines         5031     5030       -1       
===========================================  
- Hits          3963     3962       -1       
  Misses        1068     1068                
```

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-04-25 20:11:07 UTC  
> Updated_at: 2022-04-25 20:18:37 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1108993241  

Good catch, @striezel !  
  
It looks like I did a copy & paste mistake copying `struct is_less_than_eight : mp11::mp_less` as template of `is_equal_to_sixteen`.  
  
-----  
  
Hmm, since this bug was introduced by #274 in Boost 1.72 which is   
> between Boost 1.71 and Boost 1.74  
  
as @striezel points out in https://github.com/boostorg/gil/issues/633#issuecomment-1108885397, so any chance it is related to the issue #633 ?

---

## Comment 3

> Username: striezel  
> Created_at: 2022-04-25 23:02:04 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1109120677  

> Hmm, since this bug was introduced by #274 in Boost 1.72 which is  
>   
> > between Boost 1.71 and Boost 1.74  
>   
> as @striezel points out in [#633 (comment)](https://github.com/boostorg/gil/issues/633#issuecomment-1108885397), so any chance it is related to the issue #633 ?  
  
Well, that is what I am hoping for. Fixing `is_equal_to_sixteen` may possibly fix the issue in #633, too. That's how I found it in the first place, because I was looking into the issue with 16 bit PNGs.

---

## Comment 4

> Username: striezel  
> Created_at: 2022-04-26 00:33:53 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1109171365  

>  so any chance it is related to the issue #633 ?  
  
Seems like it. I just made another test with the example from https://github.com/boostorg/gil/issues/633#issuecomment-1025136555. Using Boost from the current master branch (boostorg/boost@0f43af9443299c90cc60996abce5bb66c293c48e) still has the weird colors in the 16 bit image. However, when I switch the submodule for GIL to the version from this PR, then the 16 bit image comes out fine.

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-04-26 09:23:48 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1109563245  

Sounds good, thank you @striezel for your help

---

## Comment 6

> Username: sdebionne  
> Created_at: 2022-05-02 09:15:24 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1114651977  

Sorry for the late feedback but shouldn't `is_equal_to_sixteen` be just:  
  
```  
struct is_equal_to_sixteen : std::is_same  
        <  
            std::integral_constant<int, Info::_bit_depth>,  
            std::integral_constant<int, 16>  
        >  
    {};  
```

---

## Comment 7

> Username: mloskot  
> Created_at: 2022-05-02 12:35:33 UTC  
> Updated_at: 2022-05-02 12:36:01 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1114796994  

Yes, there is no requirement to use mp11 features here.

---

## Comment 8

> Username: striezel  
> Created_at: 2022-05-02 20:55:45 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1115358438  

As I understand it, `std::is_same` is for comparing types, not values:  
  
> template< class T, class U >  
struct is_same;  
>  
> If `T` and `U` name the same type (taking into account const/volatile qualifications), provides the member constant <code>value</code> equal to `true`. Otherwise `value` is `false`.  
  
(from https://en.cppreference.com/w/cpp/types/is_same)  
  
In contrast to that `boost::mp11::mp_less` compares the values:  
  
> `mp_less<T1, T2>` is `mp_true` when the numeric value of `T1::value` is less than the numeric value of `T2::value`, `mp_false` otherwise.  
  
(from https://www.boost.org/doc/libs/1_79_0/libs/mp11/doc/html/mp11.html#mp_lesst1_t2)  
  
To summarize it, the mp11 stuff compares _values_ while `std::is_same` compares _types_.  
  
So this would not be the same as the implementation that got merged. Or am I missing something here?

---

## Comment 9

> Username: sdebionne  
> Created_at: 2022-05-03 07:33:05 UTC  
> Updated_at: 2022-05-03 07:33:59 UTC  
> Url: https://github.com/boostorg/gil/pull/650#issuecomment-1115815049  

`std::is_same` is indeed for comparing types. Since `std::integral_constant<int, Info::_bit_depth>` and `std::integral_constant<int, 16>` are both types that represents compile time integral values it's appropriate to use `std::is_same` for comparing the values of the same underlying type (`int`).   One could use `mp11::mp_similar` to make the comparison a bit looser (e.g. discarding constness, etc...).  
  
Note that I am not saying your implementation does not work, it's just a bit over complicated.  
  
```c++  
template <typename Info>  
struct is_equal_to_sixteen : std::is_same  
    <  
        std::integral_constant<int, Info::_bit_depth>,  
        std::integral_constant<int, 16>  
    >  
{};  
```  
  
https://godbolt.org/z/5qGz4WKTc

---
