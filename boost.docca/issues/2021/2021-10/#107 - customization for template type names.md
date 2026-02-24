# #107 - customization for template type names? [Closed]

> Username: vinniefalco  
> Created at: 2021-10-30 21:07:06 UTC  
> Updated at: 2021-10-31 18:10:24 UTC  
> Closed at: 2021-10-31 18:10:24 UTC  
> Url: https://github.com/boostorg/docca/issues/107  

In Beast, particular template type names are customized as links, e.g. `Protocol`, `Executor`, and `RatePolicy` here:  
  
![image](https://user-images.githubusercontent.com/1503976/139558370-45a88ca5-916e-4720-8a26-6beb091c846e.png)  
  
But I don't see the customization override for anywhere in the beast/doc files? How do I add my own customizations for Boost.URL?

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-10-31 03:55:49 UTC  
> Url: https://github.com/boostorg/docca/issues/107#issuecomment-955632379  

It looks like these are in the docca code base but should be in Beast's custom-overrides.xsl file. I'll fix that by moving it to the Beast project's custom-overrides.xsl file (and leave an empty variable in the docca code that can be overridden by each project). Then you'll see how it can be done.
