# #121 - ISO-2022-JP as well as some other charsets not supported on Windows [Closed]

> Username: mbektchiev  
> Created at: 2022-10-20 15:28:51 UTC  
> Updated at: 2022-10-24 07:26:04 UTC  
> Closed at: 2022-10-23 12:09:19 UTC  
> Url: https://github.com/boostorg/locale/issues/121  

According to [MultiByteToWideChar's docs](https://learn.microsoft.com/en-us/windows/win32/api/stringapiset/nf-stringapiset-multibytetowidechar) the flags parameter has to be 0 for some code pages:  
> For the code pages listed below, dwFlags must be set to 0. Otherwise, the function fails with `ERROR_INVALID_FLAGS`.  
>  
>50220  
>50221  
>50222  
>50225  
>50227  
>50229  
>57002 through 57011  
>65000 (UTF-7)  
>42 (Symbol)  
  
Because of this the following code snippet returns an empty string instead of the expected `冬季`:  
  
```C++  
    std::string res = boost::locale::conv::to_utf<char>("\xe5\x86\xac\xe5\xad\xa3", "iso-2022-jp");  
```  
  
My suggestion for a fix would be to check whether the code page we're dealing with is from this short-list and not add the flag if it is. This should be done in all calls to `MultiByteToWideChar` like here: [wconv_codepage.ipp#L100](https://github.com/boostorg/locale/blob/37137d1dde97df7fc3a93d3728bea386a9448b77/src/boost/locale/encoding/wconv_codepage.ipp#L100).

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-10-22 10:52:39 UTC  
> Updated at: 2022-10-22 11:01:57 UTC  
> Url: https://github.com/boostorg/locale/issues/121#issuecomment-1287759682  

I just tested your example and the provided string is not valid "iso-2022-jp" encoding. ICU converts that string to an empty string too as the character is invalid. Converting 冬季 to iso-2022-jp via ICU yields "\x1b$BE_5(\x1b(B"  
  
I assume you got the direction mixed up as the UTF-8 encoding of 冬季 is "\xe5\x86\xac\xe5\xad\xa3", so you might have wanted to use `from_utf`  
  
I assume you work on windows without ICU?

---

## Comment 2

> Username: mbektchiev  
> Created at: 2022-10-24 07:25:51 UTC  
> Updated at: 2022-10-24 07:26:04 UTC  
> Url: https://github.com/boostorg/locale/issues/121#issuecomment-1288538109  

Hi @Flamefire! Thanks a lot for fixing this! 🥳   
  
> I just tested your example and the provided string is not valid "iso-2022-jp" encoding. ICU converts that string to an empty string too as the character is invalid. Converting 冬季 to iso-2022-jp via ICU yields "\x1b$BE_5(\x1b(B"  
>  
> I assume you got the direction mixed up as the UTF-8 encoding of 冬季 is "\xe5\x86\xac\xe5\xad\xa3", so you might have wanted to use `from_utf`.  
  
The direction was right, but I must have wrongly copied the UTF-8 encoding... 😊  
  
> I assume you work on windows without ICU?  
  
That's right.
