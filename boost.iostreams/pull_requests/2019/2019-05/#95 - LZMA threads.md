# #95 LZMA threads [Merged]

> Username: jbonyun  
> Created at: 2019-05-26 17:35:34 UTC  
> Updated at: 2019-10-25 17:26:12 UTC  
> Merged at: 2019-05-28 23:49:26 UTC  
> Closed at: 2019-05-28 23:49:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95  

Enables multithreaded LZMA compression in Boost.iostreams.  
  
I have tested the build and behavior on a range of Ubuntu and RHEL systems. I have tested the build and behavior of all numbered liblzma release versions in their git repo.  
  
Unit testing doesn't cover:  
 - That multiple threads are actually used when appropriate. Because how would I do that in a unit test?  
 - Behavior on larger amounts of data (which liblzma deploys more threads on). Because it would slow down the execution of tests significantly.  
 - That the build correctly detects liblzma's capabilities. Because this is a build issue.  
 - That the build correctly uses BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED macro. Because this is a build issue.

---

## Comment 1

> Username: jbonyun  
> Created_at: 2019-05-26 17:37:10 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496018041  

I'm happy for these commits to be squashed, if that's the preference of the maintainers. I tried to keep individual steps for easier review.  
  
I'm also happy to hear more suggestions for unit tests. I've exercised the basic functionality and the various constructors/initializers.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2019-05-26 17:50:49 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496018863  

AMDG  
  
Designated initializers are a C99 feature partially adopted in C++20.  
I don't know what the minimum supported standard for iostreams is,  
but I suspect C++20 is not it.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: jeking3  
> Created_at: 2019-05-26 19:02:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496023442  

iostreams supports C++03 or later

---

## Comment 4

> Username: jbonyun  
> Created_at: 2019-05-26 19:26:03 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496024988  

Very well, I can change that to be spelled out over 8 lines. It wasn't a syntax I was accustomed to either. It does build with all versions of gcc, intel, clang that I tried, for what it's worth. But I understand you'd like to stay within the standard.

---

## Comment 5

> Username: jbonyun  
> Created_at: 2019-05-26 19:40:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496025855  

@swatanabe Actually, were you suggesting an initializer list without the "designated initializer" part of it? Or were you suggesting a one-member-at-a-time assignment? The first is concise, but I feel obfuscates. The second is verbose.  
  
Initializer list:  
  
    const lzma_mt opt = { 0, threads_, 0, 1000, level_, nullptr, LZMA_CHECK_CRC32 };  
  
One-member-at-a-time:  
  
    lzma_mt opt;                                                                                        
    opt.flags = 0;                                                                                      
    opt.threads = threads_;                                                                             
    opt.block_size = 0;                                                                                 
    opt.timeout = 1000;                                                                                 
    opt.preset = level_;                                                                                
    opt.filters = nullptr;                                                                              
    opt.check = LZMA_CHECK_CRC32;  
  
I'm happy to take the preference of the regular maintainers.

---

## Comment 6

> Username: jbonyun  
> Created_at: 2019-05-26 19:58:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496027045  

I was feeling bad for not having updated the documentation... but lzma isn't really documented in iostreams in the first place. If it's alright with you guys, I'd rather leave that for now.  
  
I imagine somebody ought to write that. The bzip2 stuff would be a good starting point to copy from. Maybe it can be an additional project I take on, after this.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-05-26 22:45:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496037401  

`const lzma_mt opt = { 0, threads_, 0, 1000, level_, nullptr, LZMA_CHECK_CRC32 };` should be fine, except that `nullptr` is also C++11.

---

## Comment 8

> Username: jbonyun  
> Created_at: 2019-05-26 23:32:02 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496040020  

Yep, nullptr is what broke the Appveyor build for Windows. And I was so proud of myself for using modern stuff!  
  
I've pushed an update for nullptr and designated initializers. I would recommend that be squashed before merge, but I didn't want to force push if that's not the preferred approach.

---

## Comment 9

> Username: jbonyun  
> Created_at: 2019-05-27 14:23:22 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496229451  

So... can anyone interpret that codecov.io break? Something related to my changes? It looks, to me, that it has to do with a gzip module for codecov not being available...

---

## Comment 10

> Username: jeking3  
> Created_at: 2019-05-27 14:44:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496235607  

Looks like something was missing from the build environment to run lcov.  Perhaps lcov changed recently.  I maintain the build wrapper around that.  I'll take a look this week.

---

## Review 11 [Commented]

> Username: jeking3  
> Created_at: 2019-05-28 11:19:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/95#pullrequestreview-242052594  

Missing documentation.  I think I saw an email thread about doing that some other time since there is no documentation for lzma usage right now?

📁 src/lzma.cpp

```diff
  84 | lzma_base::lzma_base()
  78 |-     : stream_(new lzma_stream), level(lzma::default_compression)
  85 |+     : stream_(new lzma_stream), level_(lzma::default_compression), threads_(1)
```

> Username: jeking3  
> Created_at: 2019-05-26 19:39:15 UTC  
> Updated_at: 2019-05-28 11:19:08 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#discussion_r287611482  

