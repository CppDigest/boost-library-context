# #11 - support for SOURCE_DATE_EPOCH in quickbook [Closed]

> Username: mhekkel  
> Created at: 2021-09-04 07:37:19 UTC  
> Updated at: 2021-09-07 09:12:44 UTC  
> Closed at: 2021-09-07 09:12:44 UTC  
> Url: https://github.com/boostorg/boostbook/issues/11  

I'm a happy user of boostbook and quickbook to generate the documentation for my software. This software is also part of Debian and there I run into the problem that my packages are not reproducible, due to the fact that quickbook writes out the current date as a last-revised string.  
The solution to this is really very simple, patch quickbook to pick up the value of the environment variable SOURCE_DATE_EPOCH and use that instead of time() as the last-revised date.  
  
The patch might look like this:  
  
```  
diff tools/quickbook/src/quickbook.cpp tools/quickbook/src/quickbook-n.cpp  
469c469,473  
<             time_t t = std::time(0);  
---  
>             time_t t;  
>                       if (getenv("SOURCE_DATE_EPOCH"))  
>                               t = strtol(getenv("SOURCE_DATE_EPOCH"), nullptr, 10);  
>                       else  
>                               t = std::time(0);  
  
```  
More information on reproducible builds can be found at https://reproducible-builds.org/ and https://wiki.debian.org/ReproducibleBuilds/Howto  
  
Hope this can be fixed.  
  
-maarten

---

## Comment 1

> Username: mhekkel  
> Created at: 2021-09-07 09:12:44 UTC  
> Url: https://github.com/boostorg/boostbook/issues/11#issuecomment-914134539  

Sorry, wrong project. Should have been quickbook of course.  
Submitted a pull request at https://github.com/boostorg/quickbook/pull/12
