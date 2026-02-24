# #175 - correction in pragma directive [Closed]

> Username: drosinos  
> Created at: 2024-07-30 09:53:33 UTC  
> Updated at: 2024-09-13 09:51:00 UTC  
> Closed at: 2024-09-13 09:50:48 UTC  
> Url: https://github.com/boostorg/pfr/issues/175  

https://github.com/boostorg/pfr/blob/449bf360f7d7a945639710b0fbb670ffde70e772/include/boost/pfr/detail/fake_object.hpp#L52  
  
Minor, but I think this should be `diagnostic pop` instead of `diagnostic push`.  
  
Thanks for a great library, btw!

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-13 09:50:58 UTC  
> Url: https://github.com/boostorg/pfr/issues/175#issuecomment-2348529138  

Many thanks for the report!
