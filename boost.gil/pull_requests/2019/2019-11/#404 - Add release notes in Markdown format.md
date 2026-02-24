# #404 Add release notes in Markdown format [Merged]

> Username: mloskot  
> Created at: 2019-11-11 00:01:51 UTC  
> Updated at: 2019-11-11 17:04:14 UTC  
> Merged at: 2019-11-11 17:02:33 UTC  
> Closed at: 2019-11-11 17:02:33 UTC  
> Url: https://github.com/boostorg/gil/pull/404  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
This proposes to use Git to maintain notes about all notable changes to GIL in `RELEASES.md` file based on https://keepachangelog.com format.  
  
Collect all past release notes.  
Collect release notes for the pending Boost 1.72.0, to be released in December according to [Boost release schedule](https://www.boost.org/development/index.html).  
  
> **IMPORTANT:** Before Boost release is issued, our release notes from `RELEASES.md` should be copied and added to relevant file in https://github.com/boostorg/website/tree/master/feed/history  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
This implements proposal discussed and agreed in the thread https://lists.boost.org/boost-gil/2019/11/0336.php  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Review and approve  
  
------  
  
It is possible to integrate the release notes into Sphinx-generated documentation. It will be implemented as soon as this proposal is merged.

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-11-11 01:11:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/404#pullrequestreview-314658550  

Wow, you put a lot of effort into this ! It's a lot of detail.  
One minor point: you mark the recent changes as "unreleased", yet you are working on this in preparation for 1.72. Do you expect a separate PR (or commit) to turn "unreleased" into "1.72" ? Can't these two change sets be combined ?

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-11-11 09:26:23 UTC  
> Url: https://github.com/boostorg/gil/pull/404#issuecomment-552361638  

@stefanseefeld This is a good point. I have updated this PR adding the missing version and date (based on what's in  [Boost calendar](https://www.boost.org/development/index.html)).

---

## Review 3 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-11-11 13:10:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/404#pullrequestreview-314886358  

Many thanks for working on this.

---
