# #206 Provide to_xxx equivalent for to<xxx_tag> [Closed]

> Username: badair  
> Created at: 2015-11-14 09:10:58 UTC  
> Updated at: 2015-11-17 04:56:06 UTC  
> Closed at: 2015-11-17 04:56:06 UTC  
> Url: https://github.com/boostorg/hana/pull/206  

As you suggested via email, this is a WIP pull request.  
- adding to_map  
- adding to_set  
- adding to_tuple  
  
WIP - need to add documentation in fwd headers... Suggestions?  
I've never used Doxygen before.  
  
WIP - remove doc comments in this commit?  
  
WIP - to_type<xxx> mentioned in issue #164 - is this necessary? Why not   
just use to<xxx>? Do we want to add STL extensions (to_vector, etc)   
instead?

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-11-14 14:46:32 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156706328  

Once merged, this will fix #164.

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-11-14 14:48:30 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156706490  

I would avoid putting the [#164] tag in the commit message, since this causes the issue to be referenced every time you push a WIP commit, which clutters it.

---

## Comment 3

> Username: ldionne  
> Created_at: 2015-11-14 14:49:30 UTC  
> Updated_at: 2015-11-17 00:51:45 UTC  
> Url: https://github.com/boostorg/hana/pull/206#discussion_r44859349  

nitpick: `BOOST_HANA_CONSTANT_CHECK` is too indented

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-11-14 14:57:54 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156709192  

> I would avoid putting the [#164] tag in the commit message, since this causes the issue to be referenced every time you push a WIP commit, which clutters it.  
  
Then, in your final commit, or in the merge commit when I'll merge the pull request, we can say "fixes #164" and that'll close the issue.

---

## Comment 5

> Username: ldionne  
> Created_at: 2015-11-14 15:11:19 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156710084  

> WIP - need to add documentation in fwd headers... Suggestions?  
> I've never used Doxygen before.  
  
It turns out that `to<set_tag>` _is_ documented (so is `to<map_tag>`), so I don't think there's any more documentation to add.  
  
> WIP - remove doc comments in this commit?  
  
I don't understand what you mean. Which doc comments?  
  
> WIP - to_type<xxx> mentioned in issue #164 - is this necessary? Why not   
> just use to<xxx>?  
  
What I was thinking is that `to_type == to<type_tag> == decltype_`.  
I think we should provide it for consistency.  
  
> Do we want to add STL extensions (to_vector, etc) instead?  
  
Good question. I don't know, really. We would surely need a good naming convention. For example, we can't use `to_vector` for `mpl::vector`, because we would probably want to use it for `fusion::vector` too. Hence, we'd need to write `to_mpl_vector`, or even `to_boost_mpl_vector`, but it loses some of its appeal. For now, I'd say let's just stick with the types provided by Hana.

---

## Comment 6

> Username: badair  
> Created_at: 2015-11-14 22:23:26 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156751300  

Thanks for the tag clarifications.   
  
By "doc comments" I was referring to the //! comments that I borrowed from the make_xxx declarations from the fwd headers. I assumed these were intended for documentation markup.  
  
I'll fix the whitespace (oops) and add the to_type declaration - I didn't notice the tag dispatch occurring on type_tag. That makes perfect sense now..  
  
One extension that might be useful, and wouldn't really have external conflicts, would be adding `to_c_str` (or `to_str`) as an equivalent to `hana::to<char const*>`, perhaps?  
  
Shall I close this pull request, and submit a new pull request from a new branch?

---

## Comment 7

> Username: ldionne  
> Created_at: 2015-11-15 00:13:47 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156763476  

> By "doc comments" I was referring to the //! comments that I borrowed from the make_xxx declarations from the fwd headers. I assumed these were intended for documentation markup.  
  
They are intended for documentation markup, but why should we remove them?  
  
> One extension that might be useful, and wouldn't really have external conflicts, would be adding to_c_str (or to_str) as an equivalent to hana::to<char const*>, perhaps?  
  
If we go that way, we could arguably add `to_int` for `to<int>`, `to_float` for `to<float>`, etc... The `to_xxx` shortcuts are really only meant to reduce the typing, but there's not much gain to have `to_c_str` instead of `to<char const*>`. Hence, I'd say that we don't provide it. What do you think? (Also, `to_str` could clash with an eventual `to<string_tag>` that we might want to add at some point, IDK.)  
  
> Shall I close this pull request, and submit a new pull request from a new branch?  
  
No, don't close it. Just push new commits to the same branch and they will automatically added to this pull request. It is also a good practice to rebase your commits on top of the current develop before pushing.

---

## Comment 8

> Username: badair  
> Created_at: 2015-11-16 03:35:28 UTC  
> Updated_at: 2015-11-16 03:36:57 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-156904673  

> They are intended for documentation markup, but why should we remove them?  
  
I just didn't know how they looked in the context of the file, since I don't (yet) know how to view the generated documentation on my machine.  
  
> Hence, I'd say that we don't provide it. What do you think?  
  
I agree.  
  
> It is also a good practice to rebase your commits on top of the current develop before pushing.  
  
I'm new to git (sorry), is this the same thing as "squashing WIP commits" as mentioned in CONTRIBUTING.md? I see there is a `git rebase` command, I'll Google this and figure out how to use it. I have the O'Reilly Git handbook, so that will also be a good reference for me.  
  
>  Just push new commits to the same branch and they will automatically added to this pull request.  
  
I'll let you know when I make the changes discussed and have rebased to the develop branch. Thanks for the tips.

---

## Comment 9

> Username: ldionne  
> Created_at: 2015-11-16 13:36:24 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157028293  

> I just didn't know how they looked in the context of the file, since I don't (yet) know how to view the generated documentation on my machine.  
  
If you have Doxygen installed, building the `doc` target should generate the documentation inside `build/doc/html`. There will be a bunch of files there, but you can open `index.html` in your browser.  
  
> I'm new to git (sorry), is this the same thing as "squashing WIP commits" as mentioned in CONTRIBUTING.md?  
  
No problem. Squashing means "merging together" several commits into a single commit. So for example, you have 4 commits right now; the first one adds some `to_xxx` functions, the second one fixes a small problem in the first commit, the third one improves layout, and the last one also fixes a small problem. Conceptually, we're really looking at a single commit (adding the `to_xxx` functions), except it was implemented in several commits due to mistakes & al. This always happen, it's fine. However, it would be annoying to see all these commits when we look at the history. Hence, we merge commits that logically belong together into a single commit. To do this, you can use `git rebase -i` (`-i` for interactive). Google it up for more info, it's super handy.  
  
Rebasing is the act of taking commits and applying them against another base revision. It's a bit difficult to explain here, but [the manual](https://git-scm.com/docs/git-rebase) has some good examples. The reason why we want to do this is because while you're working on your PR, I'm still updating the `develop` branch. Hence, I want to make sure that your changes still apply to the latest `develop` branch. To do this, you must "keep up" with my `develop` branch, which can be achieved by rebasing on top of `develop`.

---

## Comment 10

> Username: badair  
> Created_at: 2015-11-17 00:09:23 UTC  
> Updated_at: 2015-11-17 00:13:31 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157215566  

> What I was thinking is that to_type == to<type_tag> == decltype_. I think we should provide it for consistency.  
  
Do you want an "alias" of `decltype_`, or do you want a `to_impl` specialization for `type_tag`? I don't see any code where `to<type_tag>` is currently used.  
  
And thanks for the tips! I'll take care of the git stuff and learn Doxygen.

---

## Comment 11

> Username: ldionne  
> Created_at: 2015-11-17 00:15:48 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157217446  

> Do you want an "alias" of decltype_, or do you want a to_impl specialization for type_tag? I don't see any code where to<type_tag> is currently used.  
  
Ohhh, you're right!! I was mistaken with `make<type_tag>`. Meh, just scratch that then, we might add it later but for now it's fine. Hence, I'd say rebase + squash and we're good to merge this!

---

## Comment 12

> Username: badair  
> Created_at: 2015-11-17 00:53:39 UTC  
> Updated_at: 2015-11-17 00:54:40 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157224151  

That was fun. Did I squash and rebase correctly?

---

## Comment 13

> Username: ldionne  
> Created_at: 2015-11-17 01:09:40 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157227256  

> Did I squash and rebase correctly?  
  
You squashed properly, but it seems that you did not rebase. Instead, you seem to have merged some remote branch into your local branch, which you then pushed. After squashing, you should have done  
  
``` shell  
git checkout feature/to_xxx # make sure you're on your feature/to_xxx branch  
git rebase develop # reapply your changes on top of develop  
git push -f # push your just-rebased branch to github, with -f for --force cause you'll be overwriting your previous branch  
```  
  
Anyway, for now don't do anything; I'll cherry-pick the commit from your branch and apply it directly. Thanks a lot!

---

## Comment 14

> Username: badair  
> Created_at: 2015-11-17 01:22:47 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157229508  

Ah, I see what I did wrong now. Thanks.

---

## Comment 15

> Username: ldionne  
> Created_at: 2015-11-17 01:26:30 UTC  
> Url: https://github.com/boostorg/hana/pull/206#issuecomment-157230493  

> Ah, I see what I did wrong now. Thanks.  
  
No problem, really. The git + GitHub workflow is quite productive once you're used to it, but at the beginning it is just puzzling. Thanks to you for the patch.  
  
I'm just waiting for the CI results to be green (which it will), and then I'll push your patch. GitHub will still give you credit for your contribution even though I'm the one to push the patch, so no worries.

---
