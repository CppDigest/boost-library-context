# #76 update actions/checkout in GitHub Actions to v3 [Merged]

> Username: striezel  
> Created at: 2022-06-14 21:01:49 UTC  
> Updated at: 2022-06-25 23:14:22 UTC  
> Merged at: 2022-06-25 10:59:01 UTC  
> Closed at: 2022-06-25 10:59:01 UTC  
> Url: https://github.com/boostorg/intrusive/pull/76  

Updates the `actions/checkout` actions used in the GitHub Actions workflow to its newest version.  
  
Changes in [actions/checkout](https://github.com/actions/checkout):  
  
> ## v3.0.2  
> - [Add input `set-safe-directory`](https://github.com/actions/checkout/pull/770)  
>  
> ## v3.0.1  
> - [Fixed an issue where checkout failed to run in container jobs due to the new git setting `safe.directory`](https://github.com/actions/checkout/pull/762)  
> - [Bumped various npm package versions](https://github.com/actions/checkout/pull/744)  
>  
> ## v3.0.0  
>  
> - [Update to node 16](https://github.com/actions/checkout/pull/689)  
  
As far as I can tell this should be backwards compatible, so I do not expect any breakage.

---
