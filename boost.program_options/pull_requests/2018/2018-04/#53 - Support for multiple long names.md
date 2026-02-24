# #53 Support for multiple long names [Merged]

> Username: eyalroz  
> Created at: 2018-04-21 20:40:00 UTC  
> Updated at: 2018-07-06 12:11:37 UTC  
> Merged at: 2018-06-27 19:25:44 UTC  
> Closed at: 2018-06-27 19:25:44 UTC  
> Url: https://github.com/boostorg/program_options/pull/53  

See (prolonged) discussion in the PR-for-initial-review, PR #40.

---

## Comment 1

> Username: eyalroz  
> Created_at: 2018-04-26 19:52:34 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-384768691  

@vprus : It's not clear to me why the Travis & AppVeyor builds fail (and not all of them fail). Looking at the logs, it seems like some kind of library code mismatch (?)  Plus, I see that the develop branch has some travis fails before an acceptance of the request, so I'll claim it's Not My Fault (TM).

---

## Comment 2

> Username: eyalroz  
> Created_at: 2018-05-05 13:16:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-386805032  

@vprus : Ping.

---

## Comment 3

> Username: vprus  
> Created_at: 2018-05-14 15:03:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-388850019  

@eyalroz: Pong. I'll try to look at this patch soonish, but seems like I should first get CI to become green.

---

## Comment 4

> Username: eyalroz  
> Created_at: 2018-05-21 09:08:31 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-390597837  

@vprus : So, there's PR #55 which seems to be relevant to doing that. Also - I don't suppose I could be of help?

---

## Comment 5

> Username: eyalroz  
> Created_at: 2018-06-09 16:27:23 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-395981824  

@vprus : Ping. It's been over a month and a half now.

---

## Comment 6

> Username: vprus  
> Created_at: 2018-06-14 13:16:04 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397292207  

I think I've fixed/suppressed CI issues, will run CI on this branch and get back to you then.

---

## Comment 7

> Username: vprus  
> Created_at: 2018-06-14 13:18:34 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397292991  

In fact, it probably won't build in CI untill you rebase on top of develop, or merge from develop. Could you do that?

---

## Comment 8

> Username: eyalroz  
> Created_at: 2018-06-14 15:10:48 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397330790  

I'll try, but I'm moving out of the Netherlands this week so no guarantees.  Also - right now it tells me there are no conflicts with the base branch (= develop); so it might be trivial enough to do it.

---

## Comment 9

> Username: eyalroz  
> Created_at: 2018-06-14 17:38:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397378036  

@vprus: Ok, so I've rebased. IF you'd like me to do anything else, let me know.

---

## Comment 10

> Username: vprus  
> Created_at: 2018-06-15 06:38:54 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397528158  

@eyalroz: thanks. It seems that C++03 compilation is now broken, per https://travis-ci.org/boostorg/program_options/jobs/392365094 - mostly due to use of 'auto'. Can you switch to good old for loops? Also, the first warning in the same build log is about unused variable in the code you've added?

---

## Comment 11

> Username: eyalroz  
> Created_at: 2018-06-15 08:54:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397557759  

The unused variable was "used" in an assert; taken care of. Also - I had mistakenly thought the tests could be C++11. Also fixed. Now I'm just waiting for appveyor...

---

## Comment 12

