# #135 Add a vcs module, with Git and Subversion support. [Closed]

> Username: tee3  
> Created at: 2016-08-06 02:16:26 UTC  
> Updated at: 2021-10-02 20:59:19 UTC  
> Closed at: 2021-06-11 13:33:21 UTC  
> Url: https://github.com/boostorg/build/pull/135  

This implements a `vcs` module within Boost.Build with back ends for Git and Subversion.  One of the future goals of this is to support a source package management system in Boost.Build with support for getting source code from repositories.  
  
There are some minor outstanding issues as well:  
- [x] The `vcs` module BoostBook documentation has not been built.  
    
  Will this be done via continuous integration?  I'm sure there's going to be an escaping issue with one of the `programlisting` sections.  
- [x] The `vcs` module has not been added to the test suite.  
    
  The examples have been used to test it and I have tests outside of the Boost.Build test system.  Does anyone have an idea of how this can be added in?  
- [ ] The `type` rule fails on Windows since `path.exists` fails the first time it is run.  
    
  This causes the exhaustive example to fail the first time it is run, but successive runs will eventually get through the entire example.  
- [ ] This has not been implemented in Python.  
    
  It should be straightforward to implement, but seemed redundant and I couldn't get myself to do it :)  
  
From the commit message:  
  
```  
This provides a mechanism to interact with VCS-controlled projects.  It  
currently supports Git and Subversion.  The main interesting feature  
here is a rule to generate a version string.  There are other rules for  
querying a directory for its properties that may also prove generally  
useful.  
  
There are also rules to 'fetch' from a repository into a directory and  
'checkout' a symbolic reference from a directory.  These could be used  
to forcibly get a specific revision of a package from a repository.  It  
could also be used to create a proper source package management system  
within Boost.Build.  
  
There are two examples to show how this module might be used.  
  
The documentation build has not been tested.  However, a  
reStructuredText document that should be identical to the BoostBook file  
is included in the doc directory for reference.  
  
There are no tests, but there are two examples that do quite a bit of  
work to test the module, but do not verify the results.  
  
This has been tested on Linux, OS X, and Windows.  The 'type' rule fails  
on Windows due to `path.exists' rule failing.  This needs to be  
investigated.  
  
This does not implement these functions in Python.  It should be  
straightforward to do so.  
```

---

## Comment 1

> Username: tee3  
> Created_at: 2016-08-21 18:55:06 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-241274779  

Squash to a single commit since no comments yet.

---

## Comment 2

> Username: tee3  
> Created_at: 2016-09-04 19:05:56 UTC  
> Updated_at: 2016-09-04 19:06:05 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-244622534  

Rebased against latest develop since no comments yet.

---

## Comment 3

> Username: tee3  
> Created_at: 2017-04-02 15:38:48 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-290994177  

Build failures on Windows are not related to the ``vcs`` module.

---

## Comment 4

> Username: tee3  
> Created_at: 2017-04-03 01:58:52 UTC  
> Updated_at: 2017-04-03 02:09:03 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-291033125  

This now has documentation and tests.  Does the python implementation need to be kept up to date for this to get in?  
  
Is anyone interested in something like this?  I am slowly working on a really simple "package manager" based on Boost.Build.  I have pieces of that, but it still needs some work.  This is the most basic piece to be able to run vcs operations from within Boost.Build.

---

## Comment 5

> Username: tee3  
> Created_at: 2017-05-31 14:59:21 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-305213912  

Looks like this is failing on Windows.  I will take a look at it soon.

---

## Comment 6

> Username: swatanabe  
> Created_at: 2018-01-09 15:07:21 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-356310479  

The main issue with this is that Boost.Build does not like it at all when the filesystem changes outside of its direct control.  Lots of things are cached internally and changes will often be ignored. (This is probably why path.exists fails.)

---

## Comment 7

> Username: tee3  
> Created_at: 2018-01-09 15:48:52 UTC  
> Updated_at: 2018-01-09 15:49:00 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-356323369  

Is it your opinion that this concept is infeasible and should be abandoned in favor of a some external "package management" system?

---

## Comment 8

> Username: swatanabe  
> Created_at: 2018-01-09 23:05:15 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-356443516  

AMDG  
  
On 01/09/2018 08:48 AM, tee3 wrote:  
> Is it your opinio that this concept is infeasible and should be abandoned in favor of a some external "package management" system?  
>   
  
  For this to work, you need a way to tell b2 about the  
changes that are made, which means that they need  
to be defined as targets and updated via actions,  
somehow.  This is currently impossible, without some  
changes to the engine:  
- A target that has a LOCATE setting can be found by GLOB.  
  (This is something that I've wanted to allow for a while,  
  but it requires significant changes to the way LOCATE  
  is handled)  
- There needs to be some way to determine additional files  
  created by an action after the action completes.  
  perhaps something like:  
  rule checkout ( targets * : sources * : properties * )  
  {  
    __TARGETS__ on $(t) = deduce-targets ;  
  }  
  actions checkout  
  {  
    git/svn checkout ...  
  }  
  rule deduce-targets ( targets * : stdout-lines * )  
  {  
    return a list of targets created. ;  
  }  
  
I'm not going to say that it's impossible, but it's a significant  
amount of effort, and I don't think I'm going to get around to  
it any time soon.  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: tee3  
> Created_at: 2018-01-10 02:08:29 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-356477757  

Thanks for your expertise and time @swatanabe!  I think I’ll just leave it open for such a time where it might be possible unless someone feels it should be closed now.

---

## Comment 10

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:49 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-850859492  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 11

> Username: tee3  
> Created_at: 2021-05-31 00:06:03 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-851084351  

Is there something I can do to transition this?

---

## Comment 12

> Username: grafikrobot  
> Created_at: 2021-05-31 01:39:43 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-851110833  

> Is there something I can do to transition this?  
  
If you can open a corresponding PR on the new location. It's possible to merge between repos as long as you have both remotes in the same git tree.

---

## Comment 13

> Username: tee3  
> Created_at: 2021-05-31 17:23:56 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-851607055  

I have opened a pull request at bfgroup/b2#33 .  
  
@grafikrobot Should we close this pull request now?  I reference it from the new pull request to keep the history and the valuable comments from the b2 team.

---

## Comment 14

> Username: stale[bot]  
> Created_at: 2021-06-11 01:54:26 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-859203088  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 15

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:18 UTC  
> Url: https://github.com/boostorg/build/pull/135#issuecomment-932819619  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
