# #670 docs: Add partial release notes for Boost 1.80 [Merged]

> Username: striezel  
> Created at: 2022-05-12 18:38:33 UTC  
> Updated at: 2022-05-14 20:45:27 UTC  
> Merged at: 2022-05-13 21:06:14 UTC  
> Closed at: 2022-05-13 21:06:14 UTC  
> Url: https://github.com/boostorg/gil/pull/670  

### Description  
  
Includes changes up to commit 25 (hash [434e78f76](https://github.com/boostorg/gil/commit/434e78f76b5179369738f25a553b74cbaf886218)) from the table shown in <https://github.com/boostorg/gil/issues/667>.  
  
### References  
  
See #667.  
  
### Tasklist  
  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2022-05-12 20:24:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/670#pullrequestreview-971415430  

We usually don't include changes related to CI and internal configurations that end-user is not really interested in.  
We also don't take notes on changes to build configurations, unless a breaking change or new feature end-users of the library can utilise. As I mentioned some time ago, our release notes != our change log.  
Since we have no notes for release managers, there was no way for you to know it.  
  
  
  
In fact, I have made the mistake  myself merging https://github.com/boostorg/gil/pull/665 - this note should be removed.  
  
I highly appreciate your help @striezel

---

## Comment 2

> Username: striezel  
> Created_at: 2022-05-12 23:01:19 UTC  
> Url: https://github.com/boostorg/gil/pull/670#issuecomment-1125492866  

Thanks for the feedback.  
  
I've removed the CI stuff from the release notes.  
  
> In fact, I have made the mistake myself merging #665 - this note should be removed.  
  
Should this be a separate PR or should it be handled here, too? In the latter case, there might probably be a merge conflict when merging from `develop` to `master`.

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-05-13 21:03:57 UTC  
> Url: https://github.com/boostorg/gil/pull/670#issuecomment-1126508660  

@striezel I've just reverted the `RELEASES.md` change in https://github.com/boostorg/gil/commit/616cf28e87618663fe57112f77e2b3f5b6bb23b8

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2022-05-13 21:05:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/670#pullrequestreview-972864246  

Thanks!

---

## Comment 5

> Username: striezel  
> Created_at: 2022-05-13 21:27:11 UTC  
> Url: https://github.com/boostorg/gil/pull/670#issuecomment-1126522001  

Thanks for the merge.  
  
Just a few minutes more and you would have been able to merge the whole release notes at once. :D But now it's another PR: #672. I probably should have communicated that I was working on the rest of those release notes, too.  
  
> I've just reverted the RELEASES.md change in https://github.com/boostorg/gil/commit/616cf28e87618663fe57112f77e2b3f5b6bb23b8  
  
Alright.

---

## Comment 6

> Username: mloskot  
> Created_at: 2022-05-13 21:35:09 UTC  
> Updated_at: 2022-05-14 20:41:13 UTC  
> Url: https://github.com/boostorg/gil/pull/670#issuecomment-1126526477  

No problem, baby steps 😊  
GitHub allows to mark PR as draft, but I'm not sure if you can access that option?  
If not, then simply prefix the subject with `WIP: ` until it's ready to be reviewed and merged.

---

## Comment 7

> Username: striezel  
> Created_at: 2022-05-13 21:40:30 UTC  
> Url: https://github.com/boostorg/gil/pull/670#issuecomment-1126529071  

> GitHub allows to mark PR as draft, but I'm bot sure if you can access that option?  
  
No, I do not have that option here, but I can change the PR's title.

---

## Comment 8

> Username: mloskot  
> Created_at: 2022-05-14 20:45:27 UTC  
> Url: https://github.com/boostorg/gil/pull/670#issuecomment-1126808586  

I've just learned "Convert to draft" is available only to those with write access to a repo: https://github.blog/changelog/2020-04-08-convert-pull-request-to-draft/ - that is about changing stage of existing PR.  
  
But, AFAIU, everyone should be able to create a draft pull request: https://github.blog/2019-02-14-introducing-draft-pull-requests/  
  
Anyway, `WIP:'-ing a PR or creating a draft will work for us.

---
