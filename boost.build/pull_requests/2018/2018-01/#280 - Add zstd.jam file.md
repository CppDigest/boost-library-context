# #280 Add zstd.jam file. [Closed]

> Username: rdoeffinger  
> Created at: 2018-01-13 20:13:17 UTC  
> Updated at: 2021-10-02 22:08:23 UTC  
> Closed at: 2018-01-14 13:15:10 UTC  
> Url: https://github.com/boostorg/build/pull/280  

Just a copy-paste of lzma.jam.  
Will be used for zstd compression/decompression support  
in iostreams (see also trac ticket #13356).  
  
Implemented against master originally and github behaves weird if I try to make the pull request against develop, if you need me to rebase against develop and re-submit tell me.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-13 20:44:11 UTC  
> Url: https://github.com/boostorg/build/pull/280#issuecomment-357465907  

AMDG  
  
On 01/13/2018 01:13 PM, Reimar Döffinger wrote:  
> Just a copy-paste of lzma.jam.  
> Will be used for zstd compression/decompression support  
> in iostreams (see also trac ticket #13356).  
>   
  
- This needs a test case.  
- Don't include deprecated options in new modules.  
- I don't require you to rebase against develop, but  
  the less work I have to do, the sooner it will be applied.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2018-01-14 13:15:10 UTC  
> Url: https://github.com/boostorg/build/pull/280#issuecomment-357511182  

I'll open a new PR that is against develop, so I'll close this one.  
Note that this is a 100% copy-paste of lzma.jam. If you have suggestions to improve that one, I can do that (just take into account that you need to provide a bit more detail).  
But I am fairly strongly against letting this file diverge from lzma.jam, that seems bad for maintainability.  
What would need as test case? I don't see a test/ directory in the build repo. If you just mean some code actually using it, see https://github.com/boostorg/iostreams/pull/48  
  
Thanks!

---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-01-14 14:27:10 UTC  
> Url: https://github.com/boostorg/build/pull/280#issuecomment-357515465  

AMDG  
  
On 01/14/2018 06:15 AM, Reimar Döffinger wrote:  
> I'll open a new PR that is against develop, so I'll close this one.  
> Note that this is a 100% copy-paste of lzma.jam. If you have suggestions to improve that one, I can do that (just take into account that you need to provide a bit more detail).  
> But I am fairly strongly against letting this file diverge from lzma.jam, that seems bad for maintainability.  
  
  In the long term, I'm going to remove the is-default  
option from lzma as well.  I'd rather have fewer modules  
to update at that time.  
  
> What would need as test case? I don't see a test/ directory in the build repo.  
  
  Really?  It's definitely there.  I don't think there's  
a test case for lzma (it's on my todo list), but zlib  
and bzip2 are there.  
  
> If you just mean some code actually using it, see https://github.com/boostorg/iostreams/pull/48  
>   
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: rdoeffinger  
> Created_at: 2018-01-14 15:32:34 UTC  
> Url: https://github.com/boostorg/build/pull/280#issuecomment-357519640  

Sorry, I was quite blind/stupid there.  
I now understood what you meant with deprecated is-default and I removed it in the other PR.  
Also found the test directory, but I think writing a test is beyond what I'll manage to do in the short term, unless you can point me at an example test that tests only using system libraries and doesn't require support for building from source?  
Luckily I think there's no particular hurry in getting that code in, so worst case it can probably wait until the lzma test-case ends up reaching the top of your todo.  
You could of course also try to explain to me how to write such a test instead of me trying to reverse-engineer the existing ones, but that'll probably take you more time than what it saves you in the end.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-01-14 18:05:58 UTC  
> Url: https://github.com/boostorg/build/pull/280#issuecomment-357529843  

AMDG  
  
On 01/14/2018 08:32 AM, Reimar Döffinger wrote:  
> Sorry, I was quite blind/stupid there.  
> I now understood what you meant with deprecated is-default and I removed it in the other PR.  
> Also found the test directory, but I think writing a test is beyond what I'll manage to do in the short term, unless you can point me at an example test that tests only using system libraries and doesn't require support for building from source?  
  
  Looking at lib_zlib.py, you can just kill the  
first test (up to t.rm("zlib")) as that's the only  
one that tries to build from source.  
  
In Christ,  
Steven Watanabe

---
