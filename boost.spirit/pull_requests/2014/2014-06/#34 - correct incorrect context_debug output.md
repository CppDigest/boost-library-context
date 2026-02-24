# #34 correct incorrect context_debug output [Closed]

> Username: cppljevans  
> Created at: 2014-06-05 19:06:03 UTC  
> Updated at: 2017-12-08 15:56:18 UTC  
> Closed at: 2017-12-08 15:56:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/34  

The problem which this solves was discussed in post:  
  
http://article.gmane.org/gmane.comp.parsers.spirit.general/26803  
  
In summary, the problem occurs because the statement:  
  
  traits::post_transform(attr, attr_);  
  
swaps the arguments (at least when they're recursive variants)  
instead of copying source(attr_) to target(attr).  
  
Also, change argument to context_debug from made_attr to  
attr_ because that makes it clearer what's happening since  
attr_ is what is explicitly passed to parse_rhs.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-06-06 01:22:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45294056  

250+ commits? Something is wrong... Could you point me to the commits in your fork that you'd wish to include here?

---

## Comment 2

> Username: cppljevans  
> Created_at: 2014-06-06 01:43:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45295046  

On 06/05/14 20:22, Agustín Bergé wrote:  
  
> 250+ commits? Something is wrong... Could you point me to the commits   
> in your fork that you'd wish to include here?  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/spirit/pull/34#issuecomment-45294056.  
>   
> This:  
  
https://github.com/cppljevans/spirit/commit/f8a8dfe399bfb9886bc903b84f411745dd919af2  
  
looks like the changes I made.  
  
Sorry for the confusion. I'm new to git.  
  
-regards,  
Larry

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-06-06 01:52:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45295509  

It looks like your pull request is based on the master branch instead of develop. Also, is customary to use separate branches for pull requests as they are 'live' until closed.  
  
You could try creating a new branch based on the latest develop branch and issue a new pull request. Ask here if  you need any help with that.

---

## Comment 4

> Username: cppljevans  
> Created_at: 2014-06-06 18:49:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45371560  

On 06/05/14 20:52, Agustín Bergé wrote:  
  
> It looks like your pull request is based on the master branch  
> instead of develop. Also, is customary to use separate branches for  
> pull requests as they are 'live' until closed.  
>   
> You could try creating a new branch based on the latest develop  
> branch and issue a new pull request. Ask here if you need any help  
> with that.  
>   
> —  
> Reply to this email directly or view it on GitHub.  
  
What I'd done was create a fork instead of branch because I'd read here:  
  
https://help.github.com/articles/fork-a-repo  
  
that forking was a way to:  
  
"contribute to someone else's project"  
  
I guess that was wrong.  
  
Hence, I'm guessing I should have done this:  
  
git clone https://github.com/boostorg/spirit.git  
#clone spirit  
cd spirit  
git checkout develop  
#switch to develop branch  
git branch context_debug_fix  
#create a new branch of the develop branch  
#to contain fix to context_debug in detail/rule.hpp  
  
This is what I infer from looking here:  
  
http://www.gitguys.com/topics/switching-branches-without-committing/  
  
Is that right?  
  
Thanks for the help, Agustin.  
  
-regards,  
Larry

---

## Comment 5

> Username: K-ballo  
> Created_at: 2014-06-06 19:06:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45373221  

> What I'd done was create a fork instead of branch because I'd read here: https://help.github.com/articles/fork-a-repo that forking was a way to:  
  
Forking will be necessary since you do not have write access to the spirit repository. You'd clone your fork, create a new branch (and switch to it), apply the changes and commit & push to your fork. Then you can issue a pull requests from your branch (say `cppljevans:context_debug_fix`) against the `boostorg:develop` branch. When issuing the pull request, the first step will be the comparison of the two branches, where you can check that the diff matches your intentions.

---

## Comment 6

> Username: cppljevans  
> Created_at: 2014-06-06 19:40:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45376379  

On 06/06/14 14:06, Agustín Bergé wrote:  
  
> ```  
> What I'd done was create a fork instead of branch because I'd  
> read here: https://help.github.com/articles/fork-a-repo that  
> forking was a way to:  
> ```  
>   
> Forking will be necessary since you do not have write access to the  
> spirit repository. You'd clone your fork, create a new branch  
  
Based on your remark:  
  
   It looks like your pull request is based on the master branch  
   instead of develop.  
  
made on:  
  
   Date: Thu, 05 Jun 2014 18:52:30 -0700  
  
Shouldn't I first switch to the develop branch, with something like:  
  
   git checkout develop  
  
and then create my new branch with:  
  
   git branch context_debug_fix  
  
?  
  
> (and switch to it), apply the changes and commit & push to your  
> fork. Then you can issue a pull requests from your branch (say  
> cppljevans:context_debug_fix) against the boostorg:develop  
> branch. When issuing the pull request, the first step will be the  
> comparison of the two branches, where you can check that the diff  
> matches your intentions.

---

## Comment 7

> Username: K-ballo  
> Created_at: 2014-06-06 19:57:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45377900  

> Shouldn't I first switch to the develop branch, with something like:  
  
Yes. Since your pull request should target the `develop` branch, you would have to apply your changes to a branch that shares the current status of `develop`.

---

## Comment 8

> Username: cppljevans  
> Created_at: 2014-06-06 21:21:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45385679  

On 06/06/14 14:57, Agustín Bergé wrote:  
  
> ```  
> Shouldn't I first switch to the develop branch, with something like:  
> ```  
>   
> Yes. Since your pull request should target the |develop| branch, you   
> would have to apply your changes to a branch that shares the current   
> status of |develop|.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/spirit/pull/34#issuecomment-45377900.  
>   
> I tried:  
  
~/prog_dev/boost/boost-modular/spirit/context_debug_fix.fork/spirit $   
git checkout develop  
error: pathspec 'develop' did not match any file(s) known to git.  
~/prog_dev/boost/boost-modular/spirit/context_debug_fix.fork/spirit $   
git branch -a  
- master  
  remotes/origin/HEAD -> origin/master  
  remotes/origin/develop  
  remotes/origin/master  
  remotes/origin/sandbox-branches/bhy/py3k  
  .  
  .  
  .  
  remotes/origin/x3-devel  
  remotes/origin/x3-scalability  
  remotes/upstream/develop  
  remotes/upstream/master  
  remotes/upstream/sandbox-branches/bhy/py3k  
  .  
  .  
  .  
  
So what do I put after `git checkout`? I see two lines in the `git   
branch -a` which  
have "develop" in their name; hence, I'm unsure which to use.  
  
-regards,  
Larry

---

## Comment 9

> Username: cppljevans  
> Created_at: 2014-06-07 13:37:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-45410304  

On 06/06/14 16:21, Larry Evans wrote:  
  
> On 06/06/14 14:57, Agustín Bergé wrote:  
>   
> > Shouldn't I first switch to the develop branch, with something like:  
> >   
> > Yes. Since your pull request should target the |develop| branch, you   
> > would have to apply your changes to a branch that shares the current   
> > status of |develop|.  
> >   
> > —  
> > Reply to this email directly or view it on GitHub   
> > https://github.com/boostorg/spirit/pull/34#issuecomment-45377900.  
> >   
> > I tried:  
>   
> ~/prog_dev/boost/boost-modular/spirit/context_debug_fix.fork/spirit $   
> git checkout develop  
> error: pathspec 'develop' did not match any file(s) known to git.  
> ~/prog_dev/boost/boost-modular/spirit/context_debug_fix.fork/spirit $   
> git branch -a  
> - master  
>   remotes/origin/HEAD -> origin/master  
>   remotes/origin/develop  
>   remotes/origin/master  
>   remotes/origin/sandbox-branches/bhy/py3k  
>   .  
>   .  
>   .  
>   remotes/origin/x3-devel  
>   remotes/origin/x3-scalability  
>   remotes/upstream/develop  
>   remotes/upstream/master  
>   remotes/upstream/sandbox-branches/bhy/py3k  
>   .  
>   .  
>   .  
>   
> So what do I put after `git checkout`? I see two lines in the `git   
> branch -a` which  
> have "develop" in their name; hence, I'm unsure which to use.  
>   
> -regards,  
> Larry  
>   
> Never mind. Reading:  
  
https://help.github.com/articles/syncing-a-fork  
  
gave me the answer after:  
  
evansl@evansl-Inspiron-531:~/prog_dev/boost/boost-modular/spirit/context_debug_fix.fork/spirit$   
git remote -v  
origin https://github.com/cppljevans/spirit.git (fetch)  
origin https://github.com/cppljevans/spirit.git (push)  
upstream https://github.com/boostorg/spirit.git (fetch)  
upstream https://github.com/boostorg/spirit.git (push)  
evansl@evansl-Inspiron-531:~/prog_dev/boost/boost-modular/spirit/context_debug_fix.fork/spirit$

---

## Comment 10

> Username: Kojoley  
> Created_at: 2017-12-08 15:56:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/34#issuecomment-350298323  

Was replaced by #38

---
