# #484 - FieldsReader::get(error_code&)? [Closed]

> Username: vinniefalco  
> Created at: 2017-06-13 14:53:01 UTC  
> Updated at: 2017-06-20 01:49:36 UTC  
> Closed at: 2017-06-20 01:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/484  

Should **FieldsReader** get take an error code?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-20 01:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/484#issuecomment-309621336  

I'm going to go with no for now. This sort of thing really shouldn't fail and if it does it can use exceptions just like a dynamic buffer does.
