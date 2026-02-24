# #48 add appveyor and travis ci [Closed]

> Username: jeking3  
> Created at: 2017-09-15 05:05:47 UTC  
> Updated at: 2017-09-15 12:41:10 UTC  
> Closed at: 2017-09-15 12:41:10 UTC  
> Url: https://github.com/boostorg/winapi/pull/48  

Build results: https://github.com/jeking3/winapi/pull/1  
  
I don't have it exercising all of the available platform SDK versions, though I think that would be really sweet, I'm hoping the selection of compilers will exercise enough.  
  
The travis job is simple and just to make sure nobody broke linux somehow with a change.  
  
This fixes #46.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-09-15 11:55:01 UTC  
> Url: https://github.com/boostorg/winapi/pull/48#issuecomment-329760850  

This set of changes exercises different compilers but not all of the compile-time branches.  I am going to add more build jobs to test each of the BOOST_WINAPI_VERSION_ constants as well as BOOST_USE_WINDOWS_H to make sure we're testing the entire set of possible combinations.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-15 12:41:10 UTC  
> Url: https://github.com/boostorg/winapi/pull/48#issuecomment-329771546  

Closing this PR until I have it stable.

---
