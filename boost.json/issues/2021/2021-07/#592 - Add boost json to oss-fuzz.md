# #592 - Add boost json to oss-fuzz [Closed]

> Username: pauldreik  
> Created at: 2021-07-05 12:58:46 UTC  
> Updated at: 2021-08-18 07:17:14 UTC  
> Closed at: 2021-08-18 07:17:14 UTC  
> Url: https://github.com/boostorg/json/issues/592  

There is already fuzzing inplace using github actions. That is good, and I do not want to change that. But I would like to get boost json on to oss-fuzz. The reason is that the oss-fuzz machinery is much more sophisticated. One gets nice fuzz coverage reports, for instance so it is easier to understand what needs to be fuzzed.  
  
I made an initial attempt over here: https://github.com/google/oss-fuzz/compare/master...pauldreik:boost_json?expand=1  
  
I used the boost variant instead of standalone since I read somewhere the boost json standalone version might go away.  
  
First, I want to know if you are ok with going forward with this. If not, I will just stop working on this.  
  
In case you are ok with this, I would need some advice regarding building and which branch to use  
  
# Branch  
I used the the boost-1.76 tag in the super repo. The point is to follow the development of primarily json but also boost in case boost json needs changes from the rest of boost. I do not know if there is a branch (like "develop" or so) in the super repo that could be used for this?  
The branch for boost json should be develop, I assume. For now I just used the tag in the super repo.  
  
# Build system  
The existing fuzzer uses cmake but the oss-fuzz version just invokes CXX directly. I made a jam file for compiling the rest of boost (see build.sh in the linked pull request), I would be happy to get a pointer to how to build the fuzzers using that jam file.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-07-05 16:47:31 UTC  
> Updated at: 2021-07-05 16:49:38 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874233618  

1. What are plans regarding this? Are you planning to support it in the future?  
2. Boost does have develop and master branches. master is supposed to be rather stable, and might not get updated very often (potentially, only before a Boost release).  
3. The existing fuzzer doesn't actually use CMake (or any proper build system), it uses a POSIX shell script fuzzing/fuzz.sh. And currently uses standalone mode, BTW.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-07-05 16:55:05 UTC  
> Updated at: 2021-07-05 16:55:17 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874236590  

@pauldreik wrote the GHA fuzzer FYI.. lol

---

## Comment 3

> Username: pauldreik  
> Created at: 2021-07-05 17:59:29 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874261642  

>     1. What are plans regarding this?  
The plan is to get it going and then it will file bugs when needed. I have worked with getting simdjson and fmt into oss-fuzz and those projects just keep on fuzzing without much work at all.  
>   Are you planning to support it in the future?  
  
Yes. I don't expect it to be much work because boost json is now released and won't change it's api as fast as when I wrote the fuzzers. Also, thanks to the CI fuzzer job, the fuzzers won't stop working silently.  
  
>   
>     2. Boost does have develop and master branches. master is supposed to be rather stable, and might not get updated very often (potentially, only before a Boost release).  
I guess the master branch is suitable then.  
  
>   
>     3. The existing fuzzer doesn't actually use CMake (or any proper build system), it uses a POSIX shell script fuzzing/fuzz.sh. And currently uses standalone mode, BTW.  
You are right! I wrote that from memory without checking :-)  Glad there's git to prove I did it...  
  
One thing I forgot to mention is that getting it on to oss-fuzz also requires the oss-fuzz project to accept it. There is a boost job already (which I improved a bit) but it uses only header only libraries. I don't think it makes sense to have all of boost in the same job. But the first step is to get an OK or stop from the boost json team.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-07-05 18:07:04 UTC  
> Updated at: 2021-07-05 18:07:11 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874264935  

I say, the more the merrier 👍🏻   
  
Dmitry?

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-07-05 18:17:11 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874268969  

I see no reason to object to someone doing more testing for this library. And in fact, more thorough fuzzing was the main recommendation in Bishop Fox's assessment.

---

## Comment 6

> Username: pauldreik  
> Created at: 2021-07-05 18:19:20 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874269744  

Good, I will update my oss-fuzz branch. I put myself as the primary contact but maybe it is better to put one of you as primary. Which email addresses would you like me to use? They will be put in the yaml file, so expect them to be subject to spam.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-07-05 18:30:09 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874273939  

You can use my email, Google has a good spam filter and my email address is already public. Thanks!

---

## Comment 8

> Username: grisumbras  
> Created at: 2021-07-05 18:40:49 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-874277304  

grisumbras@gmail.com would be OK, it's on the Internet anyway.

---

## Comment 9

> Username: grisumbras  
> Created at: 2021-08-17 19:27:43 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-900571535  

@pauldreik this has been resolved successfully, right?

---

## Comment 10

> Username: pauldreik  
> Created at: 2021-08-17 20:17:56 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-900601806  

Yes, it was accepted and it works (first bug found and fixed), so this   
issue can be closed now!

---

## Comment 11

> Username: pauldreik  
> Created at: 2021-08-18 07:17:14 UTC  
> Url: https://github.com/boostorg/json/issues/592#issuecomment-900879865  

I just looked at the statistics from oss fuzz. Seems like it has used something in the order of 60 cores to fuzz boost json. I do not know if they give new projects a boost in compute power, but that is just an awesome amount of fuzzing power. Let's wait for a few days and see if anything new pops up, and if not, I will test enabling the memory sanitizer for oss fuzz as well.
