# #1356 - Return errc::bad_file_descriptor instead of errc::invalid_argument in file_posix [Closed]

> Username: djarek  
> Created at: 2018-12-07 14:55:32 UTC  
> Updated at: 2018-12-13 13:55:28 UTC  
> Closed at: 2018-12-13 13:55:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1356  

Operations that fail because the file descriptor is not valid, should fail with `errc::bad_file_descriptor`, which is consistent with ASIO's handling of calling functions on an invalid file descriptor.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-07 18:37:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1356#issuecomment-445325069  

Yeah I can agree with that
