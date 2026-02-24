# #911 - string view parameters are copied [Closed]

> Username: vinniefalco  
> Created at: 2017-11-27 13:23:17 UTC  
> Updated at: 2022-10-05 15:15:51 UTC  
> Closed at: 2022-10-05 15:15:51 UTC  
> Url: https://github.com/boostorg/beast/issues/911  

Docs should explain that all beast interfaces which take a string view as input will copy the data. This is particularly important for `basic_fields`.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-05 15:15:51 UTC  
> Url: https://github.com/boostorg/beast/issues/911#issuecomment-1268584327  

Should go without saying now that views are a very common pattern.
