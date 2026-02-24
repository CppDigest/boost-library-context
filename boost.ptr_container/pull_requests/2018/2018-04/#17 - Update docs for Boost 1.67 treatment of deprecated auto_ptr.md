# #17 Update docs for Boost 1.67 treatment of deprecated auto_ptr [Merged]

> Username: cstratopoulos  
> Created at: 2018-04-08 05:05:16 UTC  
> Updated at: 2018-04-09 16:45:37 UTC  
> Merged at: 2018-04-09 12:53:07 UTC  
> Closed at: 2018-04-09 12:53:07 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17  

This PR is the documentation counterpart to https://github.com/boostorg/ptr_container/pull/8/ coded by @DanielaE.   
  
For completeness sake, I'm going to rehash some points from the issue I opened (https://github.com/boostorg/ptr_container/issues/16) before starting this PR.  
  
The main idea is that in the tradition of pseudo-types such as _`implementation-defined`_, _`hidden`_, _`void-or-deduced`_, etc., we introduce the type _`compatible-smart-ptr`_ to be used as a stand-in for compile-time management of smart pointer interfaces. It should always appear in monospaced italics with a link back to an explanatory page, similar to e.g., [this page](https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html) from the Boost.Beast docs.  
  
The central features of this PR, which can easily be previewed in browser, are  
  
- The [Upgrading from Boost 1.66 page](https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/doc/ptr_container.rst#upgrading-from-boost-v-1-66)  
- The dedicated [Compatible Smart Pointer](https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/doc/compatible_smart_ptr.rst) page  
- Updates to the [tutorial section on smart pointer overloads](https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/doc/tutorial.rst#compatible-smart-pointer-overloads)  
  
Due to limitations with reStructuredText, it is not directly possible to write italic monospaced hyperlinks. The result is that raw rst files (e.g., [reversible_ptr_container](https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/doc/reversible_ptr_container.rst)) just have `compatible-smart-ptr` written. For all such pages the corresponding shell script used to generate HTML documentation has been updated to include an intermediate pipe to `sed` to add italics and a hyperlink (e.g., [comp_rever_ptr_container.sh](https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/doc/comp_rever_ptr_container.sh)). This approach is a bit ugly but reading around online indicates that editing raw HTML is the most common way to work around this rst limitation. The old behavior of being able to generate documentation with `sh comp_all.sh` is unchanged.   
  
Finally, I have edited [tutorial_example.html](https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/doc/tutorial_example.html) to approximately reflect the `raii_ptr` alias introduced to `tut1.cpp`: https://github.com/cstratopoulos/ptr_container/blob/feature/auto_ptr-deprecate-doc-update/test/tut1.cpp#L144

---

## Comment 1

> Username: danieljames  
> Created_at: 2018-04-09 12:04:55 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379728654  

Is this intended for 1.67? If it is, it'll have to be merged soon.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-04-09 12:48:08 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379739761  

Let me look at this and then, if it is OK, I will merge the pull request and then merge to master.

---

## Comment 3

> Username: danieljames  
> Created_at: 2018-04-09 12:56:01 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379741916  

Thank you. I'd prefer to be able to check the develop branch documentation on the website first, which can take a few hours to update. So I might not update master until this evening.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-04-09 12:58:18 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379742620  

That is fine. Tell me when you want to merge these changes to master and I will do it, otherwise feel free to do it yourself when you are ready.

---

## Comment 5

> Username: danieljames  
> Created_at: 2018-04-09 12:59:44 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379743163  

Sorry I wasn't clear, the master branch isn't automatically updating, so I'll update it manually once I've checked the website updated okay. You can merge to master when you want,

---

## Comment 6

> Username: eldiener  
> Created_at: 2018-04-09 13:06:19 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379745019  

I merge the doc changes to the master branch. Thank you for doing this Christos and providing these documentation updates..

---

## Comment 7

> Username: cstratopoulos  
> Created_at: 2018-04-09 13:28:09 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379751471  

@eldiener happy to help! Will be near my computer pretty much  all day today if any last minute fixes/typos pop up that I can help with.

---

## Comment 8

> Username: danieljames  
> Created_at: 2018-04-09 15:13:38 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379787526  

Thanks, everyone. The develop branch is up:  
  
https://www.boost.org/doc/libs/develop/libs/ptr_container/doc/ptr_container.html  
  
And I've updated master.

---

## Comment 9

> Username: eldiener  
> Created_at: 2018-04-09 16:45:37 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/17#issuecomment-379817709  

Great ! Thanks Daniel !

---
