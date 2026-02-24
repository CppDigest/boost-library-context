# #301 Remove Ubuntu 20.04 build from CI [Merged]

> Username: striezel  
> Created at: 2025-05-01 21:36:47 UTC  
> Updated at: 2025-05-03 21:58:05 UTC  
> Merged at: 2025-05-02 04:49:03 UTC  
> Closed at: 2025-05-02 04:49:03 UTC  
> Url: https://github.com/boostorg/context/pull/301  

The Ubuntu 20.04 image on GitHub Actions has been unavailable since 2025-04-15. See <https://github.com/actions/runner-images/issues/11101> for more information on the deprecation and removal.  
  
Therefore all build jobs that use the Ubuntu 20.04 runner image of GHA will fail and have to be replaced or removed.  
  
Also see <https://github.com/boostorg/context/actions/runs/14540788005/job/40798186788> for an example for a failing build on `ubuntu-20.04`. It fails with the error message:  
  
> This is a scheduled Ubuntu 20.04 retirement. Ubuntu 20.04 LTS runner will be removed on 2025-04-15. For more details, see https://github.com/actions/runner-images/issues/11101

---

## Comment 1

> Username: olk  
> Created_at: 2025-05-02 04:49:07 UTC  
> Url: https://github.com/boostorg/context/pull/301#issuecomment-2846307071  

ty

---
