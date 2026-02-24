# #186 Specify the mainline 'branch' for each submodule [Merged]

> Username: rcdailey  
> Created at: 2018-05-21 18:03:18 UTC  
> Updated at: 2018-11-16 19:52:33 UTC  
> Merged at: 2018-11-09 03:26:24 UTC  
> Closed at: 2018-11-09 03:26:24 UTC  
> Url: https://github.com/boostorg/boost/pull/186  

Using the `submodule.<name>.branch` config option (in the `.gitmodules` file),  
specify the mainline branch for usage by `git submodule update --remote`. The  
mainline branch in most submodules is `develop`, but in a few it's still  
`master`. As things are (config unspecified), git uses `master` by default but  
this causes submodule updates to fail with the `--remote` option.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-05-21 18:40:49 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390744602  

I don't think this is right. `master` is what gets merged into the super project; and what goes into the releases. `develop` is where development gets done; `master` is supposed to be "ready to go" at all times.

---

## Comment 2

> Username: rcdailey  
> Created_at: 2018-05-21 19:34:13 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390758517  

What specifically isn't right? I'm not sure your points are relevant to the issue solved here. Please provide more detail.  
  
I'm not sure how the boost devs do things, but typically (in gitflow's case) you update submodules to "latest" enmasse on develop. So naturally the `.gitmodules` file will be structured so that when you do `submodule update --remote`, you are grabbing the latest of unstable branches. Note that not all submodules have `master`, and not all have `develop`. From what I've seen it's very inconsistent.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-05-21 19:37:19 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390759293  

It's my understanding that all of the boost repositories must follow the master/develop concept that @mclow mentioned in order for us to be able to build and bundle and properly version/tag a release.  Could you identify the repositories where the default branch is still set to master instead of develop?  Thanks.

---

## Comment 4

> Username: rcdailey  
> Created_at: 2018-05-21 19:49:54 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390762360  

@jeking3 If you look at the diff I've provided here, any with the line `branch = master` are set to that explicitly because they do not have a `master` branch. I did not, however, do any analysis to determine how the remote `HEAD` is configured. For example, a repository with the following: `HEAD`, `develop`, and `master` may have a `HEAD -> master` or `HEAD -> develop`. You'd have to symbolic-ref `origin/HEAD` to find that out.   
  
Here was the general flow I performed for my tests:  
  
```  
git clone git@github.com:boostorg/boost.git  
cd boost  
git submodule update --init --recursive --remote #<-- this fails because most submodules do not have `origin/master`  
```  
  
With my patch applied, the last line in the above script executes just fine. This is because the `.gitmodules` file now explicitly tells the submodule command which remote branches to pull.

---

## Comment 5

> Username: rcdailey  
> Created_at: 2018-05-21 19:51:44 UTC  
> Updated_at: 2018-05-21 19:52:03 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390762800  

Also please note that even if all submodules were consistent in that they had both `master` and `develop` branches, changes to `.gitmodules` are still required but you'd use `branch = .` instead. The `.` here means "use the same name upstream branch for each submodule as the one I have checked out". So for example, if in the boost clone you have `develop` checked out, then `branch = .` effectively becomes `branch = develop`, and likewise if you are on `master`, it becomes `branch = master`.

---

## Comment 6

> Username: mclow  
> Created_at: 2018-05-21 21:08:41 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390783453  

If any submodules don't have `master`, then we've got a problem - since that's where we get the bits that go into the releases.

---

## Comment 7

> Username: mjcaisse  
> Created_at: 2018-05-22 03:31:02 UTC  
> Updated_at: 2018-05-22 03:35:47 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-390851875  

@rcdailey I did a spot check of the first two items in the diff: format and smart_ptr. Each of them have a master branch. Maybe I'm not understanding.

---

## Comment 8

> Username: rcdailey  
> Created_at: 2018-05-22 15:59:38 UTC  
> Updated_at: 2018-05-22 16:01:08 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-391045731  

My apologies to everyone. It appears I'm chasing the wrong issue here. Let me explain. Initially I did a clone as follows:  
  
    $ git clone --recurse-submodules --depth=1 --shallow-submodules <boost url>  
  
This resulted in some failures related to submodule(s) pointing to orphaned/dangling commits. For example, `libs/accumulators` was pointing to SHA1 `bfcbfe3c58064cd1ffabbce49a95c6c20351c96e`, which resulted in submodule update failing:  
  
```  
$ git sm update --init libs/accumulators  
error: Server does not allow request for unadvertised object bfcbfe3c58064cd1ffabbce49a95c6c20351c96e  
Fetched in submodule path 'accumulators', but it did not contain bfcbfe3c58064cd1ffabbce49a95c6c20351c96e. Direct fetching of that commit failed.  
```  
  
