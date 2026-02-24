# #1061 - Consider storing a more direct source_location in error_codes [Open]

> Username: grisumbras  
> Created at: 2024-12-16 10:06:40 UTC  
> Updated at: 2024-12-16 10:06:40 UTC  
> Url: https://github.com/boostorg/json/issues/1061  

Currently error_code stores the location of the most inner code that sets the error (the place where the error is first discovered). But there's an argument, that it should instead store the topmost location where the error is discovered.  
  
Peter Dimov:  
> the point here is that in general, the location should point to the call that failed, not inside of it  
> if an OS API call fails, the location points to where it was issued, not to the OS source  
> here the call that fails is try_as_array  
> not something called inside of it
