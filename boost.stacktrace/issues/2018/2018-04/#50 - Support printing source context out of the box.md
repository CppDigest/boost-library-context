# #50 - Support printing source context out of the box [Open]

> Username: ArekPiekarz  
> Created at: 2018-04-14 16:04:33 UTC  
> Updated at: 2018-10-09 19:03:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/50  

I believe currently the biggest "competitor" to Boost.Stacktrace is [Backward-cpp](https://github.com/bombela/backward-cpp). Its main strength is automatic printing of source code context for every frame in the stack trace.  
  
That difference doesn't seem like much at a glance, but it changes the way you develop your programs. You no longer have to manually copy-paste locations from the trace to check what code was in there. Of course the users can automate this functionality themselves, but it would lead to a lot of duplication across projects.  
  
The only downside of Backward-cpp that I noticed so far is lack of support for Windows. Boost.Stacktrace already works there, so if this feature request  was implemented, it could improve popularity of this library.  
  
If you are more interested in pushing Boost.Stacktrace as a standard, the proposed feature could also be included in the paper.  
  
Thanks in advance for your consideration.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-10-09 19:03:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/50#issuecomment-428312028  

Not shure that I'll have time to implement that any time soon. So PR are wellcomed
