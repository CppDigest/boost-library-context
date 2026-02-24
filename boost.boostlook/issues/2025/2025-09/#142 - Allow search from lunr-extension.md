# #142 - [Feature] Allow search from lunr-extension [Closed]

> Username: mborland  
> Created at: 2025-09-10 13:36:36 UTC  
> Updated at: 2026-01-26 18:00:37 UTC  
> Closed at: 2026-01-26 18:00:37 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142  

With the lunr-extension: https://gitlab.com/antora/antora-lunr-extension, we can trivially add documentation search to individual library pages. This currently does not work with boostlook. I can change my UI bundle to the default Antora one which gives me the search bar but also comes with the other baggage in the top bar:  
  
<img width="1286" height="361" alt="Image" src="https://github.com/user-attachments/assets/d69646e7-fa1e-4ebd-aa22-d5a8676cc2ff" />  
  
The gitlab page for the antora-lunr-extension has instructions on how to make your custom UI work with it.

---

## Comment 1

> Username: julioest  
> Created at: 2026-01-16 00:24:25 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3757499396  

Hey @mborland, how's this looking?   
  
https://cheerful-rabanadas-34ee2d.netlify.app/decimal/overview.html

---

## Comment 2

> Username: mborland  
> Created at: 2026-01-16 12:35:25 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3759839231  

> Hey @mborland, how's this looking?   
>   
> https://cheerful-rabanadas-34ee2d.netlify.app/decimal/overview.html  
  
Much better than my hackish style sheets for sure. Once this is merged in do I just need to install the antora-lunr extension?   
  
Thanks for adding this!

---

## Comment 3

> Username: julioest  
> Created at: 2026-01-16 17:43:51 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761125492  

> Once this is merged in do I just need to install the antora-lunr extension?  
  
That's correct! You can test now pointing to the `develop` bundle

---

## Comment 4

> Username: mborland  
> Created at: 2026-01-16 18:24:55 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761271394  

> > Once this is merged in do I just need to install the antora-lunr extension?  
>   
> That's correct! You can test now pointing to the `develop` bundle  
  
Is that: `https://github.com/boostorg/website-v2-docs/releases/download/ui-develop/ui-bundle.zip` for the bundle URL? That bundle shows a tag of April 2023?

---

## Comment 5

> Username: mborland  
> Created at: 2026-01-16 18:35:38 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761303600  

Ok, I ran that bundle locally on int128 (https://github.com/cppalliance/int128) since it's currently using boostlook. I pointed to the ui-develop bundle, and installed the lunr extension ("@antora/lunr-extension": "^1.0.0-alpha.12"). I get the following:  
  
<img width="1227" height="603" alt="Image" src="https://github.com/user-attachments/assets/ab49a023-c8ec-47af-baf2-f9c1d99a126d" />

---

## Comment 6

> Username: mborland  
> Created at: 2026-01-16 19:15:01 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761434364  

Here's a PR with doc preview so that you can see what I am seeing: https://github.com/cppalliance/int128/pull/294

---

## Comment 7

> Username: julioest  
> Created at: 2026-01-16 20:10:02 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761623741  

The job failed, but I reset it and it published. Rebuild your docs again

---

## Comment 8

> Username: mborland  
> Created at: 2026-01-16 20:36:10 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761727919  

> The job failed, but I reset it and it published. Rebuild your docs again  
  
Rebuilt the docs and I see the same behavior both locally and on that linked PR.

---

## Comment 9

> Username: julioest  
> Created at: 2026-01-16 20:57:55 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3761788747  

looking into it

---

## Comment 10

> Username: julioest  
> Created at: 2026-01-16 23:03:24 UTC  
> Updated at: 2026-01-16 23:03:53 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3762163097  

So, it seems that the develop ui-bundle is actually master's version because of how we have it set it up in v2-docs. So, we'll need to wait on master to be updated.  
  
But here's a branch with a workaround, for now, to fix the ui bugs: https://github.com/julioest/int128/tree/supplemental-boostlook-workaround

---

## Comment 11

> Username: mborland  
> Created at: 2026-01-20 13:17:30 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3772846675  

@rbbeeston Is it possible to setup a job that cuts releases on updates to develop? I'm not sure if tying everything to master is ideal. At one point develop releases were available here: https://github.com/boostorg/website-v2-docs/releases. The ui-bundles could also be generated on this repo as to not cause extra noise on website-v2-docs.

---

## Comment 12

> Username: julioest  
> Created at: 2026-01-26 18:00:37 UTC  
> Url: https://github.com/boostorg/boostlook/issues/142#issuecomment-3800922664  

✅ Confirmed working on https://develop.decimal.cpp.al/decimal/overview.html  
  
Closing this issue as complete.
