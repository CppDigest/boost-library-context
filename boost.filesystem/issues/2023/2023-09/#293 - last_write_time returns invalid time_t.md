# #293 - last_write_time returns invalid time_t [Closed]

> Username: Urmeli0815  
> Created at: 2023-09-20 15:08:17 UTC  
> Updated at: 2023-09-24 10:06:05 UTC  
> Closed at: 2023-09-23 22:25:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/293  

On Windows I have a file "abc.txt" with a modification timestamp `1601-01-01 00:00:00.0000001`. This is the lowest possible timestamp on Windows. When I call Win32-API `GetFileTime` on this file I get a `FILETIME` struct set with `dwLowDateTime=1` and `dwHighDateTime=0`.  
  
I now have this code:  
```  
boost::filesystem::path p{ R"(abc.txt)" };  
const auto pt = boost::filesystem::last_write_time(p);  
const auto d = boost::posix_time::from_time_t(pt).date(); // this throws an std::out_of_range exception with "Year is out of valid range: 1400..9999"  
```  
  
In the boost code I see that `last_write_time()` also calls `GetFileTime` and then calls `to_time_t(FILETIME)` which returns a `time_t`. The `to_time_t` function seems to be not correct as it uses `uint64_t` and looses the signedness. The `t -= 116444736000000000LL;` statement then underflows and produces the invalid `time_t`.  
  
When I change  
`uint64_t t = (static_cast< uint64_t >(ft.dwHighDateTime) << 32) | ft.dwLowDateTime;`  
to  
`int64_t t = (static_cast<int64_t>(ft.dwHighDateTime) << 32) | ft.dwLowDateTime;`  
the code works as expected.  
  
We're using `boost 1.83.0`

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-20 16:08:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/293#issuecomment-1728041687  

`time_t` is not required to be a signed type, which means only non-negative values are well defined. In other words, dates before January 1, 1970 (the POSIX time epoch) are basically unsupported and may fail to be represented.  
  
Yes, on Windows `time_t` is signed and the fix you posted appears to work (aside from the possible signed integer overflow, which is a problem in its own), but that fix will be incorrect if `time_t` is unsigned.

---

## Comment 2

> Username: Urmeli0815  
> Created at: 2023-09-20 17:03:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/293#issuecomment-1728123406  

Thanks for the fast answer!  
  
As `FILETIME` is a Windows-specific type wouldn't that be a viable solution for `to_time_t(FILETIME)`?  
  
It also seems that a signed integer overflow wouldn't be a problem as according to MSDN "a function using the FILETIME structure can allow for values outside of zero or positive values typically specified by the dwLowDateTime and dwHighDateTime members. For example, the SetFileTime function uses 0xFFFFFFFF to specify that a file's previous access time should be preserved". So for me it looks that it is sufficient to support max(int64) instead of max(uint64).  
  
Alternatively we would also be happy to add some error handling. But `last_write_time` just returns the invalid value without error reporting.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-09-23 22:27:06 UTC  
> Updated at: 2023-09-23 22:27:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/293#issuecomment-1732422887  

Thanks for the report, should be fixed now.

---

## Comment 4

> Username: Urmeli0815  
> Created at: 2023-09-24 10:06:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/293#issuecomment-1732536529  

Thank you for the fast fix!
