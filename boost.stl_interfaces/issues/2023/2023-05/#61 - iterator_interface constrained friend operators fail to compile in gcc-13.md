# #61 - iterator_interface constrained friend operators fail to compile in gcc-13 [Closed]

> Username: ldalessa  
> Created at: 2023-05-07 00:56:12 UTC  
> Updated at: 2023-05-07 05:04:43 UTC  
> Closed at: 2023-05-07 05:04:42 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/61  

The 1.82.0 iterator_interface fails to compile in gcc-13 and trunk. You can see that in this [godbolt example](https://godbolt.org/z/91jfbs3hE). The constraints have changed since 1.82.0, and I can't test them directly because trunk doesn't seem to be available on godbolt, but I expect the same problem.  
  
I reported this as a gcc-13 regression, but was told that gcc is correct and that the construction isn't valid.  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=109751  
  
I believe that the fix is to use template member functions, not template friends, for the operators that are being constrained based on `D`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2023-05-07 03:17:15 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/61#issuecomment-1537277887  

Thanks For reporting this, and for such a nicely reduced example!  
  
I don't have easy access to GGC13 right now.  Could you try this with tip of the `develop` branch, at https://github.com/boostorg/stl_interfaces?  The constraint has changed (it was nonsensical, as pointed out in the GCC bug thread :).  The change might make things work.  
  
Zach

---

## Comment 2

> Username: ldalessa  
> Created at: 2023-05-07 05:04:42 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/61#issuecomment-1537292397  

If I paste the header into the godbolt code from above [it seems happier](https://godbolt.org/z/1fb8cr6r9). So this seems fixed in the trunk code. Sorry for the false alarm.
