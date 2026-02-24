# #881 Drop dependencies in tests. [Merged]

> Username: awulkiew  
> Created at: 2021-07-04 00:33:39 UTC  
> Updated at: 2021-07-30 11:18:22 UTC  
> Merged at: 2021-07-30 11:18:22 UTC  
> Closed at: 2021-07-30 11:18:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/881  

If possible drop type_traits, mpl, typeof, foreach, assign.  
Rearrange some includes.  
  
Replacing `MPL_ASSERT` with `static_assert` fixes clang warnings, e.g.:  
test\algorithms\comparable_distance.cpp(191,9): warning: unused variable 'mpl_assertion_in_line_203' [-Wunused-variable]

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-07-05 06:29:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/881#pullrequestreview-698833475  

Looks OK to me. Thanks.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2021-07-05 09:37:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/881#issuecomment-873966200  

I'm considering merging it now and releasing with 1.77. What do you think @barendgehrels @vissarion ?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-07-05 21:48:23 UTC  
> Updated_at: 2021-07-06 14:14:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/881#issuecomment-874342639  

@barendgehrels master will be closed for 1.77 on Wed so if possible could you review this PR before? If not then I think we'll wait and release it with 1.78 because it's bigger than the simple fixes I already merged.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2021-07-21 11:08:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/881#issuecomment-884105152  

> @barendgehrels master will be closed for 1.77 on Wed so if possible could you review this PR before? If not then I think we'll wait and release it with 1.78 because it's bigger than the simple fixes I already merged.  
  
Sorry I missed this earlier. It's probably too late for 1.77 now anyway. I will approve it.

---

## Review 5 [Approved]

> Username: barendgehrels  
> Created_at: 2021-07-21 11:08:56 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/881#pullrequestreview-711531315  

Thanks

---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-07-21 14:51:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/881#issuecomment-884252486  

@barendgehrels Technically changes in tests are allowed even now without permission, but it's quite big so I think I'll wait for 1.78. Thanks!

---
