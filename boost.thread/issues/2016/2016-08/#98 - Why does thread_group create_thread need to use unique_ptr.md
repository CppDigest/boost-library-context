# #98 - Why does thread_group create_thread need to use unique_ptr? [Closed]

> Username: JackDrogon  
> Created at: 2016-08-08 06:01:23 UTC  
> Updated at: 2016-09-06 18:14:41 UTC  
> Closed at: 2016-08-08 06:53:37 UTC  
> Url: https://github.com/boostorg/thread/issues/98  

Hi, I'm a newcomer to boost. When I read the code about boost thread, I found that thread_group's member function `create_thread` used unique_ptr. Why? Use unique_ptr to avoid `threads.push_back(new_thread.get());` out of memory error that caused not releasing the thread push_backed unsuccessful?

---

## Comment 1

> Username: viboes  
> Created at: 2016-08-08 06:53:33 UTC  
> Url: https://github.com/boostorg/thread/issues/98#issuecomment-238155369  

Exactly.
