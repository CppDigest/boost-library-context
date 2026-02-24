# #100 consolidate boostlook css files and simplify sync workflow [Merged]

> Username: julioest  
> Created at: 2025-03-31 17:17:13 UTC  
> Updated at: 2025-04-10 21:17:02 UTC  
> Merged at: 2025-04-10 21:17:02 UTC  
> Closed at: 2025-04-10 21:17:02 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100  

- Simplified sync workflow to use single CSS file across v2 and v2-docs branches

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-03-31 17:23:19 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#issuecomment-2766891701  

An automated preview of the documentation is available at [https://100.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://100.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 2 [Commented]

> Username: sdarwin  
> Created_at: 2025-03-31 17:44:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/100#pullrequestreview-2730027443  

📁 .github/workflows/sync-boostlook-css.yml

```diff
   5 |     branches: ["master", "develop"]
```

> Username: sdarwin  
> Created_at: 2025-03-31 17:44:55 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2021463737  

Recently this was:  
`branches: ["master"]`  
it should return to that right?

> Username: julioest  
> Created_at: 2025-04-01 14:22:47 UTC  
> Updated_at: 2025-04-01 14:22:48 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2022970366  

Well, we'd still like to trigger develop, so staging gets latest styles

> Username: sdarwin  
> Created_at: 2025-04-01 14:47:31 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2023017860  

what is the "source of truth" for boostlook, either `master` or `develop` ?    
  
Probably either could make sense in its own way.  Prior to March 7 commit 2f6553ba it was develop :  
  
`branches: ["develop"]`  
  
After 2f6553ba :  
  
`branches: ["master"]`  
  
`master` will always be either equal or lagging behind `develop`.    That means if `develop` is chosen, there is no reason to have `master` in the list.    
  
In both cases, they push to `develop` on other repos (which is the intended method).

> Username: julioest  
> Created_at: 2025-04-01 15:02:37 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2023047032  

Ah, I see what you mean now. Yep, makes no sense to have master. Removing it...


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-04-01 15:13:22 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#issuecomment-2769710571  

An automated preview of the documentation is available at [https://100.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://100.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 4 [Commented]

> Username: julioest  
> Created_at: 2025-04-01 20:19:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/100#pullrequestreview-2733957286  

📁 .github/workflows/sync-boostlook-css.yml

```diff
  60 |           fi
  61 |       - name: Trigger website-v2-docs ui-release workflow
  69 |-         if: ${{ (github.ref == 'refs/heads/master' || github.ref == 'refs/heads/develop') && github.event_name == 'push' }}
```

> Username: julioest  
> Created_at: 2025-04-01 20:19:38 UTC  
> Updated_at: 2025-04-01 20:19:39 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2023622170  

@sdarwin Can this stay?

> Username: sdarwin  
> Created_at: 2025-04-01 20:47:09 UTC  
> Updated_at: 2025-04-01 20:49:08 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2023655132  

Here is just an idea. Tell me if you see a flaw.    
"can this stay" -> yes.   But then also the line further below, `--ref develop` changes to  -> `--ref ${{ (github.ref }}`.    
- `develop` boostlook will trigger `develop` v2-docs.   
- `master` boostlook will trigger `master` v2-docs.

> Username: julioest  
> Created_at: 2025-04-10 20:42:29 UTC  
> Url: https://github.com/boostorg/boostlook/pull/100#discussion_r2038297602  

Resolving comment because I'll re-visit with best practice changes in another PR


---

## Review 5 [Approved]

> Username: sdarwin  
> Created_at: 2025-04-10 21:16:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/100#pullrequestreview-2758439681  

it generally looks correct. if you believe this is right, ok.

---
