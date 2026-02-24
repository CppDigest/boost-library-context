# #18 Do not use test rules to check for valgrind [Merged]

> Username: Kojoley  
> Created at: 2017-11-26 20:19:49 UTC  
> Updated at: 2017-11-28 18:45:46 UTC  
> Merged at: 2017-11-28 18:29:45 UTC  
> Closed at: 2017-11-28 18:29:45 UTC  
> Url: https://github.com/boostorg/pool/pull/18  

Passing a test rule to `check-target-builds` affects `--dump-tests` output,  
some tests disappears what causes the missing tests in regression matrix.  
  
This was discovered at modularizing Spirit tests (tests under `build-project`  
dirs runs, but do not appear in `--dump-tests` output)

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-11-27 15:55:08 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347226212  

@pdimov can I kindly ask you to merge this? It blocks my work on Spirit.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-27 16:16:11 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347233462  

Could you please explain in more detail how this blocks the work on Spirit?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-11-27 16:39:10 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347240954  

I have to split Spirit tests because:  
  - otherwise it timeouts on Travis (Qi, Karma and X3 build exceeds 60 minutes)  
  - to ease the maintenance (of course I can maintain one big `test/Jamfile` and multiple its subparts in subdirectories, but what is the point, why do that and not to fix the blocker issue?).  
  
P.S: I spent 4 hours trying to figure out why the same tests structure as Geometry has does not work for Spirit and it led me only to this fix.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-11-27 16:52:04 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347245523  

OK, but where does the Pool Jamfile fit into the picture? What's the connection between Pool and the Spirit tests?

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-11-27 17:00:24 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347248609  

I see, the explanation is in https://github.com/boostorg/spirit/pull/293, the Pool Jamfile for some reason breaks `--dump-tests` for tests using `build-project`.  
  
@grafikrobot do you have any idea what's going on here?

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-11-27 17:00:35 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347248671  

If I even know it. It is some side-effect that I cannot debug because of impenetrable Boost.Build system. All I can say: I have added debug prints to Geometry and Spirit, while running `--dump-tests` Geometry has the right output, their `build-project` jams run before the `--dump-tests` output, while Spirit's after. If I exclude Pool/remove Pool/remove Pool tests/or apply this fix - the Spirit behavior fixes.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-11-27 17:02:00 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347249238  

The likely cause of that is because Geometry starts with G and Spirit with S. IOW, the Geometry Jamfile is processed before the Pool one, but the Spirit Jamfile - after.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2017-11-27 17:03:05 UTC  
> Updated_at: 2017-11-27 17:03:27 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347249641  

It was one of my thoughts but if it is right then this fix should not work out while it is.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-11-27 17:27:45 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347257958  

What happens if you change  
```  
build-project karma ;  
```  
to  
```  
alias test-karma : karma ;  
```  
?

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-11-27 17:30:02 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347258732  

I'm looking at https://github.com/boostorg/build/blob/develop/src/tools/testing.jam#L128 and it mentions problems with `build-project`, so perhaps if you could avoid `build-project` it would work?

---

## Comment 11

> Username: pdimov  
> Created_at: 2017-11-27 17:37:58 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347261555  

Or perhaps  
```  
alias spirit-karma : karma//spirit-karma ;  
```  
if you prefer.

---

## Comment 12

> Username: Kojoley  
> Created_at: 2017-11-27 17:39:19 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347262058  

> `alias test-karma : karma ;`  
  
It did not help.

---

## Comment 13

> Username: Kojoley  
> Created_at: 2017-11-27 17:45:53 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347264252  

Actually it even makes karma tests appear after the other spirit tests (that comes from `build-project`)

---

## Comment 14

> Username: swatanabe  
> Created_at: 2017-11-27 18:20:37 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347275596  

AMDG  
  
On 11/27/2017 10:00 AM, Peter Dimov wrote:  
> I see, the explanation is in https://github.com/boostorg/spirit/pull/293, the Pool Jamfile for some reason breaks `--dump-tests` for tests using `build-project`.  
>   
> @grafikrobot do you have any idea what's going on here?  
>   
  
I'm not grafikrobot, but I understand the issue.  
dump-tests is being executed prematurely, when  
the configuration tests are run instead of being  
run immediately before the main build.  I'm in  
the process of testing a fix to Boost.Build (since  
we actually have the machinery to to it correctly, now,  
unlike when testing.jam was originally written).  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-11-27 18:20:38 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347275599  

Right, I think I figured it out. `--dump-tests` run in the `**passed**` rule: https://github.com/boostorg/build/blob/develop/src/tools/testing.jam#L363  
  
But the `run valgrind_config_check.cpp` here, when invoked from `check-target-builds valgrind`, apparently invokes `**passed**`, which dumps the tests, before all of them have been added.  
  
This affects more than Spirit, but nobody has noticed so far.  
  
It also disables a bunch of tests in `libs/config/test/all`, which we probably don't want in the matrix. Not sure where these come from; probably from `check-target-builds` too.  
  
What fun.

---

## Comment 16

> Username: pdimov  
> Created_at: 2017-11-27 18:23:13 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347276356  

Didn't see your comment Steven. Should I merge this PR then, or should I wait?

---

## Comment 17

> Username: swatanabe  
> Created_at: 2017-11-27 18:27:51 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347277808  

AMDG  
  
On 11/27/2017 09:52 AM, Peter Dimov wrote:  
> OK, but where does the Pool Jamfile fit into the picture? What's the connection between Pool and the Spirit tests?  
>   
  
  There shouldn't be any.  The issue is entirely dependent on  
