# #491 Use perfect forwarding from apply_operation to visit [Merged]

> Username: sdebionne  
> Created at: 2020-05-06 19:16:12 UTC  
> Updated at: 2020-10-14 08:23:03 UTC  
> Merged at: 2020-06-16 22:35:09 UTC  
> Closed at: 2020-06-16 22:35:09 UTC  
> Url: https://github.com/boostorg/gil/pull/491  

### Description  
  
Add a couple of overloads of `apply_operation` for binary operations. This is necessary until boostorg/variant2#20 is implemented. At that point `apply_operation` won't be necessary anymore (or could perfect forward to `visit`).  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-05-06 21:38:33 UTC  
> Url: https://github.com/boostorg/gil/pull/491#issuecomment-624904907  

I'm not sure myself, but perhaps you have checked @sdebionne  if there is anything in the tests that calls these, is there?

---

## Comment 2

> Username: sdebionne  
> Created_at: 2020-05-07 06:38:57 UTC  
> Url: https://github.com/boostorg/gil/pull/491#issuecomment-625060766  

Since boostorg/variant2#20 is already implemented, I need to revisit this MR! I'll check the tests as well...

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-06-04 16:25:03 UTC  
> Url: https://github.com/boostorg/gil/pull/491#issuecomment-638963660  

@sdebionne Could you let me know if this is ready to review and merge. It's been a while and I might have missed any update

---

## Comment 4

> Username: sdebionne  
> Created_at: 2020-06-16 13:21:18 UTC  
> Url: https://github.com/boostorg/gil/pull/491#issuecomment-644760499  

@mloskot Sorry I forgot to update the state from draft to ready to review.

---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2020-06-16 22:33:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/491#pullrequestreview-431938159  

Thanks for this refactoring

---
