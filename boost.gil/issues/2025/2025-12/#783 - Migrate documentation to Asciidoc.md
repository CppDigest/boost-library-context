# #783 - Migrate documentation to Asciidoc [Open]

> Username: mloskot  
> Created at: 2025-12-09 19:57:51 UTC  
> Updated at: 2026-01-09 11:42:40 UTC  
> Url: https://github.com/boostorg/gil/issues/783  

The Asciidoc has been in use in Boost for long time, see [Asciidoc, an alternative for documentation](https://groups.google.com/g/boost-developers-archive/c/XhjlywsNGJg?pli=1). More and more libraries seem to be converting their documentation to Asciidoc, for example  
- https://github.com/boostorg/array/pull/26  
- MP11 in https://github.com/boostorg/mp11/commit/f93e0fa3e7c6a5910a30115e5ed2f9a925f2e8da  
  
If there are no objections, we should consider migration from Sphinx to Asciidoc for unified UI/UX with the rest of Boost docs and to solve issues like   
- https://github.com/boostorg/gil/issues/782  
  
## References  
- https://www.boost.org/doc/contributor-guide/docs/content.html

---

## Comment 1

> Username: mloskot  
> Created at: 2025-12-15 08:59:37 UTC  
> Updated at: 2025-12-15 11:00:17 UTC  
> Url: https://github.com/boostorg/gil/issues/783#issuecomment-3654509552  

I've [asked on cpplang](https://cpplang.slack.com/archives/C27KZLB0X/p1765788267039759?thread_ts=1765786536.690549&cid=C27KZLB0X):  
  
> which library would be the best example how it should be done after the Boost website, docs, etc. revamp?  
  
and received useful answer:  
  
> Depends on if you want a **single page** doc (good for small libs) or **multi page**  
> Single page look at [boost.mp11](https://www.boost.org/doc/libs/latest/libs/mp11/doc/html/mp11.html)  
> Multipage check out [boost.url](https://www.boost.org/doc/libs/latest/doc/antora/url/index.html)  
  
and   
  
> [Boost.JSON](https://www.boost.org/doc/libs/latest/libs/json/doc/html/index.html) also uses asciidoc, but achieves multipage with different tools  
  
I thought Asciidoc-based docs in Boost are always single-page. The multi-page variant may be suitable for GIL.  
  
Do we have any preferences?  
/cc @sdebionne @planetmarshall @boostorg/gil

---

## Comment 2

> Username: sdebionne  
> Created at: 2025-12-15 11:10:44 UTC  
> Url: https://github.com/boostorg/gil/issues/783#issuecomment-3655062225  

I think that GIL is a rather large library so I would go for the multi-page variant too.

---

## Comment 3

> Username: mloskot  
> Created at: 2025-12-15 11:34:36 UTC  
> Updated at: 2025-12-15 13:21:27 UTC  
> Url: https://github.com/boostorg/gil/issues/783#issuecomment-3655155849  

I agree with you @sdebionne   
  
I've received some more feedback on cpplang to my question from https://github.com/boostorg/gil/issues/783#issuecomment-3654509552, on tooling requirement:  
  
> only asciidoc, single page: one of Peter libs, e.g. variant2, mp11, compat  
> asciidoc + antora (multiple pages): unordered  
> asciidoc + antora + mrdocs (like Doxygen, but generates asciidoc): redis or url (probably Redis is smaller and simpler)  
> asciidoc + antora + mrdocs + standalone doc folder : openmethod
