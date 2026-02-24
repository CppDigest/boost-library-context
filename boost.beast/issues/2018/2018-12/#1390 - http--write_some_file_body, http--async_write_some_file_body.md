# #1390 - http::write_some_file_body, http::async_write_some_file_body [Open]

> Username: vinniefalco  
> Created at: 2018-12-26 20:48:30 UTC  
> Updated at: 2019-01-26 01:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1390  

We need dedicated interface like `async_write_some_file` which calls the natively-enhanced algorithms (such as using `::TransmitFile` on Windows, or `::sendfile` on POSIX). This will eliminate users' uncertainty of whether or not the enhanced algorithm will be used. These functions guarantee the algorithms will be used, unless the native enhancement is not available on the target platform.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-01-26 01:04:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1390#issuecomment-457785637  

This issue has been open for a while with no activity, has it been resolved?
