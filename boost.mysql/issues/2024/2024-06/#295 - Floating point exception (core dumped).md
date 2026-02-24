# #295 - Floating point exception (core dumped) [Closed]

> Username: ezluo  
> Created at: 2024-06-27 13:56:02 UTC  
> Updated at: 2024-06-28 07:38:37 UTC  
> Closed at: 2024-06-28 07:38:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/295  

Execute the Update statement using connection_pool and receive the result. If the update is not successful (with correct statements), the unoptimized compiled GDB will debug and execute the next section of code, and the program will terminate abnormally（  
Floating point exception (core dumped)  
[1] + Floating point exception (core dumped) "/usr/bin/gdb"  
）.   
Successfully updated or not debugged, the program is running normally.  
﻿  
system:ubuntu 2404

---

## Comment 1

> Username: anarthal  
> Created at: 2024-06-27 14:12:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2194815924  

Hi @ezluo,  
  
Could you please provide a little bit more context so I can investigate?  
- Which update statement are you running?  
- Are you using text queries or prepared statements?  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: ezluo  
> Created at: 2024-06-27 21:41:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2195707357  

I use the prepared statements  
  
UPDATE ehsta SET `ta03`=1 WHERE ta01=?  
  
TA03 is of the bit type

---

## Comment 3

> Username: ezluo  
> Created at: 2024-06-27 21:54:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2195722900  

There is also an abnormal ending when the preparation quote is not used

---

## Comment 4

> Username: anarthal  
> Created at: 2024-06-27 21:57:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2195725760  

What type is `ta1`? How does your `execute` / `bind` code look like?  
  
> There is also an abnormal ending when the preparation quote is not used  
  
I'm afraid I'm not following you here. Could you please paste a code sample oh what you mean?  
  
Thanks,  
Ruben.

---

## Comment 5

> Username: ezluo  
> Created at: 2024-06-27 22:05:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2195743463  

This error occurs without using the preparation statement  
co_await conn->async_execute(  
                sql, result, diag, tuple_awaitable);  
            ec = std::get<0>(res);  
            make_runtime_error();  
This paragraph encapsulates a function,  
After the function ends, call  
if (! result.empty() && result.affected_rows() == 1)  
esle  
An exception occurred here

---

## Comment 6

> Username: ezluo  
> Created at: 2024-06-27 22:16:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2195757218  

The same exception occurs with or without SQL prepared statements.  
The same exception occurs when a result check is not performed or when a result check is not performed  
  
The type of Ta01 is a string

---

## Comment 7

> Username: ezluo  
> Created at: 2024-06-28 03:18:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2196030695  

This issue can be closed,  
Attempted to debug using lldb, executed normally, and the dump file also showed as gdb exception.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-06-28 07:38:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/295#issuecomment-2196325593  

Was it a gdb bug? Closing as suggested.
