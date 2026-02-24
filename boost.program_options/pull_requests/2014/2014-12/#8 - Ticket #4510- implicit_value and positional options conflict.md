# #8 Ticket #4510: implicit_value and positional options conflict [Closed]

> Username: mjdecker  
> Created at: 2014-12-30 00:27:03 UTC  
> Updated at: 2014-12-30 15:16:42 UTC  
> Closed at: 2014-12-30 15:16:42 UTC  
> Url: https://github.com/boostorg/program_options/pull/8  

Add a has_implicit_value method to check if an implicit_value was set.  This is used to avoid processing positional arguments for options with implicit_value.  
  
The change seems simple enough, however, I am not nearly as familiar with the library, and I could not build this using the git repo to test it, as there seems to be some problem with building all the boost libraries on the main git repo.  However, I did test it by applying the changes to the latest release of boost (1.57.0) and it seems to work fine.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-12-30 07:17:42 UTC  
> Url: https://github.com/boostorg/program_options/pull/8#issuecomment-68335603  

Michael,  
  
thanks for the patch. I've pushed it, though I've renamed the method to be 'adjacent_tokens_only' - so that value_semantics interface does not mention the notion of 'implicit' which is only present in typed_value right now. I've also reformatted the commit message to follow standard git commit message guidelines.  
  
Any chance you'll modify tests to check for this new behaviour, so that it does not accidentally break in future?

---

## Comment 2

> Username: mjdecker  
> Created_at: 2014-12-30 15:11:19 UTC  
> Url: https://github.com/boostorg/program_options/pull/8#issuecomment-68363810  

I can take a look at the tests.

---
