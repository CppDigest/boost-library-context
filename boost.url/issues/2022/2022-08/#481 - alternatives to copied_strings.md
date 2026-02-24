# #481 - alternatives to copied_strings [Closed]

> Username: vinniefalco  
> Created at: 2022-08-31 00:37:35 UTC  
> Updated at: 2022-09-08 21:22:43 UTC  
> Closed at: 2022-09-08 21:22:43 UTC  
> Url: https://github.com/boostorg/url/issues/481  

1. keep the previous buffer alive in an RAII local in the caller on a realloc  
2. make a utility string_ref that can either be absolute or relative, and gets notifications on memmove

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-02 17:23:07 UTC  
> Url: https://github.com/boostorg/url/issues/481#issuecomment-1235743515  

All done except for segments

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-08 21:22:43 UTC  
> Url: https://github.com/boostorg/url/issues/481#issuecomment-1241247671  

done with segments now.
