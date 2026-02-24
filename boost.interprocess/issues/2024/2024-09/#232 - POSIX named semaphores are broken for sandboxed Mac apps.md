# #232 - POSIX named semaphores are broken for sandboxed Mac apps [Open]

> Username: JNMSFT  
> Created at: 2024-09-26 17:44:31 UTC  
> Updated at: 2024-10-15 16:13:45 UTC  
> Url: https://github.com/boostorg/interprocess/issues/232  

The code below in `interprocess/sync/posix/semaphore_wrapper.hpp` is broken for sandboxed Mac apps:  
  
```c++  
   std::string name;  
   #ifndef BOOST_INTERPROCESS_FILESYSTEM_BASED_POSIX_SEMAPHORES  
   add_leading_slash(origname, name);  
   #else  
   create_shared_dir_cleaning_old_and_get_filepath(origname, name);  
   #endif  
```   
  
For sandboxed apps, the name of the mutex must be < 32 characters and must be prefixed with an App Group the app is entitled to. For instance, "UBF9AAA6G9.Foo/Bar" is a valid mutex name, but "/UBF9AAA6G9.Foo/Bar" is not. However, `add_leading_slash` always prepends a slash, which causes sem_open to fail if the app is sandboxed.   
  
Is there an existing preprocessor macro I can define to skip this behavior? It didn't look like it from a quick read of the code.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-10-14 09:44:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/232#issuecomment-2410626171  

I don't know about MacOs sandboxed apps, but searching online it seems that we can detect a sandboxed application if the environment variable APP_SANDBOX_CONTAINER_ID exists. Can you confirm this? I don't know if the app group id can be obtained and Interprocess can just figure out the correct name...

---

## Comment 2

> Username: JNMSFT  
> Created at: 2024-10-14 13:58:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/232#issuecomment-2411354589  

The most foolproof way is probably going to be to test the code signing entitlements, as described here:  
  
https://forums.developer.apple.com/forums/thread/62939  
  
Outside of that, the environment variable you found is likely the best bet.  
  
That said, the issue here is that the semaphore needs to be created with an app group ID. Many apps can have multiple app groups, and the caller should be able to specify which one using the name of the semaphore. There’s an additional restriction in that the semaphore name cannot exceed 30 characters in length (in any circumstances, sandboxed or not).  
  
From: Ion Gaztañaga ***@***.***>  
Date: Monday, October 14, 2024 at 2:45 AM  
To: boostorg/interprocess ***@***.***>  
Cc: Jack Nichols ***@***.***>, Author ***@***.***>  
Subject: Re: [boostorg/interprocess] POSIX named semaphores are broken for sandboxed Mac apps (Issue #232)  
  
  
I don't know about MacOs sandboxed apps, but searching online it seems that we can detect a sandboxed application if the environment variable APP_SANDBOX_CONTAINER_ID exists. Can you confirm this? I don't know if the app group id can be obtained and Interprocess can just figure out the correct name...  
  
—  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/interprocess/issues/232#issuecomment-2410626171>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AEWIRY2RSWLWIB63GSQHVMTZ3OHCLAVCNFSM6AAAAABO5Q3QMOVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIMJQGYZDMMJXGE>.  
You are receiving this because you authored the thread.Message ID: ***@***.***>

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-10-14 22:47:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/232#issuecomment-2412466179  

I don't see any fully compilable example online for the purpose (what is that " code signing entitlements" stuff?) and I don't have access to a MacOs machine so I definitely would need a patch from someone that detects the sandboxed state under __apple__ or something and that avoids adding the first slash in that case:  
  
```  
   #ifndef BOOST_INTERPROCESS_FILESYSTEM_BASED_POSIX_SEMAPHORES  
   #ifdef __APPLE__  
   if (!is_sandboxed_app()  
      add_leading_slash(origname, name);  
   #else  
      add_leading_slash(origname, name);  
   #endif  
   #else  
   create_shared_dir_cleaning_old_and_get_filepath(origname, name);  
   #endif  
```  
  
But I imagine we'll have the same problem with shared memory (shm_open) and other utilities.

---

## Comment 4

> Username: JNMSFT  
> Created at: 2024-10-15 16:13:45 UTC  
> Url: https://github.com/boostorg/interprocess/issues/232#issuecomment-2414454452  

I think a better approach might be to just have a preprocessor definition for sandboxed behavior. I don’t think we can do this by default, but apps that wanted to opt-in to the correct behavior on macOS could #define BOOST_INTERPROCESS_APPLE_NOSLASH or something before including the Interprocess headers.  
  
I also agree that shm_open will need a similar fix. There may be others; I am not that familiar with the rest of the Interprocess code.  
  
From: Ion Gaztañaga ***@***.***>  
Date: Monday, October 14, 2024 at 3:47 PM  
To: boostorg/interprocess ***@***.***>  
Cc: Jack Nichols ***@***.***>, Author ***@***.***>  
Subject: Re: [boostorg/interprocess] POSIX named semaphores are broken for sandboxed Mac apps (Issue #232)  
  
  
I don't see any fully compilable example online for the purpose (what is that " code signing entitlements" stuff?) and I don't have access to a MacOs machine so I definitely would need a patch from someone that detects the sandboxed state under apple or something and that avoids adding the first slash in that case:  
  
   #ifndef BOOST_INTERPROCESS_FILESYSTEM_BASED_POSIX_SEMAPHORES  
   #ifdef __APPLE__  
   if (!is_sandboxed_app()  
      add_leading_slash(origname, name);  
   #else  
      add_leading_slash(origname, name);  
   #endif  
   #else  
   create_shared_dir_cleaning_old_and_get_filepath(origname, name);  
   #endif  
  
  
But I imagine we'll have the same problem with shared memory (shm_open) and other utilities.  
  
—  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/interprocess/issues/232#issuecomment-2412466179>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AEWIRY7ZMWFTWUIQCVUTLDDZ3RCYBAVCNFSM6AAAAABO5Q3QMOVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIMJSGQ3DMMJXHE>.  
You are receiving this because you authored the thread.Message ID: ***@***.***>
