# #41 - Add enum_from_string overload that doesn't require the string to be null-terminated [Closed]

> Username: grisumbras  
> Created at: 2023-09-09 13:11:09 UTC  
> Updated at: 2023-09-09 16:25:28 UTC  
> Closed at: 2023-09-09 16:25:28 UTC  
> Url: https://github.com/boostorg/describe/issues/41  

The current API requires copying the string if it's a part of larger input.
