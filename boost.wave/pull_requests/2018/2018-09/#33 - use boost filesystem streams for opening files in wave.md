# #33 use boost filesystem streams for opening files in wave [Closed]

> Username: rlenhardt  
> Created at: 2018-09-28 14:46:29 UTC  
> Updated at: 2018-10-04 15:44:20 UTC  
> Closed at: 2018-10-04 15:44:20 UTC  
> Url: https://github.com/boostorg/wave/pull/33  

This is the PR for issue #32.  
The first commit changes standard streams to boost streams in the wave lib and the wave driver. I left out the samples.  
The second submit is a demo on how to switch the wave driver to UTF-8 paths. I had no luck passing UTF-8 arguments to wave using cmd.exe, you have to switch to mintty or something. But all invocations of wave without passing in UTF-8 paths work. I'd rather not pull the second submit as-is, maybe the locale switch should be optional or left away altogether. I would be happy if the first commit made it into boost wave.

---

## Review 1 [Changes requested]

> Username: hkaiser  
> Created_at: 2018-09-29 00:53:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/wave/pull/33#pullrequestreview-160058786  

Could you add a test verifying the new functionality, please?

---

## Comment 2

> Username: rlenhardt  
> Created_at: 2018-09-29 08:26:45 UTC  
> Url: https://github.com/boostorg/wave/pull/33#issuecomment-425627145  

Will do. I need some time to get accustomed to the wave testcase harness, and i won't get to do it until next week. Stay tuned...

---

## Comment 3

> Username: rlenhardt  
> Created_at: 2018-10-04 15:44:20 UTC  
> Url: https://github.com/boostorg/wave/pull/33#issuecomment-427068480  

Will close this PR and open another one, without the DEMO code but with tests.

---
