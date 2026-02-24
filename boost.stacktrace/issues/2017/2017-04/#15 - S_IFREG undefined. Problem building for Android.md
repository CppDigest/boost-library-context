# #15 - S_IFREG undefined. Problem building for Android [Closed]

> Username: ustrobot  
> Created at: 2017-04-19 10:50:09 UTC  
> Updated at: 2017-04-22 06:02:47 UTC  
> Closed at: 2017-04-22 06:02:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/15  

S_IFREG flag is undefined for Android. It looks like the flag is intended to be used with **stat** but not with **open** and could be safely removed.  
  
[Code](https://github.com/apolukhin/stacktrace/blob/9c08e254f167223921c8122ad18afaced516a84d/include/boost/stacktrace/detail/safe_dump_posix.ipp#L36)

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-04-19 19:30:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/15#issuecomment-295403614  

You are 100% right. According to POSIX docs the third parameter for `open` must be a combination of permission flags. Otherwise the behavior is undefined.

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-04-22 06:02:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/15#issuecomment-296350658  

Thanks for the report. Issue is fixed.
