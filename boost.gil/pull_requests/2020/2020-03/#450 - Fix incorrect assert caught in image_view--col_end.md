# #450 Fix incorrect assert caught in image_view::col_end  [Merged]

> Username: simmplecoder  
> Created at: 2020-03-15 09:23:21 UTC  
> Updated at: 2020-03-15 16:15:01 UTC  
> Merged at: 2020-03-15 16:14:35 UTC  
> Closed at: 2020-03-15 16:14:35 UTC  
> Url: https://github.com/boostorg/gil/pull/450  

### Description  
  
The commit changes assert condition  
to temporary fix the problem. There are  
also very basic tests that will check  
for regression.  
  
### References  
  
Fix for #432   
  
### Tasklist  
  
- [X] Change assert condition  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-03-15 16:11:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/450#pullrequestreview-374812456  

I'm happy to accept this. I will look into any other spots that may need the assert to be refined. I will also update the tests. Thanks.

---
