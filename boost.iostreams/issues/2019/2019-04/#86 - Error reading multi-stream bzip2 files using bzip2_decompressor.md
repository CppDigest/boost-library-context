# #86 - Error reading multi-stream bzip2 files using bzip2_decompressor [Closed]

> Username: gfrontera  
> Created at: 2019-04-23 11:17:27 UTC  
> Updated at: 2019-05-24 12:29:56 UTC  
> Closed at: 2019-05-24 12:29:56 UTC  
> Url: https://github.com/boostorg/iostreams/issues/86  

Some tools generate multi-stream bzip2 files in which several bzip2 data streams are concatenated end-to-end. Using the [bzip2 executable](http://www.bzip.org/) extracts the file correctly. However, using the example code from [the docs](https://www.boost.org/doc/libs/1_68_0/libs/iostreams/doc/classes/bzip2.html#examples) does not extract the whole content of the file.  
  
The first call to `decompress()` after the stream is re-initialized (after finishing reading the first stream) returns `BZ_DATA_ERROR_MAGIC`. Then, the `bzip2_error::check(result)` throws an exception, and the following output is provided:  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::iostreams::bzip2_error> >'  
  what():  bzip2 error: iostream error  
```  
  
The file does contain the magic bytes at the beginning of each stream.

---

## Comment 1

> Username: gfrontera  
> Created at: 2019-04-23 17:33:30 UTC  
> Url: https://github.com/boostorg/iostreams/issues/86#issuecomment-485900751  

I have found that the `boost::iostreams::detail::bzip2_decompressor_impl::filter(i1, i2, o1, o2, flush)` method does not meet the [postcondition](https://www.boost.org/doc/libs/1_70_0/libs/iostreams/doc/index.html) requiring that either i1 == i2 or o1 == o2. This causes the failure when reading a multi-stream bzip2 file.
