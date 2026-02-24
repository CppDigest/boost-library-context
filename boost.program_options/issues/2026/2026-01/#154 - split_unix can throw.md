# #154 - split_unix can throw [Closed]

> Username: gnammix  
> Created at: 2026-01-22 10:52:09 UTC  
> Updated at: 2026-02-19 19:57:34 UTC  
> Closed at: 2026-02-19 19:57:34 UTC  
> Url: https://github.com/boostorg/program_options/issues/154  

Hello, I'm not sure if this intended, so it is just documentation error (but tbh documentation is bit vague on exception in general), or not, and so is missing catch.  
Anyway split_unix does use boost::tokenizer that can throw in some cases.  
Minimal example for it is:  
  
`  
boost::program_options::split_unix("\\");  
`

---

## Comment 1

> Username: vprus  
> Created at: 2026-01-22 11:37:51 UTC  
> Url: https://github.com/boostorg/program_options/issues/154#issuecomment-3783935638  

Hi, and thanks for submitting the issue.  
  
In general, every method/function of the library can throw; there are no methods that are explicitly promised to be non-throw.  
  
Can you point to any place in docs or example that might give other impression?

---

## Comment 2

> Username: gnammix  
> Created at: 2026-01-22 12:48:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/154#issuecomment-3784225721  

Fair enough, kind of assumed the dual; the, kind?, of exceptions thrown to be mentioned in the documentation or at the least mention that every function can throw.  
I suppose this can be closed then.
