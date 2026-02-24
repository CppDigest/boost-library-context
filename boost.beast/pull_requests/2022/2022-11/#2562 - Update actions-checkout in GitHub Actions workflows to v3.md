# #2562 Update actions/checkout in GitHub Actions workflows to v3 [Merged]

> Username: striezel  
> Created at: 2022-11-14 10:51:45 UTC  
> Updated at: 2022-12-17 21:21:45 UTC  
> Merged at: 2022-12-17 10:08:14 UTC  
> Closed at: 2022-12-17 10:08:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2562  

Updates the `actions/checkout` action used in the GitHub Actions workflow to its newest major version.  
  
Changes in [actions/checkout](https://github.com/actions/checkout):  
  
> ## v3.1.0  
> - Use @actions/core `saveState` and `getState`  
> - Add `github-server-url` input  
>  
> ## v3.0.2  
> - Add input `set-safe-directory`  
>  
> ## v3.0.1  
> - Fixed an issue where checkout failed to run in container jobs due to the new git setting `safe.directory`  
> - Bumped various npm package versions  
>  
> ## v3.0.0  
>  
> - Update to node 16  
  
As far as I can tell this should all be backwards compatible, so I do not expect any breakage.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-14 11:19:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2562#issuecomment-1313524712  

![pullrequest](https://2562.beast.tracing.cppalliance.org/pullrequest-6faa8faf.png)  
Timeline tracing charts: [https://2562.beast.tracing.cppalliance.org/index.html](https://2562.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2022-12-07 05:43:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2562#issuecomment-1340401936  

@sdarwin what says thou?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2022-12-07 13:18:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2562#issuecomment-1340957482  

@klemens-morgenstern yes this change should be made on any github actions configs.

---

## Comment 4

> Username: striezel  
> Created_at: 2022-12-08 15:11:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2562#issuecomment-1342880423  

> this change should be made on any github actions configs.  
  
Indeed.  
  
Still using v2 of `actions/checkout` will generate some warning like in this run: https://github.com/boostorg/beast/actions/runs/3485159420  
  
> Node.js 12 actions are deprecated. For more information see: https://github.blog/changelog/2022-09-22-github-actions-all-actions-will-begin-running-on-node16-instead-of-node12/. Please update the following actions to use Node.js 16: actions/checkout@v2  
  
The PR will get rid of those warnings for `actions/checkout`, because v3 usese Node.js 16.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-12-14 16:49:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2562#issuecomment-1351759035  

What's the status on this?

---
