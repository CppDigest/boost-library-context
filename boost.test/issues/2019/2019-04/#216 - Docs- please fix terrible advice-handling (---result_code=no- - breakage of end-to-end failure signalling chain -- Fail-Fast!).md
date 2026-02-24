# #216 - Docs: please fix terrible advice/handling ("--result_code=no" - breakage of end-to-end failure signalling chain -- Fail-Fast!) [Open]

> Username: andim2  
> Created at: 2019-04-03 10:54:16 UTC  
> Updated at: 2023-06-25 01:44:30 UTC  
> Url: https://github.com/boostorg/test/issues/216  

Hello,  
  
I keep having recurring (a half dozen incidents already) issues with  
fellow developer colleagues re-inserting AWFUL `--result_code=no` arguments to various unit test executions in a larger build environment.  
  
This is a massively annoying recurring issue here (thus keeps generating completely useless/extra consistency noise/repair work effort every couple of weeks! - a lot of time lost to achieve "truly productive progress").  
  
I had the idea of  
getting this recurring issue reliably Fixed For Real locally, by  
applying proper Convention over Configuration mechanisms which  
would directly (thus reliably) tell people that this is *NOT* to be done, via  
properly sufficiently clarifying/obvious/centralized marker expressions / modules (infrastructure).  
  
However, this intention does not seem to be workable, due to  
[oh-so-frequent] structural weakness / BUGS in msbuild (see  
    "Visual Studio post-build event macros are empty"  
      https://stackoverflow.com/questions/26748846/visual-studio-post-build-event-macros-are-empty/30612179#30612179  
      (via  
      "Make PreBuildEvents\PostBuildEvents work using targets"  
        https://github.com/dotnet/project-system/issues/1569 )  
(very obligatory snide remark: with properly functional-programming CMake infrastructure it is a LOT easier to create/provide a nice inconsistency-due-to-duplication-avoiding Convention over Configuration framework!)  
  
Thus this annoying issue MUST be tackled centrally at upstream side, read: here.  
  
--result_code=no craptastics appear to be a HUGE problem:  
1. it breaks a formerly properly working end-to-end failure signalling chain, which normally would be:  
   issue discovered [e.g. by build server] --> issue signalled via FAIL build --> "Houston, we've got a problem" --> "we need to have this fixed immediately" (*properly*)  
2. it is a GLOBAL effect:  
   single test case may happen to FAIL --> "oh, we must accept this" --> instead of fixing / treating the *ROOT CAUSE*, properly(!!), developer simply/lazily starts applying --result_code=no, thereby killing proper FAIL signalling of **any and all test cases** within the friggin' entire test suite!! --> from that moment on, any and all **future** breakage cases within scope of the entire test cases container will go entirely unnoticed/untreated --> increase of development noise / MASSIVE CONTEXT LOSS / cache-cold handling (resulting effect, worst case: "oh, hmm, this doesn't work - oh wait, crap, this hasn't been working for some time already even - oh noes, that entire company branch has already been put out of service - oh damnit, who would be able to fix this for us now or even know anything about it?")  
  
Thus, I would think that  
--result_code=no constitutes a blatant disregard for  
Best Practice Fail-Fast handling.  
  
So, the effects of applying --result_code=no (as prominently mentioned in the officially wholly undisputed --result_code=no public docs: https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/practical_usage_recommendations/ide_usage_recommendations.html , which likely is why all MSVS-using developers seem to like doing this) are:  
- not fixing the actual Root Cause  
- causing massive (since global) damage  
  
--> DOUBLE FAIL!!  
  
So, within a larger build environment to me it seems entirely counter-productive (read: WHOLLY DAMAGING) to be having tests configured with a globally-disable-FAIL mechanism (--result_code=no):  
Given a questionably large development scope of  
a couple hundred projects within a build solution (with a couple thousand translation units or so), nobody will ever really (be forced to, "IN-YOUR-FACE") notice/investigate further *newly* occurring test failure issues, things will (be encouraged to) keep piling up (noise!!), and the result is that  
you may end up with a nice completely-lost-context situation where you've got a terrible half-dozen or more test case fails where you have lost any and all context information about, *each*:  
- what exactly happened and when it first occurred  
- what the developer(s) formerly working on this have been thinking about (volatile knowledge!)  
- what this even is all about  
--> MASSIVE increase of investigation/development effort!!  
  
(c.f. the very comparable mechanism of  
compiler warnings vs. Fail-Fast handling: warning-as-error, which achieves  
keeping in check such compiler warning noise / context loss)  
  
Another thought I am having is that  
--result_code=no likely should be warned about by boost test suite framework itself, via:  
- e.g. a warning log record when encountering this cmdline option setting - but I am not sure whether such "questionable" (special logging activity parts) handling would be suitable  
- cmdline options help (--help): it currently seems to provide content "Disables test module's result code generation." - this likely should be augmented, by something like: "WARNING: will disrupt proper end-to-end failure signalling chain (NOT Fail-Fast handling / GLOBAL-ignore effect, causing delayed development / noise)." or perhaps simply: "WARNING: renders Fail-Fast benefits disabled, globally!")  
  
Thanks!

---

## Comment 1

> Username: sehe  
> Created at: 2023-06-25 01:44:30 UTC  
> Url: https://github.com/boostorg/test/issues/216#issuecomment-1605816133  

I'm in favor of a short warning if it doesn't exist. "Warning: Make sure you handle test results properly in CI setting".  
  
Other than that, it does seem like a "this button is not useful to me, I don't want it" situation. I can imagine lots of situations where the option *is* useful. After all, FAIL FAST should apply to the subject under test, not the CI toolchain, and many of them are cumbersome enough to work with as it is, making an an option like this very helpful.
