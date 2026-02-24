# #183 chore: bump macos runner version [Closed]

> Username: renbaoshuo  
> Created at: 2022-07-25 10:01:37 UTC  
> Updated at: 2024-07-06 20:53:42 UTC  
> Closed at: 2024-07-06 20:53:42 UTC  
> Url: https://github.com/boostorg/interprocess/pull/183  

GitHub Action is sunsetting the macOS 10.15 Actions runner. It will stop working intermittently until being completely removed by 2022-8-30: https://github.blog/changelog/2022-07-20-github-actions-the-macos-10-15-actions-runner-image-is-being-deprecated-and-will-be-removed-by-8-30-22

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2024-07-06 20:53:42 UTC  
> Url: https://github.com/boostorg/interprocess/pull/183#issuecomment-2211964952  

Reviewing old bugs. Thanks for the report.  
  
macos-11 was also removed and the latest ci.yml is using macos-12 as the minimum. macos-12 is being removed at the end of this year.

---