I thought the default would be 0, meaning use as many cores as possible?

> Username: jbonyun  
> Created_at: 2019-05-28 15:04:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#discussion_r288153343  

That would work, with just a change where you commented. I was trying to avoid surprising the users by changing the default behavior. If you think that `0` is the right answer, I can make that change.

> Username: jbonyun  
> Created_at: 2019-05-28 15:11:26 UTC  
> Updated_at: 2019-05-28 15:11:27 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#discussion_r288156987  

Correction: a change here would be ignored. `threads_` should always be overwritten by whatever is passed in `lzma_params`, even if that's the default constructed version of `lzma_params`. I would change both to 0.


---

## Comment 12

> Username: jbonyun  
> Created_at: 2019-05-28 15:06:10 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496557281  

@jeking3 Re documentation, lzma isn't documented in iostreams at all, so there was nothing to update. I was proposing that adding lzma documentation be a separate step. One which I would consider doing, while it's fresh in my mind.

---

## Comment 13

> Username: swatanabe  
> Created_at: 2019-05-28 15:12:10 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496559836  

AMDG  
  
On 5/28/19 9:04 AM, jbonyun wrote:  
>   
> That would work, with just a change where you commented. I was trying to avoid surprising the users by changing the default behavior. If you think that `0` is the right answer, I can make that change.  
>   
  
I would prefer not to make such a change.  0 is only going to  
be the right default in programs that are otherwise single-threaded.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: jeking3  
> Created_at: 2019-05-28 17:13:53 UTC  
> Updated_at: 2019-05-28 17:41:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496606911  

When `BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED` is present, a non-mt encoder is used, and threads_ is ignored.  Shouldn't the default behavior be `threads_(0)`, which means "use as many threads as advertised cores"?  If we make the default 1, nobody will get the benefit until they specifically enable it in their projects.

---

## Comment 15

> Username: codecov[bot]  
> Created_at: 2019-05-28 17:31:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496613794  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/95?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@7c627be`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/95/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/95?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #95   +/-   ##  
==========================================  
  Coverage           ?   68.96%             
==========================================  
  Files              ?       80             
  Lines              ?     3444             
  Branches           ?     1027             
==========================================  
  Hits               ?     2375             
  Misses             ?      452             
  Partials           ?      617  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/95?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/lzma.cpp](https://codecov.io/gh/boostorg/iostreams/pull/95/diff?src=pr&el=tree#diff-c3JjL2x6bWEuY3Bw) | `90.9% <100%> (ø)` | |  
| [include/boost/iostreams/filter/lzma.hpp](https://codecov.io/gh/boostorg/iostreams/pull/95/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL2x6bWEuaHBw) | `68.08% <100%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/95?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/95?src=pr&el=footer). Last update [7c627be...6fa2d8d](https://codecov.io/gh/boostorg/iostreams/pull/95?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 16

> Username: jbonyun  
> Created_at: 2019-05-28 18:49:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496642245  

@jeking3 Assuming that `BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED` will not be defined in the default build of Boost, I think that's the same as saying `0` is the default number of threads. Because just changing the number of threads in the user's constructor is easier than defining the macro and recompiling Boost. The macro will/should only be used if Boost won't compile because `--disable-threads` has been used on their libxzma or if, for some reason, the multithreaded API to libxzma has broken.  
  
I see @swatanabe's point that in a program where threading has been explicitly managed by the designer, suddenly using all the threads could be detrimental. I see @jeking3's point that we should give the extra speed to users who don't want to put extra thought into it.  
  
My instinct was not to change the default behavior and therefore to use one thread by default. But we're back to being above my pay grade. I will be happy with either option, but don't feel that I'm qualified to resolve the difference of opinion.

---

## Comment 17

> Username: swatanabe  
> Created_at: 2019-05-28 19:50:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-496663053  

AMDG  
  
On 5/28/19 12:49 PM, jbonyun wrote:  
> <snip>  
> My instinct was not to change the default behavior and therefore to use one thread by default. But we're back to being above my pay grade. I will be happy with either option, but don't feel that I'm qualified to resolve the difference of opinion.  
>   
  
If a function runs single-threaded, it's unlikely to  
be surprising even for users who want it to be multi-threaded.  
Adding threads behind the scenes could be a surprise, though.  
As such, I think single-threaded is a better default.  
(Note that this logic is somewhat different for individual  
functions as opposed to complete programs.)  
  
In Christ,  
Steven Watanabe

---

## Review 18 [Approved]

> Username: jeking3  
> Created_at: 2019-05-28 23:47:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/iostreams/pull/95#pullrequestreview-242974561  

Given the discussion, default behavior will be single threaded unless someone takes the time to make it use more cores.

---

## Comment 19

> Username: pdimov  
> Created_at: 2019-10-25 17:26:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/95#issuecomment-546440746  

FWIW, I just noticed that this change causes building Iostreams to fail on Travis's default macOS (xcode 9.4.1) at link time: https://travis-ci.org/boostorg/check_build/jobs/602620501#L1435  
  
This seems to imply that the lzma header contains the threading stuff, whereas the library doesn't? I'm not a Mac person so I can't check.

---
