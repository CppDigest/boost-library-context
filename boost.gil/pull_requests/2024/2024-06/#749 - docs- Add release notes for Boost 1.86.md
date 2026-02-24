# #749 docs: Add release notes for Boost 1.86 [Merged]

> Username: striezel  
> Created at: 2024-06-30 12:38:36 UTC  
> Updated at: 2025-08-03 18:08:49 UTC  
> Merged at: 2024-07-04 14:46:21 UTC  
> Closed at: 2024-07-04 14:46:21 UTC  
> Url: https://github.com/boostorg/gil/pull/749  

### Description  
  
Includes release notes for the changes seen in the table in #748, i.e. the changes for a potential Boost 1.86 release of GIL.  
  
### References  
  
See #748.  
  
### Tasklist  
  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2024-07-03 22:49:49 UTC  
> Url: https://github.com/boostorg/gil/pull/749#issuecomment-2207435037  

The one job that failed here was "posix (clang, 14,17, macos-11)". That is a bit strange, because a documentation update should not cause CI failure. Here's what the summary says:  
> This request was automatically failed because there were no enabled runners online to process the request for more than 1 days.  
  
That should not happen - unless no such runners are available at all. Turns out they are no longer available: https://github.blog/changelog/2024-05-20-actions-upcoming-changes-to-github-hosted-macos-runners/  
  
> In January 2024, GitHub announced the [deprecation of macOS 11](https://github.blog/changelog/2024-01-30-github-actions-macos-14-sonoma-is-now-available/) and the removal of the runner image by June 2024. The macOS 11 runner image will be removed on 6/28/2024. We recommend updating workflows to use macos-14, macos-13, macos-12, or macos-latest.  
  
We are now past the date when the macOS 11 runner image was removed, and that is why that CI job failed.

---

## Comment 2

> Username: sdebionne  
> Created_at: 2024-07-04 07:20:43 UTC  
> Url: https://github.com/boostorg/gil/pull/749#issuecomment-2208287112  

Thanks for looking into this and the doc improvements. I don't know much about the CI setup on this project but I think that @mloskot who also contributed to boostorg/boost-ci used it to set it up. So maybe it is just a question of merging with the latest boost-ci (the GH actions part)?

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2024-07-04 14:46:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/749#pullrequestreview-2159094950  

Thank you @striezel for this update, your efforts are priceless!  
  
@sdebionne Yes, I did some contributions to boost-ci project, but AFAIR I stopped using it for GIL a while ago, some time around dropping use of Travis CI. Then, I have been following Boost.MP11's https://github.com/boostorg/mp11/blob/develop/.github/workflows/ci.yml to catch up with changes and deprecations in GitHub Actions.  
  
Long story short, I think we can ignore this change and approve this PR.  
  
@striezel You are right, I think we should not run the whole CI for changes like this. It looks like we could improve it with exclusions based on paths/files filter, https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#example-excluding-paths

---