> Username: codecov[bot]  
> Created_at: 2018-06-15 09:41:06 UTC  
> Updated_at: 2018-06-15 14:52:07 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397569596  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=h1) Report  
> Merging [#53](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/4b81beaaa995026ebdc032bfe809f64a5056ce88?src=pr&el=desc) will **decrease** coverage by `0.39%`.  
> The diff coverage is `31.25%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/53/graphs/tree.svg?width=650&height=150&src=pr&token=UMdM0EgHK3)](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop      #53     +/-   ##  
==========================================  
- Coverage    50.29%   49.89%   -0.4%       
==========================================  
  Files           23       23               
  Lines         1372     1385     +13       
  Branches       694      707     +13       
==========================================  
+ Hits           690      691      +1       
  Misses         110      110               
- Partials       572      584     +12  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/program\_options/options\_description.hpp](https://codecov.io/gh/boostorg/program_options/pull/53/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvb3B0aW9uc19kZXNjcmlwdGlvbi5ocHA=) | `0% <ø> (ø)` | :arrow_up: |  
| [src/options\_description.cpp](https://codecov.io/gh/boostorg/program_options/pull/53/diff?src=pr&el=tree#diff-c3JjL29wdGlvbnNfZGVzY3JpcHRpb24uY3Bw) | `48.64% <31.25%> (-2.17%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=footer). Last update [4b81bea...0404a0d](https://codecov.io/gh/boostorg/program_options/pull/53?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 13

> Username: eyalroz  
> Created_at: 2018-06-15 13:15:17 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397616363  

@vprus : Ok, so there's not enough test coverage. I'll get around to this later on.

---

## Comment 14

> Username: eyalroz  
> Created_at: 2018-06-15 14:24:22 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397637368  

@vprus: The Travis CI builds actually succeeded, except for one which failed internally, trying to install g++7 before building my code.

---

## Comment 15

> Username: eyalroz  
> Created_at: 2018-06-15 16:08:43 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397668701  

This happened again - travis fails before touching my code. @vprus : Will you take my word for it? I've covered the `long_names()` method which codecov complained about.

---

## Comment 16

> Username: vprus  
> Created_at: 2018-06-18 07:40:57 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-397967969  

@eyalroz, yeah, it seems travis is not entirely reliable. I'll ignore that failure.

---

## Comment 17

> Username: eyalroz  
> Created_at: 2018-06-18 20:50:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-398191079  

@vprus  : Ok, so - will you merge? If not - anything else you'd like me to do first?

---

## Review 18 [Commented]

> Username: vprus  
> Created_at: 2018-06-27 19:23:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/53#pullrequestreview-132574113  

📁 test/exception_test.cpp

```diff
 190 |+    catch (multiple_occurrences& e)
 191 |+    {
 192 |+       BOOST_CHECK( (e.get_option_name() == "--cfgfile") || (e.get_option_name() == "--configfile"));
```

> Username: vprus  
> Created_at: 2018-06-27 19:23:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#discussion_r198612515  

Is there a typo on 'configfile'? The test has 'config-file', with dash.

> Username: eyalroz  
> Created_at: 2018-06-29 12:45:00 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#discussion_r199146821  

@vprus : Yes, it's a typo. Can you fix it on the development branch of the main repo or would you like a PR?

> Username: vprus  
> Created_at: 2018-07-06 07:37:04 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#discussion_r200571823  

Done, thanks.


---

## Review 19 [Commented]

> Username: vprus  
> Created_at: 2018-06-27 19:23:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/53#pullrequestreview-132574170  

📁 test/exception_test.cpp

```diff
 193 |+       BOOST_CHECK(
 194 |+          (string(e.what()) == "option '--cfgfile' cannot be specified more than once") ||
 195 |+          (string(e.what()) == "option '--configfile' cannot be specified more than once")
```

> Username: vprus  
> Created_at: 2018-06-27 19:23:40 UTC  
> Updated_at: 2018-06-27 19:23:41 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#discussion_r198612560  

Same question as above.

> Username: eyalroz  
> Created_at: 2018-07-06 12:11:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#discussion_r200634571  

Same answer as above.


---

## Comment 20

> Username: vprus  
> Created_at: 2018-06-27 19:26:38 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-400800739  

Eyal,  
  
I've merged this pull request; thanks for you contribution and patience. I had one question, inline, which can be addressed separately.

---

## Comment 21

> Username: vprus  
> Created_at: 2018-06-27 19:48:51 UTC  
> Url: https://github.com/boostorg/program_options/pull/53#issuecomment-400807050  

This was also merged to master for 1.68 and added to release notes for same. Thanks again!

---
