# #88 - Multiple core instances [Closed]

> Username: henrikedin  
> Created at: 2019-06-26 16:35:57 UTC  
> Updated at: 2020-09-08 13:11:39 UTC  
> Closed at: 2020-09-08 13:11:39 UTC  
> Url: https://github.com/boostorg/log/issues/88  

Would it be possible to support multiple instances of the logging core? We are looking into boost::log at MongoDB and we have a use case where we want complete isolation between two separate log systems. So separate log statements in user code and separate backend sinks where the logs are written.   
  
I know you can use filtering to achieve this, with some meta attribute value that decides which backend(s) should consume the log. But I find that solution messy as they need to be aware of each other, you’d also have a bunch of sinks considered for logging unnecessarily.  
  
By looking at the source code, it seems like the building blocks for this are already in place. The sources::basic_logger already store a pointer to its core and if I’m not mistaken it would be a matter of exposing a way to create additional core instances and overloads to the basic_logger’s constructor that take a core as a parameter.  
  
What do you think? Am I missing a better way to achieve isolation than using filtering? I can work on this and send you a PR if you think it is a good idea.  
  
Thanks

---

## Comment 1

> Username: Lastique  
> Created at: 2019-06-26 17:03:41 UTC  
> Url: https://github.com/boostorg/log/issues/88#issuecomment-505961785  

Filtering is indeed the only way to achieve this currently.  
  
Loggers are not the only place that use the logging core (and assume it is a singleton). There are facilities like [scoped attributes](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/detailed/attributes.html#log.detailed.attributes.related_components.scoped_attributes) and the whole set of initialization functions that assume the global core. Then there are singletons besides the logging core, like [named scopes](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/detailed/attributes.html#log.detailed.attributes.named_scope), [global storage for loggers](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/detailed/sources.html#log.detailed.sources.global_storage), cache for [attribute names](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/detailed/attributes.html#log.detailed.attributes.related_components.attribute_name), factories for [filters, formatters and sinks](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/extension/settings.html). Then there is trivial logging, which must be usable with exactly zero configuration. Maybe there's more. You can probably find most of it by searching `singleton`.  
  
Given the above, I don't think it is possible to eliminate global state. It is probably possible to allow creating that global state (including the logging core and everything else) and then provide that instance to every component you're using, taking great care to not forget that so that the default global state is not used instead. I'm not sure how useful that would be, but implementing this would be a rather large task. I'm not planning doing this, but if you want you can look into it. If you do, my primary requirement would be to keep existing interfaces available so that users don't have to rewrite their code.

---

## Comment 2

> Username: henrikedin  
> Created at: 2019-06-26 17:21:03 UTC  
> Url: https://github.com/boostorg/log/issues/88#issuecomment-505968060  

Thanks for your reply. Keeping the global singleton and allowing for additional instances is the way to achieve this so no current user code would have to be re-written.  
  
If I look into doing this, would you expect a complete solution with all features of this library supported? Or would you be OK with a subset and potentially making it complete over time?

---

## Comment 3

> Username: Lastique  
> Created at: 2019-06-26 17:25:26 UTC  
> Url: https://github.com/boostorg/log/issues/88#issuecomment-505969643  

I'd prefer to have a complete solution. Users tend to expect everything to just work, it's difficult to explain which parts work and which don't.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-09-08 13:11:39 UTC  
> Url: https://github.com/boostorg/log/issues/88#issuecomment-688856020  

I'm going to close this as I don't plan to implement this feature. Feel free to create PRs if you have some code.
