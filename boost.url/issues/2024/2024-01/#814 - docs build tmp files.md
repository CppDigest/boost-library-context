# #814 - docs build tmp files [Closed]

> Username: sdarwin  
> Created at: 2024-01-18 00:45:17 UTC  
> Updated at: 2024-02-09 18:07:01 UTC  
> Closed at: 2024-02-09 18:07:01 UTC  
> Url: https://github.com/boostorg/url/issues/814  

`./build_antora.sh` calls `doc/generate-files.mjs` which generates a random tmp directory such as this:    
  
```  
~/tmpfvSvBg:  
  MrDocs  
  MrDocs-0.0.1-Linux.tar.gz  
  boost  
```  
That tmp directory is 2GB in size. Every time `./build_antora.sh` runs it uses another 2 GB, eventually filling up all disk space.    
  
Solutions:  
  
1. `generate-files.mjs` creates, and knows the name, of the tmp dir. Have it delete the tmp dir at the end of the script.  Probably the best choice.  
  
2. Or always use the same location every time.  If the directory is named "url-tmp-dir" it will use 2 GB of space, but only 1 time.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-01-18 14:06:19 UTC  
> Url: https://github.com/boostorg/url/issues/814#issuecomment-1898544888  

I thought the machine was ephemeral. I'll delete the tmp dir in #792.

---

## Comment 2

> Username: sdarwin  
> Created at: 2024-01-18 14:47:14 UTC  
> Url: https://github.com/boostorg/url/issues/814#issuecomment-1898620307  

> I'll delete the tmp dir in https://github.com/boostorg/url/pull/792.  
  
Agreed.    
  
Out of curiosity, a third option:  if the tmp dir is located immediately in the workspace folder rather than outside of it in  `${HOME}/tmpdir`, then I could clear the workspace on every job run which also solves the problem.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-01-18 22:01:00 UTC  
> Url: https://github.com/boostorg/url/issues/814#issuecomment-1899291097  

I already applied the change in #792, which is the PR that's creating the antora previews for now and the one that will be merged. Old temp dirs might need to be deleted manually though.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-02-09 18:07:01 UTC  
> Url: https://github.com/boostorg/url/issues/814#issuecomment-1936380954  

Fixed in d1538dc384156057d3706fa6fb8a62e3fd74f8bf
