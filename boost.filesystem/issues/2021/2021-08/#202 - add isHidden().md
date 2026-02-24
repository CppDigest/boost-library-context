# #202 - add isHidden() [Closed]

> Username: ChungZH  
> Created at: 2021-08-10 11:19:23 UTC  
> Updated at: 2021-08-10 12:48:24 UTC  
> Closed at: 2021-08-10 12:48:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/202  

Add `isHidden()` to know if the file is hidden.

---

## Comment 1

> Username: ChungZH  
> Created at: 2021-08-10 11:21:11 UTC  
> Updated at: 2021-08-10 11:21:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/202#issuecomment-895947044  

Many libraries support this, such as [poco](https://pocoproject.org/docs/Poco.File.html#14597), [Qt](https://doc.qt.io/qt-5/qfileinfo.html#isHidden)

---

## Comment 2

> Username: Lastique  
> Created at: 2021-08-10 12:39:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/202#issuecomment-895993170  

Boost.Filesystem is mostly modeled on POSIX and on POSIX systems there is no "hidden" attribute, it's just a file naming convention. For example, there would be no way to represent it in `file_status`, on which all `is_*` functions rely.

---

## Comment 3

> Username: ChungZH  
> Created at: 2021-08-10 12:48:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/202#issuecomment-895998957  

I see. Thank you.
