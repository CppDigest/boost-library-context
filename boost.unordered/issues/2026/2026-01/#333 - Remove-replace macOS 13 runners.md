# #333 - Remove/replace macOS 13 runners [Closed]

> Username: k3DW  
> Created at: 2026-01-01 00:01:00 UTC  
> Updated at: 2026-01-01 19:59:51 UTC  
> Closed at: 2026-01-01 19:59:51 UTC  
> Url: https://github.com/boostorg/unordered/issues/333  

In recent CI runs, the macOS 13 jobs are failing  
  
> The macOS-13 based runner images are now retired. For more details, see `https://github.com/actions/runner-images/issues/13046`.  
> The macOS-13 based runner images are being deprecated, consider switching to macOS-15 (macos-15-intel) or macOS 15 arm64 (macos-latest) instead. For more details see `https://github.com/actions/runner-images/issues/13046`  
  
It looks like we do more testing on macOS 13 than we do on 14 or 15, so I don't think we can just remove the two `macos-13` lines in the CI script
