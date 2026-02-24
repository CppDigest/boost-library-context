# #277 Drop bintray, use github action cache [Closed]

> Username: pauldreik  
> Created at: 2020-09-05 15:40:00 UTC  
> Updated at: 2020-09-06 13:07:12 UTC  
> Closed at: 2020-09-06 13:07:12 UTC  
> Url: https://github.com/boostorg/json/pull/277  

@Flamefire please have a look

---

## Comment 1

> Username: pauldreik  
> Created_at: 2020-09-05 16:14:49 UTC  
> Updated_at: 2020-09-06 11:58:59 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687631019  

Edit: the mystery has been solved, see comments further down.  
  
When run for the first time, it correctly stores the cache and it is found on the next run (at least when running on the same branch).  
The problem comes at the end of second run. Because the cached item already exists, it won't be updated even if the file has changed.  
  
I can not find how to evict the cache manually.  
  
If that eviction feature would have existed, using github action cache could have worked for storing the corpus.  
  
@jonathanmetzman maybe you know more than me on how this could work?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2020-09-05 19:08:04 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687649814  

This  should work in principle. See my test repo:  https://github.com/Flamefire/gha_tests  
Check the actions tab for the 2 "test" commit runs and expand " Run value=$(cat cacheFolder/foo.txt)" which shows how the cached value is increased with every run

---

## Comment 3

> Username: pauldreik  
> Created_at: 2020-09-06 05:49:06 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687705750  

> This should work in principle. See my test repo: https://github.com/Flamefire/gha_tests  
> Check the actions tab for the 2 "test" commit runs and expand " Run value=$(cat cacheFolder/foo.txt)" which shows how the cached value is increased with every run  
  
I see that it works in your repo, thanks for the demo! I think I know why it did not work for me:  
I used the "rerun workflow" button and not pushing new changes. The new run gets the same id. On the second run, I get "Cache hit occurred on the primary key corpus, not saving cache."   
  
That means the suggested approach would work, but not on reruns. I will do an experiment with a random key instead and see what happens.

---

## Comment 4

> Username: pauldreik  
> Created_at: 2020-09-06 07:06:02 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687713379  

Ok, I found an ugly hack to get this to work. Using a symlink to /proc/self/stat placed within the repo, then using the github action supplied function hashFiles() on the symlink.

---

## Comment 5

> Username: pauldreik  
> Created_at: 2020-09-06 08:17:48 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687722794  

I think this is ready for review/merge now.

---

## Comment 6

> Username: pauldreik  
> Created_at: 2020-09-06 08:18:16 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687722858  

Thank you @Flamefire  for your help!

---

## Comment 7

> Username: Flamefire  
> Created_at: 2020-09-06 11:10:21 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687759017  

> I used the "rerun workflow" button and not pushing new changes. The new run gets the same id. On the second run, I get "Cache hit occurred on the primary key corpus, not saving cache."  
  
I'd consider that a feature and keep that. If you rerun you often like to see the same failure again. Besides that I very rarely use the "rerun" button and just trigger the CI with a new commit (you can even amend the last commit without changing anything and force push) so I wouldn't include such hacks for such a minor use case.  
Just for your consideration.

---

## Comment 8

> Username: pauldreik  
> Created_at: 2020-09-06 11:57:39 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687769658  

> > I used the "rerun workflow" button and not pushing new changes. The new run gets the same id. On the second run, I get "Cache hit occurred on the primary key corpus, not saving cache."  
>   
> I'd consider that a feature and keep that. If you rerun you often like to see the same failure again. Besides that I very rarely use the "rerun" button and just trigger the CI with a new commit (you can even amend the last commit without changing anything and force push) so I wouldn't include such hacks for such a minor use case.  
> Just for your consideration.  
  
This is for a fuzz corpus so it is only to speed up the fuzzing. If something interesting happens, it ends up in one of the files caught by the artifacts job. Hence there is no need to rerun with the exact same corpus.  
  
Regarding rerun, it is a very handy feature if one wants to manually add some extra fuzzing effort, for instance when wondering why a bug is not caught by the fuzzer.  
  
For this particular CI job, this is exactly how I want it :-)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-06 12:17:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/277#pullrequestreview-483138239  

📁 .github/workflows/proc_self_stat

```diff
   1 |+ /proc/self/stat
```

> Username: vinniefalco  
> Created_at: 2020-09-06 12:17:44 UTC  
> Updated_at: 2020-09-06 12:48:57 UTC  
> Url: https://github.com/boostorg/json/pull/277#discussion_r484062915  

hmmm

> Username: vinniefalco  
> Created_at: 2020-09-06 12:22:49 UTC  
> Updated_at: 2020-09-06 12:48:57 UTC  
> Url: https://github.com/boostorg/json/pull/277#discussion_r484063407  

This file is giving me trouble on Windows. When I check out the branch, the file shows up as modified in my working set. WIndows doesn't support symlinks like this. I"m not sure what to do.

> Username: pauldreik  
> Created_at: 2020-09-06 12:33:38 UTC  
> Updated_at: 2020-09-06 12:48:57 UTC  
> Url: https://github.com/boostorg/json/pull/277#discussion_r484064508  

That means a large percentage of users will have the same problem, leading to questions even if I am sure there is some symlink setting in windows git somewhere which might fix this.  
  
Well, I guess that means I will drop that nice hack.

> Username: pauldreik  
> Created_at: 2020-09-06 12:50:02 UTC  
> Updated_at: 2020-09-06 12:50:03 UTC  
> Url: https://github.com/boostorg/json/pull/277#discussion_r484066264  

ok, pushed a new version with the hack dropped.


---

## Comment 10

> Username: vinniefalco  
> Created_at: 2020-09-06 13:07:12 UTC  
> Url: https://github.com/boostorg/json/pull/277#issuecomment-687784996  

Merged thanks

---
