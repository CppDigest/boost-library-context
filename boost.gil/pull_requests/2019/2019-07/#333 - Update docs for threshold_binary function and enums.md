# #333 Update docs for threshold_binary function and enums [Merged]

> Username: mloskot  
> Created at: 2019-07-12 14:09:42 UTC  
> Updated at: 2020-02-22 13:09:21 UTC  
> Merged at: 2019-07-23 05:46:59 UTC  
> Closed at: 2019-07-23 05:46:59 UTC  
> Url: https://github.com/boostorg/gil/pull/333  

Prefer C++ style comments for Doxygen.

---

## Review 1 [Changes requested]

> Username: miralshah365  
> Created_at: 2019-07-17 20:46:50 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/333#pullrequestreview-263305997  

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 129 |+ /// of pixel based on channel type.
 130 |+ /// If direction is regular:
 131 |+ /// values more or equal to threshold_value will be set to maximum numeric limit of channel else 0
```

> Username: miralshah365  
> Created_at: 2019-07-17 20:44:22 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r304632851  

After merging #321 `threshold_value` is treated as a boundary so it is not included into valid range.   
Only pixel values more than `threshold_value` will be set to the maximum numeric limit.

> Username: mloskot  
> Created_at: 2019-07-18 22:23:09 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r305138486  

Done

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 156 |+ /// If mode is truncate and direction is inverse:
 157 |+ /// values less than threshold_value will be set to threshold_value else no change
 158 |+ /// If mode is zeo and direction is regular:
```

> Username: miralshah365  
> Created_at: 2019-07-17 20:46:15 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r304633582  

Typo in word `zero`.

> Username: mloskot  
> Created_at: 2019-07-18 22:23:23 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r305138538  

Done

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 131 |+ /// values more or equal to threshold_value will be set to maximum numeric limit of channel else 0
 132 |+ /// If direction is inverse:
 133 |+ /// values more than or equal to threshold_value will be set to 0 else maximum numeric limit of channel
```

> Username: miralshah365  
> Created_at: 2019-07-17 20:46:30 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r304633686  

See the comment above

> Username: mloskot  
> Created_at: 2019-07-18 22:23:16 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r305138516  

Done


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-07-18 22:23:42 UTC  
> Url: https://github.com/boostorg/gil/pull/333#issuecomment-513011611  

@miralshah365 Thanks for the feedback! Addressed.

---

## Review 3 [Changes requested]

> Username: miralshah365  
> Created_at: 2019-07-22 01:01:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/333#pullrequestreview-264565764  

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 152 |+ /// \brief Applies truncating threshold to each pixel of image view.
 153 |+ /// Takes an image view and performes truncating threshold operation on each chennel.
 154 |+ /// If mode is truncate and direction is regular:
```

> Username: miralshah365  
> Created_at: 2019-07-22 00:58:35 UTC  
> Updated_at: 2019-07-22 18:03:16 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r305646267  

My mistake, the mode option is `threshold` and not `truncate`.

> Username: mloskot  
> Created_at: 2019-07-22 18:03:24 UTC  
> Url: https://github.com/boostorg/gil/pull/333#discussion_r305959185  

Good catch. Fixed.


---
