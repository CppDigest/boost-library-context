# #1361 - __see_below__ in cleanup-type [Closed]

> Username: vinniefalco  
> Created at: 2018-12-09 22:36:09 UTC  
> Updated at: 2019-04-19 03:16:14 UTC  
> Closed at: 2019-04-19 03:16:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1361  

Need a way to do this  
```  
#if BOOST_BEAST_DOXYGEN  
template<class T>  
struct is_async_read_stream : std::integral_constant<bool, __see_below__>{};  
...  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-01-08 23:36:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1361#issuecomment-452491222  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-01-16 11:51:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1361#issuecomment-454751864  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-19 03:16:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1361#issuecomment-484754892  

This works now in the old xsl code, and we use the newer doxygen so that template aliases show up correctly.
