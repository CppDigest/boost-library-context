# #297 - Limits: the write buffer may surpass max_buffer_size [Closed]

> Username: anarthal  
> Created at: 2024-06-30 11:40:25 UTC  
> Updated at: 2024-07-26 09:46:33 UTC  
> Closed at: 2024-07-26 09:46:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/297  

In #278 we implemented a check to make write operations fail if they attempt to write packets bigger than max_buffer_size. However, this is checked after the buffer is resized. This yields the correct user-facing behavior, but doesn't actually constrain memory. The correct implementation is a little bit more involved, and should go together with #279.
