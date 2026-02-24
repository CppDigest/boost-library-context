# #183 Backporting fixed boost::python::exec_file to v1.58.0 [Closed]

> Username: aambrosano  
> Created at: 2018-01-17 11:40:11 UTC  
> Updated at: 2018-04-09 01:09:51 UTC  
> Closed at: 2018-04-09 01:09:51 UTC  
> Url: https://github.com/boostorg/python/pull/183  

Backporting src/exec.cpp from boost 1.59 to 1.58 to fix segmentation fault. It should allow any Ubuntu 16.04 (shipping boost 1.58) user to run boost::python::exec_file without segmentation faults.  
  
The fix is related to #15 and #19.  
  
I guess boost 1.59 has been already tested so there shouldn't be any problem, but in any case [here](https://github.com/boostorg/python/files/1638758/test_script.txt) is a script for testing the change with python 2.7.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-01-17 12:47:52 UTC  
> Url: https://github.com/boostorg/python/pull/183#issuecomment-358293954  

I don't quite understand what you are trying to do here. You mention "backporting from 1.59 to 1.58", but you submit a PR against develop, which is in sync with the latest release, i.e. 1.66. If you want to backport anything, shouldn't it be *from* develop, to some other stable branch somewhere else ?

---

## Comment 2

> Username: aambrosano  
> Created_at: 2018-01-17 13:38:04 UTC  
> Url: https://github.com/boostorg/python/pull/183#issuecomment-358306633  

What I am trying to do is to update only boost 1.58 related commits, but as I see there isn't any branch for old releases, just the "boost_1_xx_0" tags, that's where I got it wrong.  
  
Everything went apparently fine on my side until I got to the pull request phase (I forked and patched starting from boost_1_58_0), but now I would need to merge on top of a "boost_1_58_0" _branch_ in order to update only the source code for the old version. Do you think anything like this is possible?

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-01-17 13:45:38 UTC  
> Url: https://github.com/boostorg/python/pull/183#issuecomment-358308693  

If I understand correctly you want to patch the 1.58 release (with commits from the `develop` branch). What do you want to do with that patched version ? Will that be the basis for new Ubuntu packages ? Or just your personal build ?  
As a start, you may want to fork this repo, then you can add a "1.58" branch to which you can backport (or "cherry-pick") any later commits. As you say, this repo doesn't have stable branches for past releases which would serve similar purposes, it only has tags.  
If such branches become useful to more people, we may consider merging them back into this repo. But as of now, I'm hesitant, as this implies additional maintenance work, which I'd prefer to delegate to those who need it (e.g., you).

---
