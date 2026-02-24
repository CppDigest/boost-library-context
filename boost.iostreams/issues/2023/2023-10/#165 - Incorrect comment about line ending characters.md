# #165 - Incorrect comment about line ending characters [Closed]

> Username: itsff  
> Created at: 2023-10-08 22:20:25 UTC  
> Updated at: 2023-10-27 15:22:48 UTC  
> Closed at: 2023-10-27 15:22:48 UTC  
> Url: https://github.com/boostorg/iostreams/issues/165  

The comment in the `newline.hpp` incorrectly states that POSIX uses `CR` for line endings rather than `LF`, and that legacy Mac uses `LF`. This can confuse users.