I even did a `fetch --unshallow` inside `libs/accumulators` and the SHA1 is still not available. So I think some of the submodules need updating/fixing. So it doesn't seem like there's an issue with master and develop branches being unavailable in the submodules (After double/triple checking, I wasn't able to find any missing; the only reason I saw `master` missing before is again because of the sparse clones of the submodules which do not pull all remote refs by design, so it confused me).  
  
So at this point, I think there's 2 items that need action:  
  
1. The submodules should be corrected somehow  
2. The `.gitmodules` file should be updated (this is an action item for me) to specify `branch = .` per each entry. As I explained before, this allows submodule update's `--remote` option to pull latest on either `master` or `develop` depending on the checked out branch in the parent repository. I do not believe this hurts anything, basically it's enabling better `--remote` semantics, so if you never used that switch before it won't matter too much.  
  
Thoughts on these items? Again, I apologize for the confusion!

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-08-01 16:39:15 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-409639412  

The conflict here needs to be resolved.

---

## Comment 10

> Username: rcdailey  
> Created_at: 2018-08-01 18:54:20 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-409683817  

Rebased to latest on `develop` and clarified what is actually being fixed in the commit message. Note that the only change at this point is to introduce `branch = .` in the `.gitmodules` file, to facilitate `git submodule update --remote` usage on both `master` and `develop`.

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-08-12 22:01:59 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-412374893  

Needs rebasing again.

---

## Comment 12

> Username: rcdailey  
> Created_at: 2018-08-12 23:40:05 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-412379618  

Rebased & corrected the commit. I must have messed up the last rebase because it had tons of changes in there I didn't intend to include. Let me know if it looks acceptable now.

---

## Comment 13

> Username: jeking3  
> Created_at: 2018-08-13 23:53:24 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-412704889  

@mjcaisse please kick the build again (or the individual jobs that failed) looks like environmental issues.

---

## Comment 14

> Username: jeking3  
> Created_at: 2018-10-27 14:55:22 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-433627497  

So to summarize this change, if someone is in the superproject and has the `develop` branch checked out, then `git submodule update --remote` will fetch and check out `develop` in every subproject.  Similarly if someone is in the superproject and has the `master` branch checked out, then `git submodule update --remote` will fetch and check out `master` in every subproject.  Finally, if someone uses `git submodule update` then it will continue to snap each subproject to the current commit ID, just like it does today.  Correct?  If so, this seems fine.  It just needs to be rebased again.

---

## Review 15 [Approved]

> Username: jeking3  
> Created_at: 2018-10-27 14:57:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boost/pull/186#pullrequestreview-169058385  

This PR makes it easier to snap all boost repositories to `develop` or `master` using a single command: `git submodule update --remote`.  It does not affect the `git submodule update` behavior we're all used to.

---

## Comment 16

> Username: rcdailey  
> Created_at: 2018-10-27 16:35:54 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-433635418  

Your explanation is correct. I will rebase this for you on Monday when I  
get back to the office.  
  
On Sat, Oct 27, 2018, 9:57 AM James E. King III <notifications@github.com>  
wrote:  
  
> *@jeking3* approved this pull request.  
>  
> This PR makes it easier to snap all boost repositories to develop or  
> master using a single command: git submodule update --remote. It does not  
> affect the git submodule update behavior we're all used to.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/boost/pull/186#pullrequestreview-169058385>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABr6dvEoOZUkd9_LQvVk1qPGXy-9EC4cks5upHRwgaJpZM4UHYrm>  
> .  
>

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-11-04 12:50:46 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-435666976  

Needs rebasing.

---

## Comment 18

> Username: rcdailey  
> Created_at: 2018-11-04 13:19:45 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-435668988  

Thanks for the reminder @jeking3, I completely forgot about this. The conflicts were pretty frustrating this time; it looks like someone drastically reordered the `.gitmodules` file since my last rebase. On that note, I've had to rebase a lot over the past few months and the change is pretty simple. Hopefully you guys can get it merged in so I don't have to keep rebasing it so much.

---

## Comment 19

> Username: rcdailey  
> Created_at: 2018-11-06 15:35:15 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-436295700  

@jeking3 Reminder to merge this branch before it diverges again.

---

## Comment 20

> Username: rcdailey  
> Created_at: 2018-11-08 15:08:15 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-437028695  

Going to go ahead and close this PR since weeks go by and it gets no attention. I no longer have the time to ping back & forth on this for on-demand rebasing when no one takes action after I do so. Not angry or anything, and not trying to be rude, but I've just run out of time to maintain this PR. If someone else wants to come in and take ownership of this, I would definitely appreciate it. I will close this for now.

---

## Comment 21

> Username: jeking3  
> Created_at: 2018-11-09 02:40:45 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-437230667  

@pdimov @mjcaisse this is unfortunate.

---

## Comment 22

> Username: pdimov  
> Created_at: 2018-11-09 02:45:43 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-437231434  

I reordered the .gitmodules on develop to match master, as the two had an entirely different order for some reason.

---

## Comment 23

> Username: pdimov  
> Created_at: 2018-11-09 03:28:07 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-437238052  

`master` is frozen now, so we'll need to remember to apply this there after the 1.69 release goes out.

---

## Comment 24

> Username: jeking3  
> Created_at: 2018-11-16 19:41:06 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-439504659  

This change does not seem to actually place the subrepositories on "develop", if boostorg/boost has "develop" checked out, and then run "git submodule update --remote".  It still seems to check out the git hash in each subrepository.  I was expecting all the subrepositories to be on "develop"... perhaps they are, but using a detached HEAD for each.  Just not what I expected to happen, that's all.

---

## Comment 25

> Username: rcdailey  
> Created_at: 2018-11-16 19:51:23 UTC  
> Updated_at: 2018-11-16 19:52:33 UTC  
> Url: https://github.com/boostorg/boost/pull/186#issuecomment-439507495  

Submodule commands *never* check out a symbolic ref. They always check out a SHA1, which inherently means they will always have a detached `HEAD`. However, this is a negligible detail if you adjust your workflow. I personally do the following:  
  
* Never use `submodule` commands when I'm doing development work between submodules. I always `cd` between submodules and run git commands on them like independent repositories as I'm working.  
* Use `submodule` commands only when I don't care about changing something. This is especially the case when I just need the working copy to be "correct" in repos where I am not familiar with the code.  
  
If you happen to be making changes inside the submodule while you're detached, simply do a `git checkout develop` to move those changes back onto the mainline branch, or `git checkout -b`, or if you've already committed, it's a bit more involved but doable.  
  
I'm sure you guys already knew all of this, but just sharing some insight.

---
