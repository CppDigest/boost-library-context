# #1104 - Example in release needs boost/geometry/extensions which is not shipped [Closed]

> Username: sgn  
> Created at: 2022-12-17 03:26:02 UTC  
> Updated at: 2023-02-20 09:04:06 UTC  
> Closed at: 2023-02-20 09:04:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1104  

Hello,  
  
Can you either ship `boost/geometry/extensions` in the release or remove those examples from release?  
  
As of it is, the example are all useless because it needs  `boost/geometry/extensions`

---

## Comment 1

> Username: vissarion  
> Created at: 2022-12-19 14:52:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1104#issuecomment-1357784337  

Thanks for the report. The release you mean is version `1.81.0`. Indeed this is an issue that will be fixed in `1.82.0` (we cannot change/update the current release). See https://github.com/boostorg/geometry/pull/1105.
