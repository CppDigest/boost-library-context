# #266 Remove linking with Boost.System [Merged]

> Username: Lastique  
> Created at: 2019-01-14 17:21:55 UTC  
> Updated at: 2019-01-20 11:29:05 UTC  
> Merged at: 2019-01-16 21:59:31 UTC  
> Closed at: 2019-01-16 21:59:31 UTC  
> Url: https://github.com/boostorg/thread/pull/266  

Since Boost.System is now header-only, no need to link with the library.

---

## Comment 1

> Username: viboes  
> Created_at: 2019-01-15 21:15:23 UTC  
> Url: https://github.com/boostorg/thread/pull/266#issuecomment-454554118  

Humm, what happens if a User wants to have Boost.Thread linked with Boost.System?  
  
BTW, Boost.Chrono is also header only, so shouldn't Boost.Thread remove also the link with Boost.Chrono if we apply your logic?  
  
I believe that at the end it is up to the user to select his configuration, isn't it?  
  
I would appreciate a patch of the build system that is able to configure Boost.Thread/Boost.Chrono so that it can be build either linking with Boost.Chrono/Boost.System or not.

---

## Comment 2

> Username: viboes  
> Created_at: 2019-01-15 21:22:46 UTC  
> Url: https://github.com/boostorg/thread/pull/266#issuecomment-454556467  

Oh, I see that Boost.Thread link conditionaly with Boost.Chrono depending on whether we are building with threadapi=pthread or not.  
  
I believe thsi should be independent of the value of threadapi.  
  
How other non-header only libraries are configured in Boost?  
Which ones depend on other Boost libraries?  
Is there something that it is agreed on in Boost?

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-01-16 00:06:04 UTC  
> Url: https://github.com/boostorg/thread/pull/266#issuecomment-454599675  

Maybe you misunderstood me. Boost.System is unconditionally header-only now, there's no config option to make it otherwise. Its compiled library is empty and it only exists to avoid breaking dependent libraries and applications. Given this, why would anyone want to link with it or have a config option in Boost.Thread? What is your reason in keeping the linking dependency?  
  
I'm not sure what's the situation with Boost.Chrono, this PR is not about it. However, IMHO, if Boost.Chrono can be (or is) header-only without sacrificing its features or other major compromises, I don't see why it shouldn't be.

---

## Comment 4

> Username: viboes  
> Created_at: 2019-01-16 21:57:55 UTC  
> Url: https://github.com/boostorg/thread/pull/266#issuecomment-454959615  

Sorry, I was not aware that Boost.System had nothing inside the binary library now.  
This change completely the context.

---
