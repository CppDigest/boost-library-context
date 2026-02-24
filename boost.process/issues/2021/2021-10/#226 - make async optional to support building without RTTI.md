# #226 - make async optional to support building without RTTI [Open]

> Username: ghost  
> Created at: 2021-10-29 05:03:27 UTC  
> Updated at: 2021-10-29 05:38:02 UTC  
> Url: https://github.com/boostorg/process/issues/226  

currently, RTTI is required to build, but it seems this is inherited from asio.  
  
It would be great if there was an option, for users who aren't using async (or want to handle async some other way) to disable this, so that building without RTTI will continue to work.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2021-10-29 05:38:02 UTC  
> Url: https://github.com/boostorg/process/issues/226#issuecomment-954444302  

What's the use-case and what is the error you're getting?
