# #418 Only use console window size if `ioctl(TIOCGWINSZ)` succeeded. [Merged]

> Username: Lastique  
> Created at: 2025-11-26 13:22:37 UTC  
> Updated at: 2025-11-28 11:02:54 UTC  
> Merged at: 2025-11-28 10:08:55 UTC  
> Closed at: 2025-11-28 10:08:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/418  

This avoids potentially using uninitialized data if `ioctl` fails. Also, zero-initialize the `winsize` struct to begin with.

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-11-26 15:54:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/418#issuecomment-3582011187  

This fixes valgrind errors in `histogram_ostream_ascii_test`, when the test output is redirected to a file. Full valgrind log is below:  
  
[vg.log](https://github.com/user-attachments/files/23775283/vg.log)

---

## Comment 2

> Username: HDembinski  
> Created_at: 2025-11-28 10:09:19 UTC  
> Url: https://github.com/boostorg/histogram/pull/418#issuecomment-3588675700  

Thanks for investigating and providing a solution, that's awesome.

---
