# #47 Add zstd compression/decompression filter. [Closed]

> Username: rdoeffinger  
> Created at: 2018-01-13 20:13:47 UTC  
> Updated at: 2018-01-14 13:07:07 UTC  
> Closed at: 2018-01-14 13:07:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/47  

Implements trac issue #13356.  
Not tested in real production use, help welcome.  
  
Implemented against master originally and github behaves weird if I try to make the pull request against develop, if you need me to rebase against develop and re-submit tell me.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-01-13 21:02:19 UTC  
> Url: https://github.com/boostorg/iostreams/pull/47#issuecomment-357467011  

To start with this needs to be against develop, not master. I do not know what you mean by github behaving weird. Fork the repository, make the change for your fork against develop, commit the change, push to your fork, create a PR. It is all very straightforward.

---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2018-01-14 13:07:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/47#issuecomment-357510697  

Yes, it's a total non-issue to do it against develop, I'll submit a new PR. I just originally tried to do the pull request against develop without manually rebasing and the result was very weird.  
But anyway, the change is exactly the same so it doesn't really make any difference for reviewing, thus I first submitted what I had at that time.

---
