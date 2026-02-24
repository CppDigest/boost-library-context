# #370 - Crash in plumbum module when importing .so linked with gold [Open]

> Username: mmatrosov  
> Created at: 2021-07-27 16:48:10 UTC  
> Updated at: 2021-07-27 17:05:42 UTC  
> Url: https://github.com/boostorg/python/issues/370  

I am not sure it is boost.python's fault, since there are a lot of variables in the equation, but please take a look.  
  
I prepared the repro case and gathered some info in this repo: https://github.com/mmatrosov/plumbum-crash-repro  
  
In short, if you `import` an extension library in c++ which uses `boost.python`, compiles with `clang` and links with `gold`, there is a crash in this line https://github.com/tomerfiliba/plumbum/blob/master/plumbum/commands/processes.py#L286 during program shutdown. It does not happen if the extension is not loaded, and I've never experienced this crash even though plumbum is widely used in our scripts.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2021-07-27 17:00:13 UTC  
> Url: https://github.com/boostorg/python/issues/370#issuecomment-887677431  

For avoidance of doubt: the title mentions "when importing", but the actual text talks about "shutdown". Can you please clarify ? An actual stack trace would be useful.  
And please try to reduce your test case to enable others to reproduce it without having to download and build your own project first.

---

## Comment 2

> Username: mmatrosov  
> Created at: 2021-07-27 17:03:04 UTC  
> Updated at: 2021-07-27 17:05:42 UTC  
> Url: https://github.com/boostorg/python/issues/370#issuecomment-887679272  

The crash happens during the shutdown, but only when there is `import` of an .so. The stack traces are available in the linked repo. I reduced the repro as much as I could, it is hard not to build anything when you have bugs involving both c++ and python. And I hope anyone willing to reproduce would be able to build the repro with a single call to `build.py`. The only option I am aware of on how to simplify the repro even more is to provide a docker image. Please let me know if you think this is necessary.
