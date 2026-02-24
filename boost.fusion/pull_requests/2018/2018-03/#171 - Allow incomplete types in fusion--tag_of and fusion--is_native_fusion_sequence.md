# #171 Allow incomplete types in fusion::tag_of and fusion::is_native_fusion_sequence [Merged]

> Username: ldionne  
> Created at: 2018-03-01 04:04:24 UTC  
> Updated at: 2018-03-03 07:06:39 UTC  
> Merged at: 2018-03-03 04:31:23 UTC  
> Closed at: 2018-03-03 04:31:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/171  

Fixes #170   
  
Please merge this before the 1.67.0 release, as this is a regression.

---

## Review 1 [Changes requested]

> Username: Flast  
> Created_at: 2018-03-01 14:15:45 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/fusion/pull/171#pullrequestreview-100444030  

Please confirm usage on hana, https://github.com/boostorg/fusion/issues/170#issuecomment-369513528

---

## Comment 2

> Username: Flast  
> Created_at: 2018-03-02 09:10:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/171#issuecomment-369864037  

@ldionne can you add a patch for `fusion::is_sequence`?

---

## Comment 3

> Username: ldionne  
> Created_at: 2018-03-02 10:02:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/171#issuecomment-369878194  

`is_sequence` does not suffer from the same problem:  
  
```c++  
template <typename T>  
struct is_sequence  
  : mpl::bool_<  
        (bool)extension::is_sequence_impl<  
            typename fusion::detail::tag_of<T>::type  
        >::template apply<T>::type::value  
    >  
{};  
```  
  
If `T` is incomplete, `tag_of` will do the right thing and return something like `non_fusion_tag`. However, `is_native_fusion_sequence` appears to suffer from the same problem, so I'll patch this one too.

---

## Review 4 [Approved]

> Username: Flast  
> Created_at: 2018-03-02 11:17:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/fusion/pull/171#pullrequestreview-100743991  

> is_sequence does not suffer from the same problem:  
  
You're right.

---

## Comment 5

> Username: Flast  
> Created_at: 2018-03-03 04:31:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/171#issuecomment-370117088  

Thanks!

---

## Comment 6

> Username: djowel  
> Created_at: 2018-03-03 07:06:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/171#issuecomment-370125699  

Thanks @ldionne

---
