# #155 Update actions/checkout in GitHub Actions to v4 [Closed]

> Username: striezel  
> Created at: 2024-04-30 22:04:55 UTC  
> Updated at: 2024-05-17 05:34:08 UTC  
> Closed at: 2024-04-30 22:40:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/155  

This updates [actions/checkout](https://github.com/actions/checkout) in the GitHub Actions workflow to v4.  
  
Still using the older v3 will generate warnings in CI runs, for example in https://github.com/boostorg/uuid/actions/runs/8901444927:  
  
> Node.js 16 actions are deprecated. Please update the following actions to use Node.js 20: actions/checkout@v3. For more information see: https://github.blog/changelog/2023-09-22-github-actions-transitioning-from-node-16-to-node-20/.  
  
The PR will get rid of those warnings.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-30 22:32:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/155#issuecomment-2087604438  

The reason for sticking with v3 in this specific case is https://github.com/actions/checkout/issues/1590. We'll have to figure something out eventually if GHA doesn't fix it and kills Node 16, but for now, I just live with the warnings.

---

## Comment 2

> Username: striezel  
> Created_at: 2024-04-30 22:40:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/155#issuecomment-2087627892  

I see. So the older Ubuntu 18.04 and 16.04 containers don't work with the newer Node.js 20. Let's see whether GH fixes the Node.js problem. I'll close this PR for now.  
  
> We'll have to figure something out eventually if GHA doesn't fix it and kills Node 16, but for now, I just live with the warnings.  
  
I guess in that case one could still try to install whatever version of Git is available in those containers and manually do a `git clone` there.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-05-15 13:53:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/155#issuecomment-2112607543  

FYI, I've been removing `actions/checkout` usage in my repositories for this exact problem and replacing them with curl downloading source snapshots from GitHub. You can see an example [here](https://github.com/boostorg/core/blob/965508d9e187d589070c5cd1853cc035b51ea3cf/.github/workflows/ci.yml#L469-L484).  
  
As a bonus, this permanently solves the obsolescence warnings that periodically appear whenever GitHub feels like.

---

## Comment 4

> Username: striezel  
> Created_at: 2024-05-16 22:50:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/155#issuecomment-2116326460  

Using curl is one option, another would be to just use `git clone` instead, because many workflows already have some version of Git installed.  
  
If you want to keep the actions-based stuff, then a more verbose approach is to use v3 for jobs running on older containers and v4 for newer environments: https://github.com/boostorg/nowide/commit/dd1a4f163079d72be2cb2922d472da0a39ef89c7

---

## Comment 5

> Username: Lastique  
> Created_at: 2024-05-17 05:34:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/155#issuecomment-2116703096  

On May 17, 2024 1:50:50 AM Dirk Stolle ***@***.***> wrote:  
  
> Using curl is one option, another would be to just use `git clone` instead,   
> because many workflows already have some version of Git installed.  
  
Clone is more expensive as it downloads history. Shallow clone is not   
supported for arbitrary commits.

---