the project load order, which is very complicated.  The main  
issue is that build-project only triggers a lazy load of the  
subproject, which happens too late.  use-project does force  
the subproject to be loaded immediately after the caller,  
so that would work around the problem.  
  
In Christ,  
Steven Watanabe

---

## Comment 18

> Username: swatanabe  
> Created_at: 2017-11-27 18:44:33 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347283232  

AMDG  
  
On 11/27/2017 11:23 AM, Peter Dimov wrote:  
> Didn't see your comment Steven. Should I merge this PR then, or should I wait?  
>   
  
  I think this PR is the right thing to do anyway, since  
a config check shouldn't really be considered a test.  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: pdimov  
> Created_at: 2017-11-27 21:38:54 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347336858  

Should no longer be a problem due to https://github.com/boostorg/build/commit/6cea15c958ba3a5a60d6de40236a1f2cdbde20ae (thanks Steven). I'll still apply this PR after a few Travis cycles.

---

## Comment 20

> Username: pdimov  
> Created_at: 2017-11-28 14:01:10 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347531903  

I was having second thoughts about the legitimacy of this patch; was thinking that since I was seeing many additional tests in the fixed `--dump-tests` output, maybe `run` rules are being used for checks, and that perhaps the right way would be to make them usable and hidden from the list.  
  
But now that I look at the matrix, the only additional test that shows up incorrectly seems to be `no_std_wstreambuf_{pass,fail}`, not the whole family as I thought would happen (as I definitely saw them in `--dump-tests`.)  
  
http://www.boost.org/development/tests/develop/developer/config.html  
  
And it appears that this is caused not by a `run` rule used as a check, but by  
  
https://github.com/boostorg/serialization/blob/develop/util/test.jam#L113

---

## Comment 21

> Username: Kojoley  
> Created_at: 2017-11-28 14:06:31 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347533397  

And `valgrind_config_check` seems to disappear from builds with boostorg/build@6cea15c. Maybe add some argument to `run` so the test actually do not append to tests list so it will not show up on `--dump-tests` output?

---

## Comment 22

> Username: pdimov  
> Created_at: 2017-11-28 14:10:30 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347534506  

That's what I was thinking when I though such use will be common, but then I looked and `run` is not actually being used in this manner, so this seems like the only such occurrence and it probably would not warrant fiddling with `run`.

---

## Comment 23

> Username: pdimov  
> Created_at: 2017-11-28 14:12:42 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347535123  

There's one empty row in the Spirit matrix between `support_utree_debug-p3` and `x3_actions`, maybe that's `valgrind_config_check` :-)

---

## Comment 24

> Username: Kojoley  
> Created_at: 2017-11-28 14:15:51 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347536071  

> There's one empty row in the Spirit matrix between `support_utree_debug-p3` and `x3_actions`, maybe that's `valgrind_config_check` :-)  
  
It is a category name, scroll to the middle of the table to see it :-D

---

## Comment 25

> Username: pdimov  
> Created_at: 2017-11-28 14:20:10 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347537359  

I need a wider monitor. 8K would probably do.  
  
One cosmetic disadvantage of this change is that when `valgrind` (the binary) is not present, before we got `- valgrind : no` as output, and now we get nothing. Not sure if that's worth addressing though. I can think of ways to fix it, but all seem terribly overkill and not particularly elegant.

---

## Comment 26

> Username: swatanabe  
> Created_at: 2017-11-28 15:53:27 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347568083  

AMDG  
  
On 11/28/2017 07:01 AM, Peter Dimov wrote:  
> I was having second thoughts about the legitimacy of this patch; was thinking that since I was seeing many additional tests in the fixed `--dump-tests` output, maybe `run` rules are being used for checks, and that perhaps the right way would be to make them usable and hidden from the list.  
>   
> But now that I look at the matrix, the only additional test that shows up incorrectly seems to be `no_std_wstreambuf_{pass,fail}`, not the whole family as I thought would happen (as I definitely saw them in `--dump-tests`.)  
>   
  
  Oh.  I guess that makes sense.  This is probably  
because process_jam_log doesn't just look for  
the output of dump-tests.  The main thing it uses  
is the actual listing of the actions being run,  
which gets redirected to a file for configure checks.  
  
> http://www.boost.org/development/tests/develop/developer/config.html  
>   
> And it appears that this is caused not by a `run` rule used as a check, but by  
>   
> https://github.com/boostorg/serialization/blob/develop/util/test.jam#L113  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 27

> Username: Kojoley  
> Created_at: 2017-11-28 15:56:50 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347569169  

> process_jam_log doesn't just look for the output of dump-tests  
  
It parses the output of dump-tests to fill the `test-type` and `test-program` values.

---

## Comment 28

> Username: pdimov  
> Created_at: 2017-11-28 18:00:01 UTC  
> Url: https://github.com/boostorg/pool/pull/18#issuecomment-347609645  

Tried  
```  
local rule if_ ( cond ? : then_ * : else_ * )  
{  
    if ( $(cond) ) { return $(then_) ; } else { return $(else_) ; }  
}  
  
local VALGRIND = [ common.find-tool valgrind ] ;  
  
obj valgrind_config_check : valgrind_config_check.cpp : [ if_ $(VALGRIND) : : <build>no ] ;  
explicit valgrind_config_check ;  
  
local use-valgrind = [ check-target-builds valgrind_config_check "valgrind"  
                     : <testing.launcher>"$(VALGRIND) --error-exitcode=1"  
                     : <build>no ] ;  
```  
but `<build>no` causes the check to pass instead of failing it. Could have used `<build>fail` if there were.

---
