# #1818 - Premature end-of-file leads to infinite loop [Closed]

> Username: mika-fischer  
> Created at: 2020-01-23 11:36:29 UTC  
> Updated at: 2020-01-28 16:48:33 UTC  
> Closed at: 2020-01-28 16:48:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1818  

- All implementations of `file::read` communicate EOF by returning 0 and no error AFAICT.  
- [`basic_file_body<File>::writer::get`](https://github.com/boostorg/beast/blob/46ac848fa46ce5b81f2bb5b221774d15c0cdd128/include/boost/beast/http/basic_file_body.hpp#L324-L380) [only has a `BOOST_ASSERT`](https://github.com/boostorg/beast/blob/46ac848fa46ce5b81f2bb5b221774d15c0cdd128/include/boost/beast/http/basic_file_body.hpp#L361) that fails in the EOF case, but is ignored in release mode. In which case [an empty buffer and `more == true` is returned](https://github.com/boostorg/beast/blob/46ac848fa46ce5b81f2bb5b221774d15c0cdd128/include/boost/beast/http/basic_file_body.hpp#L376-L379).  
- This leads the serializer to try reading forever and never getting any data.  
- I'm not sure whether the `file_win32` specialization that uses `TransmitFile` has the same issue  
- I also don't know whether this (that a file size of an opened file might not be accurate or change over time) can happen on Windows at all.  
  
I would argue that encountering EOF before the expected size is reached to be an important runtime error that should be reported. An assertion is not enough.  
  
Premature EOF can always happen (at least on Linux) if the file is truncated after determining the file size. On Linux it's also possible to have files that report an incorrect size (e.g. 0 for pipes or 4K for sysfs files), and it might be nice to support those as well (or at the very least report an error instead of causing an infinite loop).

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-24 10:31:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1818#issuecomment-578075418  

This sequence can indeed occur. Good find.  
  
I have set up a branch to prove it and provide a resolution here:  
  
https://github.com/madmongo1/beast/tree/fix-issue-1818
