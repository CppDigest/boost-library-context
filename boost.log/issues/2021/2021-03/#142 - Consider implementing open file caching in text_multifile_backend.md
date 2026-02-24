# #142 - Consider implementing open file caching in text_multifile_backend [Open]

> Username: Lastique  
> Created at: 2021-03-04 16:12:25 UTC  
> Updated at: 2021-03-04 16:12:25 UTC  
> Url: https://github.com/boostorg/log/issues/142  

It was [reported](https://lists.boost.org/Archives/boost/2021/03/250968.php) that on Windows file open and close operations are much more expensive than on other systems, e.g. Linux. This affects `text_multifile_backend`, which opens and closes a file on every log record.  
  
Consider implementing a cache of the most recently used files, so that the amount of open/close operations is reduced. The cached entries should have a timeout as well, so that the file is eventually closed if it is not used for too long.
