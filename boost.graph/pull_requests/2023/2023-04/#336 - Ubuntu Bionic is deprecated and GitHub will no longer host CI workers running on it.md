# #336 Ubuntu Bionic is deprecated and GitHub will no longer host CI workers running on it [Merged]

> Username: daankolthof  
> Created at: 2023-04-25 10:38:45 UTC  
> Updated at: 2023-05-01 23:24:55 UTC  
> Merged at: 2023-04-27 04:21:45 UTC  
> Closed at: 2023-04-27 04:21:45 UTC  
> Url: https://github.com/boostorg/graph/pull/336  

GitHub blog: https://github.blog/changelog/2022-08-09-github-actions-the-ubuntu-18-04-actions-runner-image-is-being-deprecated-and-will-be-removed-by-12-1-22/  
  
Also fixes:  
- Deprecation of boost/progress.hpp  
  
Hopefully after this PR is merged the CI builds actually succeed for the develop branch. After that, new features can be implemented.

---

## Comment 1

> Username: daankolthof  
> Created_at: 2023-04-25 10:46:35 UTC  
> Updated_at: 2023-04-25 10:46:47 UTC  
> Url: https://github.com/boostorg/graph/pull/336#issuecomment-1521575530  

@jeremy-murphy I would like to have this (pre-)approved as it's blocking everything else and thus needs to be merged as soon as possible.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2023-04-25 12:08:00 UTC  
> Url: https://github.com/boostorg/graph/pull/336#issuecomment-1521679081  

Looks good, but it's late at night for me so I'll merge it in the morning after I have a complete check.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-04-27 04:22:05 UTC  
> Url: https://github.com/boostorg/graph/pull/336#issuecomment-1524676452  

Awesome, thanks for making those changes!

---

## Comment 4

> Username: daankolthof  
> Created_at: 2023-04-27 09:30:02 UTC  
> Url: https://github.com/boostorg/graph/pull/336#issuecomment-1525260722  

Thanks for merging this @jeremy-murphy, maybe we add the latest versions of the GitHub Action Runners? See https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners#supported-runners-and-hardware-resources.  
This obviously depends on Boost's strategy for integration testing, which I don't know much about.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2023-04-28 01:11:27 UTC  
> Url: https://github.com/boostorg/graph/pull/336#issuecomment-1526842111  

> Thanks for merging this @jeremy-murphy, maybe we add the latest versions of the GitHub Action Runners? See https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners#supported-runners-and-hardware-resources. This obviously depends on Boost's strategy for integration testing, which I don't know much about.  
  
The Boost organization provides resources, but each library has their own strategy.  
  
I support adding the latest Ubuntu image as a runner. Let's not use the `xxx-latest` label, though, as that causes unwanted surprises when they change it to point to a new image.  :)

---

## Comment 6

> Username: daankolthof  
> Created_at: 2023-04-30 21:18:11 UTC  
> Url: https://github.com/boostorg/graph/pull/336#issuecomment-1529141467  

> > Thanks for merging this @jeremy-murphy, maybe we add the latest versions of the GitHub Action Runners? See https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners#supported-runners-and-hardware-resources. This obviously depends on Boost's strategy for integration testing, which I don't know much about.  
>   
> The Boost organization provides resources, but each library has their own strategy.  
>   
> I support adding the latest Ubuntu image as a runner. Let's not use the `xxx-latest` label, though, as that causes unwanted surprises when they change it to point to a new image. :)  
  
Sounds good! If you could create an Issue, I can pick it up sometime in May.

---
