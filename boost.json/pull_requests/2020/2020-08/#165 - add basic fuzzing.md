# #165 add basic fuzzing [Closed]

> Username: pauldreik  
> Created at: 2020-08-18 09:08:30 UTC  
> Updated at: 2020-08-22 15:38:12 UTC  
> Closed at: 2020-08-22 14:24:54 UTC  
> Url: https://github.com/boostorg/json/pull/165  

This adds fuzzing. No bugs are detected in the current version, the old ones detected by fuzzing have been fixed.  
Closes #21   
Closes #18   
  
The fuzzing is run in a github action with persistent storage of the corpus on bintray between runs.  
  
The bintray account has to be setup in several places for this to work  
 - the project has to be created on bintray.  On my account, I set it up as repository boost.json, package corpus, version 0. I then added a file manually in the web interface (you can download mine to have a file to start with). This made the path to the corpus be https://bintray.com/pauldreik/boost.json/corpus/0#files/corpus  
- the username of bintray has to be adapted in the github action file (the environment variable BINTRAYUSER)  
- the api key of bintray (available under the bintray profile page) has to be added as a secret in the repository settings under name bintrayApiKey  
  
I have test run this action on my github clone and it seems to work, although I am not sure the crontab works as intended.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-08-19 23:30:27 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-676809492  

Well... my apologies but in the final touches for getting ready for Boost review, we have refactored the `basic_parser` which is now a class template. This should be in develop. Have a look at the `null_parser` in the example to see how it is used.

---

## Comment 2

> Username: pauldreik  
> Created_at: 2020-08-20 07:49:49 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-677435587  

When fixing up this branch for the latest changes in develop, I came across a new bug.  
  
I filed it as #176   
  
The good thing is that the github action catches this, since it reruns old crashes. So that's a win to avoid reintroducing old bugs in the future.

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-20 14:57:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/165#pullrequestreview-471700332  

📁 fuzzing/fuzz_parser.cpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2020-08-20 14:57:18 UTC  
> Updated_at: 2020-08-21 11:05:33 UTC  
> Url: https://github.com/boostorg/json/pull/165#discussion_r474047770  

Consider using your name if you want


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-20 14:57:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/165#pullrequestreview-471700567  

📁 fuzzing/fuzz_parser.cpp

```diff
  10 |+ /*
  11 |+     This example verifies that a file contains valid JSON.
  12 |+     It is implementing by subclassing basic_parser
```

> Username: vinniefalco  
> Created_at: 2020-08-20 14:57:32 UTC  
> Updated_at: 2020-08-21 11:05:33 UTC  
> Url: https://github.com/boostorg/json/pull/165#discussion_r474048079  

This comment is no longer accurate, and should just be deleted


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-08-20 15:02:32 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-677720040  

We need to make some adjustments to this. First, it needs to be all squashed down into one commit. Second, we need the fuzz program to be built along with the tests, and it needs to have its own Visual Studio Project that is part of the enclosing Solution. Otherwise, if we refactor or make changes, the fuzz sources will not be part of the workflow and we won't know that it breaks.  
  
For now I think we should make it an example, e.g. `example/fuzz.cpp` and add the appropriate lines in the Jamfile and CMakeLists.txt here:  
  
https://github.com/CPPAlliance/json/blob/develop/example/CMakeLists.txt  
https://github.com/CPPAlliance/json/blob/develop/example/Jamfile  
  
This will ensure that it shows up as a project in the VS IDE, and it will also get built via Travis, Appveyor, and Azure Pipelines thus alerting us if there is ever an error. To be clear, these CI systems are merely compiling the fuzz program (like the other examples) to make sure there are no errors, they are not performing the actual fuzzing.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2020-08-20 23:08:46 UTC  
> Updated_at: 2020-08-21 10:08:46 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-677949959  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=h1) Report  
> Merging [#165](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/1d10aedb8ded0c3a2f5db160d7d0095f3982815f?el=desc) will **increase** coverage by `0.24%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/165/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #165      +/-   ##  
===========================================  
+ Coverage    98.07%   98.32%   +0.24%       
===========================================  
  Files           63       64       +1       
  Lines         5779     5723      -56       
===========================================  
- Hits          5668     5627      -41       
+ Misses         111       96      -15       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `93.54% <0.00%> (-2.92%)` | :arrow_down: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `99.66% <0.00%> (ø)` | |  
| [include/boost/json/detail/buffer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9idWZmZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value\_builder.hpp](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2J1aWxkZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| ... and [3 more](https://codecov.io/gh/CPPAlliance/json/pull/165/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=footer). Last update [1d10aed...71beb9d](https://codecov.io/gh/CPPAlliance/json/pull/165?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2020-08-21 10:16:21 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-678183198  

An automated preview of the documentation is available at [http://165.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://165.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: pauldreik  
> Created_at: 2020-08-21 10:53:45 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-678231128  

Thanks for the code comments, fixed them!  
  
I added the fuzzer source files to cmake, I assume that is what you meant with the Visual Studio comment?  
  
I also added a jamfile, but I do not really know how to test it locally and I am unfortunately not familiar with that build system.

---

## Comment 9

> Username: pauldreik  
> Created_at: 2020-08-22 05:51:30 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-678599398  

I have fuzzed the three targets locally for more than 12 hours each, no issues found, good!  
Uploaded the corpus manually to bintray so the github actions job can benefit from it.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2020-08-22 14:24:54 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-678647322  

This is merged, thanks!

---

## Comment 11

> Username: pauldreik  
> Created_at: 2020-08-22 15:38:04 UTC  
> Url: https://github.com/boostorg/json/pull/165#issuecomment-678655318  

Nice having it merged! Don't forget to setup the bintray credentials and path as described above, otherwise the corpus won't be updated over time since this repo does not have credentials to my bintray account.

---
