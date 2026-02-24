# #388 - Improve documentation for system::default_device() [Open]

> Username: kylelutz  
> Created at: 2014-12-29 19:14:53 UTC  
> Updated at: 2017-03-21 04:14:47 UTC  
> Url: https://github.com/boostorg/compute/issues/388  

Add better documentation for the `system::default_device()` method and indicate how it uses the environmental variables and what happens when no device is found.  
  
From the review:  
  
```  
What does default_device do if there are no devices?  Do the various  
environment variables mentioned have any kind of precedence?  (Probably  
linking to the corresponding OpenCL docs is sufficient for such  
questions).  Do they have to be exact matches?  Are they case-sensitive?  
```  
##
