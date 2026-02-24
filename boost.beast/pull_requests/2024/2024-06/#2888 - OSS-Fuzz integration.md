# #2888 OSS-Fuzz integration [Merged]

> Username: tyler92  
> Created at: 2024-06-18 19:44:21 UTC  
> Updated at: 2024-06-24 15:52:08 UTC  
> Merged at: 2024-06-20 21:44:20 UTC  
> Closed at: 2024-06-20 21:44:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2888  

As we discussed in #2887, we are adding Boost.Beast to OSS-Fuzz. I plan to proceed with several steps and would appreciate your help. The plan is as follows  
  
- [x] add fuzzing targets (`*.cpp` files with `LLVMFuzzerTestOneInput` entry point)  
- [x] add fuzzing seed (or initial corpus). This consists of a set of files grouped by target. Typically, these are stored as `.zip` or `.tar` files. In our case, I can create three files: `http_request_fuzzer_seed_and_corpus.zip`, `http_response_fuzzer_seed_and_corpus.zip`, and `websocket_server_fuzzer_seed_and_corpus.zip`. OSS-Fuzz expects this format, but we can store these files in our Git repository in any way that suits us. For example, Boost.URL [uses a single tar file](https://github.com/boostorg/url/tree/50f7247e7c47d1b34d166b7d0cc92c186341ad18/test/fuzz) with directories inside.  
- [x] add CMakeLists.txt and/or Jamfile. The specifics depend on who will build the fuzzers manually. We can create a CMakeLists.txt or Jamfile that simply compiles fuzzers, or we can implement more complex scenarios [similar to what we have done for Boost.URL](https://github.com/boostorg/url/blob/50f7247e7c47d1b34d166b7d0cc92c186341ad18/test/fuzz/CMakeLists.txt). I would be glad to receive your advice on this matter.  
- [x] add fuzzers to CI. According to the [official documentation](https://google.github.io/clusterfuzzlite/build-integration/), we need to create three files in `.clusterfuzzlite` directory and create a [workflows file](https://google.github.io/clusterfuzzlite/running-clusterfuzzlite/github-actions/). However, @ashtum mentioned that another approach might be used. An example of clusterfuzzlite usage: [link](https://github.com/jonathanmetzman/clusterfuzzlite-example/tree/main).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-18 20:52:44 UTC  
> Updated_at: 2024-06-19 22:42:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2176952429  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2888?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.23%. Comparing base [(`e7f4919`)](https://app.codecov.io/gh/boostorg/beast/commit/e7f49190ef12cbda6c809624b8c10a05f8c8a284?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`35f9e9c`)](https://app.codecov.io/gh/boostorg/beast/commit/35f9e9c9de48927a48f954e6b599cd0c2e39464e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2888/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2888?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2888   +/-   ##  
========================================  
  Coverage    93.23%   93.23%             
========================================  
  Files          177      177             
  Lines        13675    13675             
========================================  
  Hits         12750    12750             
  Misses         925      925             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2888?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2888?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e7f4919...35f9e9c](https://app.codecov.io/gh/boostorg/beast/pull/2888?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: tyler92  
> Created_at: 2024-06-18 21:49:53 UTC  
> Updated_at: 2024-06-18 21:54:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2177136025  

I added a draft version for `.clusterfuzzlite` directory. Obviously the Dockerfile has to be updated for the CI environment (instead of `git clone`), but it can be used as a starting point. Please let me know if it makes sense and we can continue with this approach.  
  
Here is some information about how to test it locally: https://google.github.io/clusterfuzzlite/build-integration/#testing-locally  
  
```  
python infra/helper.py build_image --external $PATH_TO_PROJECT  
python infra/helper.py build_fuzzers --external $PATH_TO_PROJECT --sanitizer address  
  
# OK  
python infra/helper.py run_fuzzer --external --corpus-dir /tmp/corpus $PATH_TO_PROJECT http_request_fuzzer  
  
# Will fail due to https://github.com/boostorg/beast/pull/2881  
python infra/helper.py run_fuzzer --external --corpus-dir /tmp/corpus $PATH_TO_PROJECT websocket_server_fuzzer  
```

---

## Comment 3

> Username: ashtum  
> Created_at: 2024-06-19 16:35:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2179100898  

@tyler92 If I understand correctly, we don't need to add the `.clusterfuzzlite` directory to Beast if we decide to build and run fuzzer targets ourselves, as we do for Boost.URL. Therefore, the contents of the `.clusterfuzzlite` directory belong to projects/boost-beast directory in [oss-fuzz](https://github.com/google/oss-fuzz) repository. Is that correct?

---

## Comment 4

> Username: tyler92  
> Created_at: 2024-06-19 17:16:46 UTC  
> Updated_at: 2024-06-19 17:18:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2179196220  

> @tyler92 If I understand correctly, we don't need to add the `.clusterfuzzlite` directory to Beast if we decide to build and run fuzzer targets ourselves, as we do for Boost.URL. Therefore, the contents of the `.clusterfuzzlite` directory belong to projects/boost-beast directory in [oss-fuzz](https://github.com/google/oss-fuzz) repository. Is that correct?  
  
Yes, you are right. There are two options:  
  
1. Using integration [ClusterFuzzLite](https://google.github.io/clusterfuzzlite/) which requires this directory.  
2. Building and running fuzzers manually. In this case, the content of `.clusterfuzzlite` is moved to [oss-fuzz](https://github.com/google/oss-fuzz) repository. I can do it after we finish with this PR. Beast needs to contain only the fuzzer targets and corpus (which is already done).  
  
The first option should be easier, but the second one is probably more flexible and requires additional effort. Let me know which is better for Beast, and I will move the ClusterFuzzLite-related files accordingly. I would probably ask for your help if we choose the second option because I'm not very familiar with the Beast's build system.

---

## Comment 5

> Username: tyler92  
> Created_at: 2024-06-20 18:41:14 UTC  
> Updated_at: 2024-06-20 18:42:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181310401  

@ashtum Should I add a CMakeLists.txt similar to [Boost.URL](https://github.com/boostorg/url/blob/50f7247e7c47d1b34d166b7d0cc92c186341ad18/test/fuzz/CMakeLists.txt)?

---

## Comment 6

> Username: ashtum  
> Created_at: 2024-06-20 18:52:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181330840  

> @ashtum Should I add a CMakeLists.txt similar to [Boost.URL](https://github.com/boostorg/url/blob/50f7247e7c47d1b34d166b7d0cc92c186341ad18/test/fuzz/CMakeLists.txt)?  
  
There is also https://github.com/boostorg/json/blob/develop/fuzzing/Jamfile, which looks solid. I'm currently experimenting with these and testing them in CI. The problem with using ClusterFuzzLite is that it requires an additional repository to save corpus files and cannot use the GHA cache feature.

---

## Comment 7

> Username: tyler92  
> Created_at: 2024-06-20 20:34:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181512355  

OK, just for reference - PR for oss-fuzz https://github.com/google/oss-fuzz/pull/12109 which is WIP until this PR is merged (it requires fuzzing targets and seeds from Beast's repo).

---

## Comment 8

> Username: ashtum  
> Created_at: 2024-06-20 20:50:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181540558  

> OK, just for reference - PR for oss-fuzz [google/oss-fuzz#12109](https://github.com/google/oss-fuzz/pull/12109) which is WIP until this PR is merged (it requires fuzzing targets and seeds from Beast's repo).  
  
It looks good thank you. I think we might want to remove this line because the Boost repository is checked out at master:  
https://github.com/google/oss-fuzz/pull/12109/files#diff-2d7de9a413e01b701ddb0903c9c94831e32b6c2a47c0b9f9a2d5a433752310b6R20  
  
```  
RUN git -C libs/beast checkout develop  
```  
  
In the next step, please remove the `.clusterfuzzlite` directory from this PR and force push a single commit that adds the `test/fuzz` directory. I want to merge your PR so you can continue your work on https://github.com/google/oss-fuzz/pull/12109. I'll setup the CI and build scripts in a separate commit.

---

## Comment 9

> Username: tyler92  
> Created_at: 2024-06-20 21:07:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181564878  

> In the next step, please remove the `.clusterfuzzlite` directory from this PR and force push a single commit that adds the `test/fuzz` directory. I want to merge your PR so you can continue your work on [google/oss-fuzz#12109](https://github.com/google/oss-fuzz/pull/12109). I'll setup the CI and build scripts in a separate commit.  
  
Done  
  
> It looks good thank you. I think we might want to remove this line because the Boost repository is checked out at master: https://github.com/google/oss-fuzz/pull/12109/files#diff-2d7de9a413e01b701ddb0903c9c94831e32b6c2a47c0b9f9a2d5a433752310b6R20  
>   
> ```  
> RUN git -C libs/beast checkout develop  
> ```  
  
OK, sure, let me do that after we merge this PR to let oss-fuzz checks pass. BTW: do I need to add you to [auto_ccs](https://github.com/google/oss-fuzz/blob/master/docs/getting-started/new_project_guide.md#auto_ccs) in project.yaml there?

---

## Comment 10

> Username: ashtum  
> Created_at: 2024-06-20 21:34:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181598983  

Sorry, I forgot to mention that there is no need for the `_fuzzer` suffix for cpp files, as they are all in the `test/fuzz` directory. Please remember to update `seeds.tar` afterward.  
  
> OK, sure, let me do that after we merge this PR to let oss-fuzz checks pass. BTW: do I need to add you to [auto_ccs](https://github.com/google/oss-fuzz/blob/master/docs/getting-started/new_project_guide.md#auto_ccs) in project.yaml there?  
  
Yes please, ashtumashtum@gmail.com.

---

## Comment 11

> Username: tyler92  
> Created_at: 2024-06-20 21:40:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2181606055  

> Sorry, I forgot to mention that there is no need for the `_fuzzer` suffix for cpp files, as they are all in the `test/fuzz` directory. Please remember to update `seeds.tar` afterward.  
  
Fixed

---

## Comment 12

> Username: tyler92  
> Created_at: 2024-06-21 17:38:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2183170358  

> > OK, just for reference - PR for oss-fuzz [google/oss-fuzz#12109](https://github.com/google/oss-fuzz/pull/12109) which is WIP until this PR is merged (it requires fuzzing targets and seeds from Beast's repo).  
>   
> It looks good thank you. I think we might want to remove this line because the Boost repository is checked out at master: https://github.com/google/oss-fuzz/pull/12109/files#diff-2d7de9a413e01b701ddb0903c9c94831e32b6c2a47c0b9f9a2d5a433752310b6R20  
>   
> ```  
> RUN git -C libs/beast checkout develop  
> ```  
  
@ashtum Are you sure it's better to use master branch there? E.g. boost-json project [has 'develop' branch and master for Boost](https://github.com/google/oss-fuzz/blob/3b9936f940aa0903d89742510b146e576b9907fb/projects/boost-json/Dockerfile#L25). I don't know if this is correct, but probably it's better to use 'develop' for both Beast and Boost to detect issues as early as possible before they are merged to master. I might be not so familiar with the branch policy here, what's your opinion?

---

## Comment 13

> Username: ashtum  
> Created_at: 2024-06-21 18:25:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2888#issuecomment-2183244871  

> @ashtum Are you sure it's better to use master branch there? E.g. boost-json project [has 'develop' branch and master for Boost](https://github.com/google/oss-fuzz/blob/3b9936f940aa0903d89742510b146e576b9907fb/projects/boost-json/Dockerfile#L25). I don't know if this is correct, but probably it's better to use 'develop' for both Beast and Boost to detect issues as early as possible before they are merged to master. I might be not so familiar with the branch policy here, what's your opinion?  
  
There is a possibility that if we get a breaking change in one of the dependencies and update Beast in the develop branch, it will fail when trying to build it against Boost master. However, I would say this is a rare possibility and would be transient if it occurs. So you might want to keep it as it is (checking out the develop branch).

---
