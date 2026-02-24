# #193 - CI issues in date_time [Closed]

> Username: JeffGarland  
> Created at: 2021-05-02 16:01:22 UTC  
> Updated at: 2021-05-03 00:32:54 UTC  
> Closed at: 2021-05-03 00:32:54 UTC  
> Url: https://github.com/boostorg/date_time/issues/193  

The last few commits have had CI issues. Including this one:  
  
https://github.com/boostorg/smart_ptr/issues/87  
  
Which looks like a platform std lib in compatibility. Updating the CI setup to use newer ubuntu.  Update the CI configs to try and get back to clean.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-05-03 00:32:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/193#issuecomment-830970316  

fixed for now -- still needs some additional cleanup
