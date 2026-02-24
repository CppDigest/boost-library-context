# #239 Fix point_iterator with raw pointer base [Merged]

> Username: sdebionne  
> Created at: 2015-03-03 15:01:21 UTC  
> Updated at: 2015-03-06 10:28:30 UTC  
> Merged at: 2015-03-06 05:17:08 UTC  
> Closed at: 2015-03-06 05:17:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/239  

Fix `point_iterator` (and adapt `reverse_point_iterator` to the fix) to support linestrings/rings implemented with raw pointer as iterator. The issue can be reproduced with:  
  
```  
boost::iterator_range<const point_t *> l;  
points_begin(l);  
```  
  
The compiler complain that `const point_t *` is not a legal base class of `point_iterator`.  
  
The fix uses `boost::iterator_adaptor` that implements base() a synonyme of base_ptr() that consequently has been removed. The operators are also redondant and removed.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-03 23:42:17 UTC  
> Updated_at: 2015-03-04 09:10:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#discussion_r25739744  

In order to follow our coding guidelines please re-write the above as follows:  
  
```  
template <typename Geometry>  
class point_iterator  
    : public boost::iterator_adaptor  
        <  
            point_iterator<Geometry>,  
            typename detail::point_iterator::iterator_type<Geometry>::type  
        >  
{  
    // ...  
}  
```

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-03 23:44:15 UTC  
> Updated_at: 2015-03-04 09:10:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#discussion_r25739873  

The type `base` is not really needed now that you are using `boost::iterator_adaptor<>`. It is defined in `boost::iterator_adaptor<>` as `base_type`.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-03 23:46:42 UTC  
> Updated_at: 2015-03-04 09:10:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#discussion_r25740024  

This line seems too long (more than 80 characters). Please re-write it as:  
  
```  
inline point_iterator(typename point_iterator::base_type const& base_it)  
    : point_iterator::iterator_adaptor_(base_it)  
{}  
```  
  
Notice that I also used `base_type` from `boost::iterator_adaptor<>` that I have mentioned in another line comment.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-03 23:49:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77065218  

Samuel thanks a lot for the fix.  
Please see the line notes I made.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-03-04 00:25:27 UTC  
> Updated_at: 2015-03-04 09:10:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#discussion_r25742237  

Both `g++` and `clang++` confuse the `base` type with the `base()` method called in the constructor.  
Please rename `base` to `base_type`.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-03-04 00:27:16 UTC  
> Updated_at: 2015-03-04 09:10:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#discussion_r25742351  

In the private constructor above please rename `base` to `base_type`.  
To avoid a long line, please write it as:  
  
```  
inline point_reverse_iterator(base_type const& base_it)  
    : base_type(base_it)  
{}  
```

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-03-04 00:27:42 UTC  
> Updated_at: 2015-03-04 09:10:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#discussion_r25742375  

Please replace `base` by `base_type`.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-03-04 00:29:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77073015  

Just one more comment: the `base()` method you are using in `point_reverse_iterator<>` has nothing to do with the changes in `point_iterator<>`. It is coming from `std::reverse_iterator<>`.  
I should have used it in the first place.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-03-04 00:31:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77073176  

Once again, thanks for the fix.

---

## Comment 10

> Username: sdebionne  
> Created_at: 2015-03-04 09:14:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77121248  

And thank you for the thoroughfull review ! I have made the changes you mention in your line comments. And you are right about `point_reverse_iterator<>`, I was confused when I looked for any other class that used the `base_ptr()` function.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-03-04 10:17:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77129900  

Thanks for taking care of this. Please run the units tests if you have not done already.  
I am okay with merging this.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-03-04 12:23:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77148643  

Thanks for the contribution/review  
I'm OK too, as soon as it is confirmed that all unit tests are run successfully (especially because the branch closes today)

---

## Comment 13

> Username: sdebionne  
> Created_at: 2015-03-04 22:16:58 UTC  
> Updated_at: 2015-03-05 20:52:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77262130  

All tests passed on Windows 7 64 bit, MSVC 2013. It's probably to late for 1.58 but could this be merged to develop ?

---

## Comment 14

> Username: mkaravel  
> Created_at: 2015-03-06 09:57:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77533954  

@sdebionne Interestingly enough, some of my unit tests fail (both in the algorithms and in the iterators directories). The reason is a missing `const` in the private `point_iterator<>` constructor. I am curious as to what is the cause of the different behavior of `g++` and `clang++` with respect to MSVC 2013 on this issue.  
No need to do anything on your side: I have filed PR #243 to fix the problem.

---

## Comment 15

> Username: sdebionne  
> Created_at: 2015-03-06 10:11:37 UTC  
> Updated_at: 2015-03-06 10:12:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77535729  

@mkaravel I have just seen your PR, sorry about that. I had corrected the missing `const` on my branch as well but for some obscure reason this was not pushed to Github... Interestingly the computer where I ran the tests was a fresh checkout from Github so did _NOT_ have the `const`, so I would like to investigate. Here is what I did (basically following your Contributing Tutorial):  
- Clone boost modular (recursive)  
- Generate the headers (b2 headers)  
- Add a new remote for geometry fork  
- Checkout my branch `sdebionne:fix/iterators`  
- run b2 in geometry\tests  
  
AFAICT all targets build successfully with `*passed*`. Is there any generated report that I could check ?

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-03-06 10:28:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/239#issuecomment-77537871  

@sdebionne This is what I do also: run `b2` and inspect the output. I am not aware of any other reporting mechanism.

---
