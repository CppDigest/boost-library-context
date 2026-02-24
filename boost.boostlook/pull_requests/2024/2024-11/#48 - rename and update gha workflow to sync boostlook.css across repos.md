# #48 rename and update gha workflow to sync boostlook.css across repos [Merged]

> Username: julioest  
> Created at: 2024-11-25 14:18:41 UTC  
> Updated at: 2024-11-25 19:32:21 UTC  
> Merged at: 2024-11-25 19:32:21 UTC  
> Closed at: 2024-11-25 19:32:21 UTC  
> Url: https://github.com/boostorg/boostlook/pull/48  

- renamed workflow file from update-website-v2.yml to sync-boostlook-css.yml  
- simplified commit process to push changes directly to develop branch if updates are detected  
- added steps to trigger ui-release and publish workflows in website-v2-docs repository  
- ensured pre-commit hooks are run before committing updates  
  
fixes #44

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-11-25 14:28:21 UTC  
> Url: https://github.com/boostorg/boostlook/pull/48#issuecomment-2498169837  

An automated preview of the documentation is available at [https://48.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://48.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 2

> Username: julioest  
> Created_at: 2024-11-25 14:46:20 UTC  
> Url: https://github.com/boostorg/boostlook/pull/48#issuecomment-2498221527  

Hi @pdimov! Could you approve the token requested for website-v2-docs? Thanks

---
