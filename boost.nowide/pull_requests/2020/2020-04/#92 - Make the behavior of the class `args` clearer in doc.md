# #92 Make the behavior of the class `args` clearer in doc [Closed]

> Username: tats-u  
> Created at: 2020-04-27 15:55:10 UTC  
> Updated at: 2020-04-29 06:39:43 UTC  
> Closed at: 2020-04-28 14:46:39 UTC  
> Url: https://github.com/boostorg/nowide/pull/92  

The  documentation did not seem to be easy for me to understand what `boost::nowide::args` does with `argv`, one of arguments of `main`.  
I reworded sentences explaining the behavior of `boost::no::wide::args` to make the following clearer:  
- The instance of this class *hides* the original but legacy-encoded (`argc`,) `argv` (, and `env`) with UTF-8 encoded ones *only while it is alive.* (the most important)  
- This class fetches the complete (Unicode) (`argc`,) `argv` (, and `env`) from the Windows API regardleass of `main`, and converts them to UTF-8 *by itself.*  
  
I do not have any confidence in selecting words.  You can fix my words in the contents of this PR as you like without asking me.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-04-28 10:26:09 UTC  
> Url: https://github.com/boostorg/nowide/pull/92#issuecomment-620519404  

> I do not have any confidence in selecting words. You can fix my words in the contents of this PR as you like without asking me.  
  
It seems I can't append to this PR directly. Opened another one based on this: #93  
  
Main changes are the use of "lifetime" which is defined in the C++ standard and removal of  "encoded by an non-Unicode character set" which isn't exactly right. I also added a basic example.  
Would be great if you can proof-read that. Thanks again! :+1:

---

## Comment 2

> Username: tats-u  
> Created_at: 2020-04-28 14:44:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/92#issuecomment-620652337  

Thank you for making use of my change.  
Shall I close this PR and entrust it to yours?

---

## Comment 3

> Username: Flamefire  
> Created_at: 2020-04-28 14:46:39 UTC  
> Url: https://github.com/boostorg/nowide/pull/92#issuecomment-620653300  

Can do. Did you check the updated wording in the other PR?

---

## Comment 4

> Username: tats-u  
> Created_at: 2020-04-29 01:52:32 UTC  
> Updated_at: 2020-04-29 05:14:06 UTC  
> Url: https://github.com/boostorg/nowide/pull/92#issuecomment-620945955  

Yes, it seems great.  
However, I'd like to ask you why you did not append a modification commit on mine but recreate a new single commit.  
It makes it unclear what you and I modify respectively.  
Maybe is it a single commit per PR preferred in this repository?  
  
```bash  
git brach -a backup_  
git remote add tmp_ https://github.com/tats-u/nowide.git  
git fetch tmp_  
git checkout -b tmp2_ tmp_/fix-doc-class-args  
git revert  
git checkout args_docu  
git rebase tmp2_  
git rebase -i develop # Unify the reverting and my commits (use squash)  
git commit --amend # Remove revert commit message (or rebase -i develop)  
git push --force-with-lease mine args_docu  
  
git branch -D backup_  
git branch -D tmp2_  
git remote remove tmp_  
```

---

## Comment 5

> Username: Flamefire  
> Created_at: 2020-04-29 06:33:21 UTC  
> Url: https://github.com/boostorg/nowide/pull/92#issuecomment-621016059  

Honestly just because that seemed to complicated ;)  
  
However I found something easier: ` git fetch origin pull/92/head:pr_args` fetches the PR directly into a branch. I rebased #93 onto your commit, so should be fine now, local log looks good

---

## Comment 6

> Username: tats-u  
> Created_at: 2020-04-29 06:39:43 UTC  
> Url: https://github.com/boostorg/nowide/pull/92#issuecomment-621018026  

Thank you for rebasing.  
I should have reflected the points pointed out by the CI.

---
