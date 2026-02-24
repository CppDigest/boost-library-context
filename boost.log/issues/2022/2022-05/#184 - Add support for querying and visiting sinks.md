# #184 - Add support for querying and visiting sinks [Open]

> Username: ropieur  
> Created at: 2022-05-16 17:19:47 UTC  
> Updated at: 2022-05-18 16:16:18 UTC  
> Url: https://github.com/boostorg/log/issues/184  

Hi,  
I would need to allow my application to provide to the end user with a dynamic control on sinks configured in settings file (typically, change the filtering at runtime).  
For that, I would need to have to list of the configured sinks, but from my searches results on the web, it looks like there is no such a support.  
  
Would it be possible to add the support for querying and visiting the sinks?  
If not, what alternative solution would you suggest (if possible, without having to reimplement setup file parser)?  
  
Thanx in advance

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-16 17:37:23 UTC  
> Url: https://github.com/boostorg/log/issues/184#issuecomment-1127951181  

You are right, there is currently no way to discover the registered sinks after initializing the logging library. I would like to rectify this, but it's not clear how to do this to make it a useful feature.  
  
Sinks, formatters and filters that are registered in the core with erased types. For example, the core operates on sinks through a pointer to `sink` interface, which is useful for feeding log records but not much else. The core, or the setup helper function, cannot produce a pointer to the actual sink that derives from this interface, like `synchronous_sink<text_file_backend>` for instance. It could produce a pointer to `sink`, which would require the user to use whatever means to discover the actual type of the sink, which doesn't look very useful.

---

## Comment 2

> Username: ropieur  
> Created at: 2022-05-18 16:11:19 UTC  
> Url: https://github.com/boostorg/log/issues/184#issuecomment-1130215441  

Hi @Lastique ,  
I guess that this kind of extension will not pop up in a short term. Do you have suggestions how I could proceed meanwhile?

---

## Comment 3

> Username: Lastique  
> Created at: 2022-05-18 16:16:18 UTC  
> Url: https://github.com/boostorg/log/issues/184#issuecomment-1130220310  

One workaround would be to register your own sink factories that will remember the sinks they create.
