# #2435 Update zlib to 1.2.12 [Closed]

> Username: JackyYin  
> Created at: 2022-05-25 10:07:00 UTC  
> Updated at: 2022-06-15 17:57:48 UTC  
> Closed at: 2022-06-03 20:44:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2435  

Greeting,  
  
What this PR does is simply update `zlib` library from `1.2.11` to `1.2.12`, this is because there is a CVE issue affect `zlib` library before `1.2.12`.  
  
CVE link: https://access.redhat.com/security/cve/CVE-2018-25032   
  
Currently `zlib` library is only used in `test/beast` and `test/bench`, I don't think this CVE will affect the beast application itself, but I think it's still a good idea to update this and avoid any related problems.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-05-25 16:23:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1137499090  

Yeah, not a bad idea I suppose although I don't like "chores" lol, not a good look :)  
  
The bigger question is why in the hell is a 20+ year old library still discovering CVEs???

---

## Comment 2

> Username: JackyYin  
> Created_at: 2022-05-26 01:46:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1138054983  

Hi @vinniefalco ,  
Thanks for your feedback!  
  
> Yeah, not a bad idea I suppose although I don't like "chores" lol, not a good look :)  
  
Actually it's a git commit convention I have been used for a long time, please see this:  
https://gist.github.com/joshbuchea/6f47e86d2510bce28f8e7f42ae84c716.  
  
But I think maybe it's more appropriate to use `fix` because those commits include production code changes, right?  
  
> The bigger question is why in the hell is a 20+ year old library still discovering CVEs???  
  
Actually, in our company, we use `blackduck` to statically scan source code for possible vulnerability, It will indicate CVE in every third party library and it's regularly update.  
  
I think there are a bunch of threat experts looking for these security problems everyday.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-05-26 04:27:14 UTC  
> Updated_at: 2022-05-26 18:38:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1138140756  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2435](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4e205c8) into [develop](https://codecov.io/gh/boostorg/beast/commit/5799f159c588c3a4b886d0f8d4dd3e0c1fd42bed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5799f15) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 4e205c8 differs from pull request most recent head 2f2c8fa. Consider uploading reports for the commit 2f2c8fa to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2435/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2435      +/-   ##  
===========================================  
- Coverage    94.72%   94.69%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11858    11858                
===========================================  
- Hits         11233    11229       -4       
- Misses         625      629       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2435/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5799f15...2f2c8fa](https://codecov.io/gh/boostorg/beast/pull/2435?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-05-26 18:22:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1138881011  

> But I think maybe it's more appropriate to use fix because those commits include production code changes, right?  
  
Yes. This would be a `fix` in this terminology. Not that the project uses semantic commit messages anyway.  
  
We should have "fix #2439" in the message body though.

---

## Comment 5

> Username: JackyYin  
> Created_at: 2022-05-26 18:33:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1138890097  

Hi @alandefreitas ,  
Thanks for your feedback.  
  
I will squash the two commits and add "fix #2439" to the commit message body.

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-05-26 18:44:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1138898368  

> Hi @alandefreitas , Thanks for your feedback.  
>   
> I will squash the two commits and add "fix #2439" to the commit message body.  
  
No problem.   
  
I also had a look at the code and everything seems safe, especially since only the tests are affected.  
  
It would be good if we also approved running the GHA workflow. Drone is already running.

---

## Comment 7

> Username: JackyYin  
> Created_at: 2022-05-26 18:59:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1138909876  

> It would be good if we also approved running the GHA workflow. Drone is already running.  
  
Sure, actually I'm not quite sure about the difference between the tests being ran by Drone CI and GHA.  
Seems like the Drone CI includes `codecov` test, but they both ran test cases in `test/beast`.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-05-26 20:57:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1139042545  

@alandefreitas you need to port the fix to Beast

---

## Comment 9

> Username: alandefreitas  
> Created_at: 2022-06-03 20:44:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1146347822  

Merged at ca824f607ccab6682b955eb91ed49753e6433069

---

## Comment 10

> Username: sehe  
> Created_at: 2022-06-15 17:57:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2435#issuecomment-1156769611  

@alandefreitas I noticed that the Bjam/CMake changes included a number of files that were previously excluded because they were emitting a slew of C99 violation warnings (implicit function declarations)  
  
```  
test/extern/zlib-1.2.12/gzread.c|35 col 15| warning: implicit declaration of function ‘read’; did you mean ‘fread’? [-Wimplicit-function-declaration]  
test/extern/zlib-1.2.12/gzread.c|649 col 11| warning: implicit declaration of function ‘close’; did you mean ‘pclose’? [-Wimplicit-function-declaration]  
test/extern/zlib-1.2.12/gzwrite.c|89 col 20| warning: implicit declaration of function ‘write’; did you mean ‘fwrite’? [-Wimplicit-function-declaration]  
test/extern/zlib-1.2.12/gzlib.c|14 col 17| warning: implicit declaration of function ‘lseek’; did you mean ‘fseek’? [-Wimplicit-function-declaration]  
test/extern/zlib-1.2.12/gzwrite.c|673 col 9| warning: implicit declaration of function ‘close’; did you mean ‘pclose’? [-Wimplicit-function-declaration]  
```  
  
Opened a PR to address this by excluding the unused sources once again: https://github.com/boostorg/beast/pull/2452

---
