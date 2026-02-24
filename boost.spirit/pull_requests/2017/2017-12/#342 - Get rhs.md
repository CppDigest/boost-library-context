# #342 Get rhs [Closed]

> Username: cppljevans  
> Created at: 2017-12-23 12:27:08 UTC  
> Updated at: 2017-12-28 23:12:37 UTC  
> Closed at: 2017-12-28 23:12:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/342  

# Purpose  
  
  This  
  [file](https://github.com/cppljevans/spirit-experiments/blob/get_rhs/README-get_rhs-branch.md)  
  explains the purpose of this pull request.  
    
# Why so many "develop" files?  
  
  No doubt some (maybe all) reviewers will notice many  
  files which seem to have no impact on user's code.  In  
  particular, everything in folders:  
    
  * [skip_ctx](https://github.com/cppljevans/spirit-experiments/tree/get_rhs/workbench/x3/HanWang_make_context)  
  * [csv](https://github.com/cppljevans/spirit-experiments/tree/get_rhs/workbench/x3/csv_parser)  
  * [rec_ex](https://github.com/cppljevans/spirit-experiments/tree/get_rhs/workbench/x3/rec_ex_orig)    
  * [rule_defns](https://github.com/cppljevans/spirit-experiments/tree/get_rhs/workbench/x3/rule_defns)  
  
  The reason these folders are there is because the original  
  purpose of this fork was to understand how critical parts  
  of spirit worked.  This was the particular reason for the  
  `rule_defns` folder.  In addition, reading and/or solving  
  user's problems posted to  
  [spirit-general](https://lists.sourceforge.net/lists/listinfo/spirit-general)  
  provided further opportunities to learn how `spirit`  
  worked as well as help user's; hence, the other folders  
  emerged.  
    
# Suitability for pull request?  
  
  With so many "develop" files, is this branch suitable for  
  a pull request? Well, maybe not, but this post,  
  [need_pull_request](https://sourceforge.net/p/spirit/mailman/message/36151103/),  
  and in particular this passage:  
    
  > If you want to get things merged, the best thing would  
  > be if you had agreed on the design changes along the  
  > way, and pull requests are excellent devices to get  
  > this discussion transparently on the board.  
      
  indicated that a pull request was needed.  Hence, this  
  pull request is really a belated request to "agree on  
  design changes" in the `get_rhs` fork.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2017-12-23 13:05:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/342#pullrequestreview-85422654  

Oh my... Can you please rebase and remove those merge commits?  
Also:  
  - The code formatting is heavily differs from what Spirit has.  
  - Does `workbench/*` really maters for the PR?  
  - It is strange to see files with word `example` in their names under `test` directory.

📁 include/boost/spirit/home/x3/nonterminal/rule.hpp

```diff
 111 |+          */
 112 |+         {
 113 |+             assert(false);
```

> Username: Kojoley  
> Created_at: 2017-12-23 12:41:57 UTC  
> Updated_at: 2017-12-23 13:06:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158581813  

This is not a static assert (as comment above says) + `assert` is a no-op in release version.

> Username: cppljevans  
> Created_at: 2017-12-27 19:20:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158857619  

The comment indicates the reason it's needed even though  
it's a no-op.  You'll see further down where it's used  
there's a little further explanation in comments.


📁 include/boost/spirit/home/x3/nonterminal/detail/rule.hpp

```diff
  53 |       , Iterator& first, Iterator const& last
  54 |-       , Context const& context, ActualAttribute& attr);
  54 |+       , Context const& context, ActualAttribute& attr);
```

> Username: Kojoley  
> Created_at: 2017-12-23 12:45:28 UTC  
> Updated_at: 2017-12-23 13:05:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158581862  

Please clean up the PR from only white space changes. Less code you are touching - less merge conflicts and easier to rebase.

> Username: cppljevans  
> Created_at: 2017-12-27 19:17:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158857340  

Will do.  My emacs editor, for some strange reason, puts those in and it's, obviously,  
not immediately obvious  ;(  
  
W.r.t. the rebase, I've been googling the question and read several web pages  
and blogs but still don't have enough confidence to try it.  Currently I've followed  
the `Merge Workflow` described here:  
  
  https://randyfay.com/taxonomy/term/26  
  
More specifically I've done:  
  
> git pull upstream develop  
  
Then manually resolved the CONFLICT's, then:  
  
> run the revised test/x3/Jamfile  
  
and make changes to get all tests to pass.  However, I still do not  
understand, despite reading *many* webpages of `git rebase` docs,  
 how *exactly* I should do a rebase to remove all these merge commits.  
I'll continue to read such pages until I do understand or just  
resort to trial and error trying, but it would speed things up  
if you could tell me exactly how to do this rebase.  
  
w.r.t. the strange files, they're not really needed, but they  
were helpful to illustrate to the newsgroup readers solutions  
to their problems.  I'll remove them (or maybe move them  
to another repo).

> Username: Kojoley  
> Created_at: 2017-12-27 20:56:18 UTC  
> Updated_at: 2017-12-27 20:56:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158866905  

> More specifically I've done:  
> > git pull upstream develop  
  
Be aware that if you did not tweak git this command will produce a merge commit on your branch.  
  
> how exactly I should do a rebase to remove all these merge commits.  
  
Enter `git rebase upstream/develop` and solve merge conflicts. That's all. (I assume `upstream` is setup for boostorg repository). If you solve merge conflicts right they will disappear, otherwise they will turn into a regular commits (but you still can polish out them by squashing/removing/editing).

> Username: cppljevans  
> Created_at: 2017-12-28 11:56:28 UTC  
> Updated_at: 2017-12-28 11:56:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158933880  

Thanks Nikita.  I've replied to you via my email to:  
  
  > notifications@github.com  
    
In that email were details of how I tried your suggestion  
without success. However, that reply is not turning up here;  
so, this is to check that you're getting them.  If not, I'll  
repeat those details here in another reply.

> Username: Kojoley  
> Created_at: 2017-12-28 21:12:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158999611  

I did not receive anything.  
  
>  I tried your suggestion without success.  
  
You failed to solve merge conflicts? (I don't think you've failed to type a single command). Well, since you've wrongly solved them currently too, do not bother then, just cherry-pick or re-do your patches on the latest develop (and then forcepush to this branch).

> Username: cppljevans  
> Created_at: 2017-12-28 22:48:43 UTC  
> Updated_at: 2017-12-28 22:51:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r159008175  

Solving the merge conflicts was not the problem.  The problem was repeatedly solving  
merge conflicts.  After the 1st `git rebase upstream/develop`, the git output listed the conflicts,  
which were resolved.  Then, following the git output instructions, did `git rebase --continue`  
which resulted in another set of merge conflicts.  After doing this 3-4 times, I googled and  
found the -Xours option.  So, I used `git rebase --abort ; git rebase -Xours upstream/develop` followed by only 1 set of merge conflict resolutions.  
This seemed to work, but then, when I tried `git push`, the error msg said:  
  
> hint: Updates were rejected because the tip of your current branch is behind  
> hint: its remote counterpart. Integrate the remote changes (e.g.  
> hint: 'git pull ...') before pushing again.  
  
Further googling lead to more suggestions for solving the problem but  
they didn't work either.  
  
So, I guess I'll do as you suggest with the latest develop.

> Username: djowel  
> Created_at: 2017-12-28 22:54:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r159008658  

You'll have a heavy burden keeping up such a large PR with the moving code base. Please heed my request to keep the PRs minimal and incremental. Please focus on one small item at a time. Superfluous formatting also do not help as they tend to break the diffs.  
  
That said, I am genuinely interested with your work.

> Username: Kojoley  
> Created_at: 2017-12-28 22:56:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r159008741  

> when I tried `git push`, the error msg  
  
This is a git protection of unintended rewriting of history. But you are rewriting it with a clear intention, that's why you need to force-push (`--force` flag).  
If you need to save the previous branch history just make a copy of the branch.

> Username: Kojoley  
> Created_at: 2017-12-28 22:58:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r159008938  

>  After doing this 3-4 times, I googled and found the -Xours option. So, I used `git rebase --abort ; git rebase -Xours upstream/develop` followed by only 1 set of merge conflict resolutions.  
  
This is really the wrong way to do it. I swear you will solve merge conflicts in a bad way and will end with something you really do not want to.

> Username: cppljevans  
> Created_at: 2017-12-28 23:11:22 UTC  
> Updated_at: 2017-12-28 23:12:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r159009833  

OK, Nikita.  I'll create 3 new branches to solve  
3 separate problems, following Joel's suggestion, and cherry-pick,  
as you suggest Nikita, from this branch to the 3 new branches.  
  
Consequently, there'd be no need for this PR and I'll try to close it.


📁 test/qi/Jamfile

```diff
 124 | run utree3.cpp ;
 125 | run utree4.cpp ;
 126 |+ <<<<<<< HEAD
```

> Username: Kojoley  
> Created_at: 2017-12-23 12:46:27 UTC  
> Updated_at: 2017-12-23 13:05:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158581879  

You have did not solve a merge conflict here.

> Username: cppljevans  
> Created_at: 2017-12-27 19:38:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158859490  

Will do.


📁 workbench/x3/rule_defns/gen_benchmark_gram.cpp

```diff
 221 |+   gf.close();
 222 |+   return 0;
 223 |+ }
```

> Username: Kojoley  
> Created_at: 2017-12-23 13:00:30 UTC  
> Updated_at: 2017-12-23 13:05:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158582089  

Code format of this makes it nearly impossible to understand :(

> Username: cppljevans  
> Created_at: 2017-12-27 19:44:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158860011  

I assume you're referring to the code indentation.  I added  
comments at the top of the code to explain that the odd  
indentation is to help debugging.  Basically, the code  
indentation reflects the output indentation to make  
debugging the code much easier.  This become  
really obvious once you try figuring out where  
to put the ++gfi's and --gfi's while debugging the code.

> Username: Kojoley  
> Created_at: 2017-12-27 21:08:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158868056  

I am mostly about first hundred lines (this actually problem of all the PR).  
  
_Suggestion: you really should use RAII instead of `++gfi's and --gfi's`._

> Username: cppljevans  
> Created_at: 2017-12-27 21:40:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158871291  

The rationale for that is that the function definitions form a dictionary, and the convention for dictionaries is the what's being  
defined is aligned on the far left.  Thus, the function name is on the  
far left.  Of course this is unconventional, but conventional is not always logical.  However, I will change the code conform the code to  
convention by putting both the return type, the function name, and the  
argument list on 1 line, if they fit.  If they don't fit, I'd break  
*after* the comma for the argument closest to column 70 or thereabouts.

> Username: cppljevans  
> Created_at: 2017-12-27 21:44:46 UTC  
> Updated_at: 2017-12-27 22:00:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158871669  

Sorry, I don't understand how RAII applies here.  The ++gfi and --gfi change the current indentation of the current output file.  RAII is  
about initializing a resource at the time of acquisition.  Well gfi  
is acquired at the beginning of the block and destructed at the end of  
that block but the indentation and outputs must happen in between; so,  
how can RAII be used to do this?  
  
Ah, now I think I understand.  Instead of using paired ++gfi; --gfi;, your  
suggesting using *another* class, gfiRAII, taking the gfi as a parameter and storing  
that as a ref parameter, and, in the CTOR, do the ++gfi and in the CTOR do  
the --gfi.  Is that what you mean?


📁 include/boost/spirit/home/x3/support/context.hpp

```diff
  22 |+     https://github.com/boostorg/spirit/pull/237
  23 |+    */
  24 |+ #endif
```

> Username: Kojoley  
> Created_at: 2017-12-23 13:01:48 UTC  
> Updated_at: 2017-12-23 13:05:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158582111  

Why it is here if it is disabled?

> Username: cppljevans  
> Created_at: 2017-12-27 19:20:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158857699  

I simply overlooked it.  Thanks for finding it and I'll remove.

---

📁 include/boost/spirit/home/x3/support/context.hpp

```diff
 103 |         inline context<ID, T, Next>
 104 |         make_unique_context(T& val, Next const& next, unused_type)
 105 |+         //ID not found in next; so append val to existing context.
```

> Username: Kojoley  
> Created_at: 2017-12-23 13:03:03 UTC  
> Updated_at: 2017-12-23 13:06:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158582126  

If it is a comment to function overload, please place them before template keyword.

> Username: cppljevans  
> Created_at: 2017-12-27 19:37:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#discussion_r158859454  

To understand how make_unique_context works, the code reader  
1st has to notice the difference in the last argument for  
the 2 make_unique_context overloads.  So the comment makes  
more sense *after* seeing the difference in the last  
argument; hence, the comment comes after the function  
signature instead of before.


---

## Comment 2

> Username: djowel  
> Created_at: 2017-12-23 19:41:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/342#issuecomment-353744393  

The PR seems to be "fixing" many things at once. I really doubt such an extensive PR can get through. For one, I won't have the time to review it all. I'd probably consider it if it were incremental, with a clear goal per PR.

---
