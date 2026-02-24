# #16 - I tried to send a win32 SendMessage with a boost::tuples::tuple variable pointed by the lParam of the message. It does not work. When doing the same with a std::tuple it does works. Can anybody explain me why? [Closed]

> Username: klasing  
> Created at: 2020-08-23 11:42:22 UTC  
> Updated at: 2020-10-09 14:04:37 UTC  
> Closed at: 2020-10-09 14:04:37 UTC  
> Url: https://github.com/boostorg/tuple/issues/16  



---

## Comment 1

> Username: djowel  
> Created at: 2020-10-08 23:56:51 UTC  
> Url: https://github.com/boostorg/tuple/issues/16#issuecomment-705888521  

Please post an MVCE

---

## Comment 2

> Username: klasing  
> Created at: 2020-10-09 10:29:21 UTC  
> Url: https://github.com/boostorg/tuple/issues/16#issuecomment-706103047  

First, MVCE stands for Microsoft Visual C++ Example??? Second, I'm not so sure anymore the problem, I described, had to do with boost::tuples::tuple. As far as I know I'm now sending (boost) tuples within Win32 Messages, pointed by LPARAM, without any trouble. I would have to go through my codebase to find a proof of it. But this is not worth the effort, considering all the other things I have to do. Anyway, thanks for your reply!!! greetz
