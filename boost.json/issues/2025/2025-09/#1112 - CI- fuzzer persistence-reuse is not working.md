# #1112 - CI: fuzzer persistence/reuse is not working [Closed]

> Username: Flamefire  
> Created at: 2025-09-25 14:03:21 UTC  
> Updated at: 2025-10-24 15:58:12 UTC  
> Closed at: 2025-10-24 15:58:12 UTC  
> Url: https://github.com/boostorg/json/issues/1112  

https://www.boost.org/doc/contributor-guide/testing/fuzzing.html describes the fuzzing process in great detail citing [Boost.JSON CI configs](https://github.com/boostorg/json/blob/develop/.github/workflows/run_fuzzer.yml) as a good example  
  
However it seems the persisting and restoring of the corpus is not working, is it?  
  
- The cache action restores head/fuzzing/corpus.tar  
- The pack step puts the `cmin` folder into `corpus.tar.tmp`, then moves it to head/fuzzing  
- The upload-artifact step uploads head/fuzzing/corpus.tar  
  
The crucial missing steps are the extraction of a potentially restored corpus and using `corpus.tar` as the file name.  
  
See [this run](https://github.com/boostorg/json/actions/runs/17646114677/job/50144391537#step:8:31)  
> Warning: No files were found with the provided path: head/fuzzing/corpus.tar. No artifacts will be uploaded.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-09-29 12:25:00 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3346652929  

Now that I fixed it in #1115, I wonder if I actually need to save the corpus in an artifact. The important part is saving it in the cache. What would be the utility for the artifact? AFAIK crash dumps contain all the necessary information to recreate the crash.

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-09-29 15:19:15 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3347611074  

> What would be the utility for the artifact? AFAIK crash dumps contain all the necessary information to recreate the crash.  
  
Do they? With the artifact you have an input to use for a test case and/or the permanent corpus (https://www.boost.org/doc/contributor-guide/testing/fuzzing.html#_handling_crashes)  
  
> When a crash is detected, you should save the offending sample to source control, reproduce the crash, and fix your code. During regression testing, you should make your fuzzer run that specific sample, to verify that the crash doesn’t happen again.  
  
> The important part is saving it in the cache.   
  
The cache can go away at any time so you'd loose that sample for regression tests

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-09-29 15:35:08 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3347694848  

> When a crash is detected, you should save the offending sample to source control, reproduce the crash, and fix your code. During regression testing, you should make your fuzzer run that specific sample, to verify that the crash doesn’t happen again.  
  
The paragraph above has this:  
  
> If your fuzzer finds an input that makes your code crash, it will report the error and exit immediately, creating a file named crash-<id> **containing the sample that caused the problem**.

---

## Comment 4

> Username: grisumbras  
> Created at: 2025-09-29 15:36:27 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3347701579  

> The cache can go away at any time so you'd loose that sample for regression tests  
  
This is actually a good point. I wonder if there's any point in using cache altogether. The goal of the cache is speeding up the job. I'm not sure that goal can be achieved there.

---

## Comment 5

> Username: Flamefire  
> Created at: 2025-09-30 07:10:57 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3350326748  

> The paragraph above has this:  
>>If your fuzzer finds an input that makes your code crash, it will report the error and exit immediately, creating a file named crash- >>containing the sample that caused the problem.  
  
But without the artifact where do you get that crash dump and sample from? That is what's uploaded as the artifact, isn't it?  
  
> This is actually a good point. I wonder if there's any point in using cache altogether. The goal of the cache is speeding up the job. I'm not sure that goal can be achieved there.  
  
That is exactly what it is meant to do: The next time a job runs it starts from a more complete corpus than before increasing the effectiveness of the fuzzer by reusing those samples.      
At least that is the intention. Currently the corpus is (possibly) restored, with your change it will also be updated. But what is still missing is a step that on cache hit extracts the corpus in a way that it actually gets used.

---

## Comment 6

> Username: grisumbras  
> Created at: 2025-09-30 11:30:02 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3351548198  

I've intentionally introduced a bug that was previously caught by fuzzing.  
The source line that invokes UB: https://github.com/boostorg/json/actions/runs/18127549432/job/51586170413?pr=1115#step:6:2342  
The input that causes it: https://github.com/boostorg/json/actions/runs/18127549432/job/51586170413?pr=1115#step:6:2346  
The file with the input: https://github.com/boostorg/json/actions/runs/18127549432/job/51586170413?pr=1115#step:6:2347  
The archive with that file: https://github.com/boostorg/json/actions/runs/18127549432/artifacts/4142303859  
  
So, it seems like everything that's needed to reproduce the issue is there.  
  
> That is exactly what it is meant to do: The next time a job runs it starts from a more complete corpus than before increasing the effectiveness of the fuzzer by reusing those samples.  
  
But earlier you correctly commented that the cache can be dropped at any point randomly. So, using _cache_ for this seems to be the wrong approach. It seems, I should replace usage of cache with an artifact download/upload.

---

## Comment 7

> Username: Flamefire  
> Created at: 2025-09-30 12:17:39 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3351760622  

There seems to be some confusion on what is replied on. I'll put it together:  
  
> When a crash is detected, you should save the offending sample to source control, reproduce the crash, and fix your code. During regression testing, you should make your fuzzer run that specific sample, to verify that the crash doesn’t happen again.  
  
...  
  
>   
  
> > > > >> Now that I fixed it in https://github.com/boostorg/json/pull/1115, I wonder if I actually need to save the corpus in an artifact. The important part is saving it in the cache.  
> > > > >>  
> > > > > The cache can go away at any time so you'd loose that sample for regression tests  
> > >  
> > > This is actually a good point. I wonder if there's any point in using cache altogether. The goal of the cache is speeding up the job. I'm not sure that goal can be achieved there.  
> >  
> > Without the artifact where do you get that crash dump and sample from?  
>  
>  The archive with that file: https://github.com/boostorg/json/actions/runs/18127549432/artifacts/4142303859  
> So, it seems like everything that's needed to reproduce the issue is there.  
  
The is the artifact. You cannot download the cache, only artifacts. So it is important to save **failures** in artifacts in order to put them in the permanent corpus inside the git repository.  
  
The cache is used to speed up fuzzing: You start with only the git corpus on the first run (or when the cache got purged). fuzzing will add more samples to the corpus and minimize them at the end removing "duplicates". This is saved to the cache. On the next run the fuzzer starts from this enhanced corpus exploring more parts of the code in the first iteration than with the git corpus.  
  
TLDR:  
- Cache is for storing the corpus created during previous CI runs to make future runs more efficient by starting from a corpus that is a compressed version of a large randomly created corpus. The runtime of the fuzzer is [limited](https://github.com/boostorg/json/blob/a6fbf10e1f5c28bf11b0061f19fe99f6f54a1a9d/fuzzing/Jamfile#L29) so you basically get to run the fuzzer effectively twice as long on the first cache reuse  
- Artifacts are samples showing actual issues in the code. They should be downloaded and added as a test case and/or the permanent corpus comited to git, (see `old_crashes` folder)

---

## Comment 8

> Username: grisumbras  
> Created at: 2025-09-30 13:00:04 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3352005175  

Okay, so we both agree that putting crash dumps into an artifact + having job logs is enough to recreate the problematic case, right? The corpus in its entirety is not needed for that.  
  
Now, regarding cache.  
  
1. JSON takes the original corpus (in seedcorpus directory) from tests.  
2. When the fuzzers run they create random new files (in out directory) if they increase coverage.  
3. When the fuzzers run again they do the same as on step 2 (but into cmin directory), but also copy files from the original corpus.  
4. The files from cmin are then added on future runs _in addition to the existing corpus_.  
  
So, as far as I can tell, step 1 makes it impossible for the cache from step 4 to speed up the fuzzing. Also, now that I look at it, I'm not sure there's any point in step 2. And I question whether it is a good approach to treat the new files created on steps 2 and 3 as just "cache". If they are valuable, shouldn't they be kept as an artifact, so that they won't disappear randomly?

---

## Comment 9

> Username: grisumbras  
> Created at: 2025-09-30 14:13:04 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3352405868  

> If they are valuable, shouldn't they be kept as an artifact, so that they won't disappear randomly?  
  
Apparently, this is not exactly trivial with GHA. Probably thats's why cache was used originally for this.

---

## Comment 10

> Username: Flamefire  
> Created at: 2025-09-30 14:26:08 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3352471893  

> Now, regarding cache.  
>   
>     1. JSON takes the original corpus (in seedcorpus directory) from tests.  
>   
>     2. When the fuzzers run they create random new files (in out directory) if they increase coverage.  
>   
>     3. When the fuzzers run again they do the same as on step 2 (but into cmin directory), but also copy files from the original corpus.  
  
No, this step is deduplication of the random files based on if/how they increase coverage. In step 2 the files are still quite random although the fuzzer makes an effort to produce meaningful ones.  
  
>     4. The files from cmin are then added on future runs _in addition to the existing corpus_.  
  
Exactly.  
  
> So, as far as I can tell, step 1 makes it impossible for the cache from step 4 to speed up the fuzzing.  
  
What is (currently) missing in step 1 is the extraction of the corpus saved in step 4. And yes given that the fuzzing starts with a time limit it does not speed up fuzzing in wall clock time. It "just" makes it more effective as it won't start to redundantly create all the samples again that it had already tried in a previous run and can start right away with new mutations.  
  
> Also, now that I look at it, I'm not sure there's any point in step 2.  
  
See above on the distinction of step 2 and 3  
  
> And I question whether it is a good approach to treat the new files created on steps 2 and 3 as just "cache". If they are valuable, shouldn't they be kept as an artifact, so that they won't disappear randomly?  
  
Probably, but that is basically a space-time tradeoff. The most valuable files are the ones that produce crashes.      
And as the fuzzing involves quite a bit of randomness it isn't too bad if it has to restart creating those files which isn't too often anyways  
  
> Apparently, this is not exactly trivial with GHA. Probably thats's why cache was used originally for this.  
  
Indeed. Also I'm not sure how much "arbitrary" storage you get on GHA, even the cache is limitted and least used ones will be evicted when the limit is reached. But see above: Keeping reasonable (manually created) files in the initial corpus together with previous crashes makes a good cold start already.

---

## Comment 11

> Username: grisumbras  
> Created at: 2025-10-02 10:57:17 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3360539081  

> No, this step is deduplication of the random files based on if/how they increase coverage.  
  
What I'm saying is that step 3 is step 2 + filtering of the samples based on their usefulness. This makes me wonder if I should just leave step 3.  
  
> In step 2 the files are still quite random although the fuzzer makes an effort to produce meaningful ones.  
  
Are you saying that step 2 produces "bad" samples, and step 3 produces better samples based on those? Would it then be better to run thrice for 20 seconds rather than twice for 30 seconds? Would it be better yet to run 4 times for 15 seconds?  
  
> What is (currently) missing in step 1 is the extraction of the corpus saved in step 4. And yes given that the fuzzing starts with a time limit it does not speed up fuzzing in wall clock time. It "just" makes it more effective as it won't start to redundantly create all the samples again that it had already tried in a previous run and can start right away with new mutations.  
  
Why would it not redundantly create more samples, when it uses both the cached samples from previous run's `cmin` directory _and_ the samples from the `test` directory?

---

## Comment 12

> Username: Flamefire  
> Created at: 2025-10-02 11:34:36 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3360733393  

> > No, this step is deduplication of the random files based on if/how they increase coverage.  
>   
> What I'm saying is that step 3 is step 2 + filtering of the samples based on their usefulness. This makes me wonder if I should just leave step 3.  
  
Step 2 and 3 are completely different not a combination. Step 3 is ONLY minimizing/compressing samples, no fuzzing. See https://www.boost.org/doc/contributor-guide/testing/fuzzing.html#_corpus_minimization  
  
> Before saving the corpus, we recommend performing corpus minimization .  
> It will run the different samples in your corpus and discard "repeated" ones, based on the code paths they trigger.  
> [...] Note that no actual fuzzing is performed by this command.  
  
  
> > In step 2 the files are still quite random although the fuzzer makes an effort to produce meaningful ones.  
>   
> Are you saying that step 2 produces "bad" samples, and step 3 produces better samples based on those? Would it then be better to run thrice for 20 seconds rather than twice for 30 seconds? Would it be better yet to run 4 times for 15 seconds?  
  
"bad" in some sense. From the guide:  
> LibFuzzer will run your code with [..] with random inputs. It will [...] attempt to generate inputs that maximize [coverage], effectively trying to discover new paths in your code.   
> [Without a corpus] The fuzzer will try random inputs, without any guidance, and will generate a corpus. Doing this is not advisable, though, since it reduces the effectiveness of your fuzzing - the fuzzer may fail to find some relevant inputs.   
  
So step 2 is creating random mutations of the existing corpus guided by existing and newly created samples and their coverage. In the end it might find that some mutations were redundant, so step 3 throws them away to save space (and time) without doing further fuzzing in this step.  
  
> Why would it not redundantly create more samples, when it uses both the cached samples from previous run's `cmin` directory _and_ the samples from the `test` directory?  
  
Because the fuzzer tries to create mutated samples to maximize coverage. The larger the corpus the more information it has to create useful samples instead of being fully random. Think of fuzzing an HTML parser/validator. If you let a fuzzer run without any guidance it takes a lot of random samples until it figures out it needs to provide ASCII input starting with a `<` such that it even passes the very first condition (When the HTML validator e.g. first checks for `<html` or `<!doctype` at the top), so it takes a while until it starts creating "useful" inputs as the number of possible inputs is nearly infinite.     
If you pass it a couple valid HTML pages as the corpus it can mutate those, which has a higher chance of hitting relevant code paths. So the more non-redundant samples it has, the less "random" it will be.  
  
Coming from another direction: I think it should be clear that the longer the fuzzer runs, the more it can discover. The corpus created by a previous run is basically a "checkpoint". So if you run the fuzzer a second time on the corpus created by a 30s run, you'll basically save that 30s and get an effective fuzzing time of 60s. Step 3 simply optimizes the size of the corpus it generated before.     
This is oversimplified of course and there likely is some state missing from one run to the next. So 2 30s runs will likely be better than 3 20s runs.

---

## Comment 13

> Username: grisumbras  
> Created at: 2025-10-02 12:34:01 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3361011255  

> Step 3 is ONLY minimizing/compressing samples, no fuzzing. See https://www.boost.org/doc/contributor-guide/testing/fuzzing.html#_corpus_minimization  
  
Oh! This was very much not clear from the official documentation. I should have read Ruben's article more attentively.  
  
> > Why would it not redundantly create more samples, when it uses both the cached samples from previous run's `cmin` directory _and_ the samples from the `test` directory?  
>   
> ... So the more non-redundant samples it has, the less "random" it will be.  
  
Right. And it seems to me that if I have minimised output from the previous run, the original corpus from tests is redundant.   
  
> Coming from another direction: I think it should be clear that the longer the fuzzer runs, the more it can discover. The corpus created by a previous run is basically a "checkpoint". So if you run the fuzzer a second time on the corpus created by a 30s run, you'll basically save that 30s and get an effective fuzzing time of 60s.   
  
Right. And using both the original corpus and the cache is like simultaneously running from the checkpoint and from the original point.  
  
So, I'm thinking step 1 should be "use cache if it exists, otherwise construct the original corpus".

---

## Comment 14

> Username: Flamefire  
> Created at: 2025-10-02 14:48:29 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3361567416  

> So, I'm thinking step 1 should be "use cache if it exists, otherwise construct the original corpus".  
  
I'm not fully sure. I don't think it hurts to include the original corpus which should include detected crashes just to make sure those are never missed which means potential regressions might not get detected until the cache is flushed or the case is rediscovered.     
Although in theory the minimized corpus should cover that case.

---

## Comment 15

> Username: grisumbras  
> Created at: 2025-10-03 11:23:39 UTC  
> Url: https://github.com/boostorg/json/issues/1112#issuecomment-3365326142  

Okay, sounds sensible. Thank you very much for this discussion.
