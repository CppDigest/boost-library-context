# #148 - [win32] crash with 0xC000000D in dll after exit() was called [Closed]

> Username: mika-fischer  
> Created at: 2023-11-27 14:49:15 UTC  
> Updated at: 2024-01-20 07:51:08 UTC  
> Closed at: 2024-01-20 07:51:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/148  

I have a nodejs addon (basically a dll), and want to print a stacktrace under some circumstances using `to_string(stacktrace())`. If this happens after nodejs calls exit(), I get a crash. It does not make a difference whether I use `BOOST_STACKTRACE_USE_WINDBG_CACHED` or not. What helps is to format a stacktrace before exit() is called.  
  
Maybe exit makes it impossible to load new dlls?  
  
<img width="296" alt="2023-11-27_14h51_24" src="https://github.com/boostorg/stacktrace/assets/426158/cbcc72c7-ccc4-4556-88aa-3d30e66ff339">  
  
<img width="1034" alt="2023-11-27_14h51_14" src="https://github.com/boostorg/stacktrace/assets/426158/4073e366-d010-4592-aca4-da83ba788d0b">  
  
<img width="691" alt="2023-11-27_14h51_18" src="https://github.com/boostorg/stacktrace/assets/426158/4d0210dd-1bcb-4f17-b2ab-723b6bea8190">

---

## Comment 1

> Username: apolukhin  
> Created at: 2023-11-28 14:44:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/148#issuecomment-1829989129  

Try calling stacktrace before the exit for first time. Probably after such initialization the stacktrace would work after exit

---

## Comment 2

> Username: mika-fischer  
> Created at: 2023-11-28 16:05:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/148#issuecomment-1830162805  

Yes, that works, that's what I meant by  
> What helps is to format a stacktrace before exit() is called.  
  
Still, it's a bit unfortunate that it just crashes otherwise...

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-01-20 07:51:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/148#issuecomment-1901887724  

I'm afraid that this is a Windows runtime limitation. I could do some initialization on program start, however this would make many other developers unhappy  
  
Closing as 'wont fix'
