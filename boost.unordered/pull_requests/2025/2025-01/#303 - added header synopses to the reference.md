# #303 added header synopses to the reference [Merged]

> Username: joaquintides  
> Created at: 2025-01-22 14:04:04 UTC  
> Updated at: 2025-01-23 19:33:42 UTC  
> Merged at: 2025-01-23 19:33:34 UTC  
> Closed at: 2025-01-23 19:33:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/303  

Added header synopses to the reference in the style of the standard. Solves #301.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-01-22 14:13:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2607358684  

An automated preview of the documentation is available at [https://303.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://303.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Review 2 [Commented]

> Username: cmazakas  
> Created_at: 2025-01-22 18:12:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/303#pullrequestreview-2567942216  

📁 doc/modules/ROOT/pages/ref.adoc

```diff
   7 |- * xref:reference/unordered_multiset.adoc[unordered_multiset]
   8 |- * xref:reference/hash_traits.adoc[hash_traits]
   4 |+ * xref:reference/header_unordered_map_fwd.adoc[`<boost/unordered/unordered_map_fwd.hpp>` Synopsis]
```

> Username: cmazakas  
> Created_at: 2025-01-22 18:12:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#discussion_r1925761438  

Try putting the code backticks on the outside of the `xref` and tell me if you like how it looks.  
  
![image](https://github.com/user-attachments/assets/8f4e03ff-2c24-4a45-8339-e36b601db647)  
  
In this image, the top two links are the updated version. The third one is the current look.

> Username: joaquintides  
> Created_at: 2025-01-22 19:59:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#discussion_r1925890863  

I can solve this in a more satisfactory way, for that I'd need for you to add this definition to the source `site.css`:  
  
```  
.inlink-code {  
    font-family: Roboto Mono,monospace;  
    font-size: 95%;  
    color: inherit;  
}  
```  
  
Can you do that?

> Username: cmazakas  
> Created_at: 2025-01-22 20:32:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#discussion_r1925925961  

Of course. You can do that locally yourself even.  
  
All you gotta clone is the unordered-ui-bundle repo and then invoke `gulp bundle` in it. There's docs here: https://gitlab.com/antora/antora-ui-default#user-content-development-quickstart

> Username: joaquintides  
> Created_at: 2025-01-23 08:26:42 UTC  
> Updated_at: 2025-01-23 08:32:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#discussion_r1926556791  

I managed to do it without changing the CSS file: 191adf4a3028b88010e9c4e735380fc79bddaffe.


---

## Comment 3

> Username: cmazakas  
> Created_at: 2025-01-22 18:46:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2608008964  

I really like these changes but we'll probably have to update the UI bundle.  
  
Consider:  
![image](https://github.com/user-attachments/assets/184ce35d-03f1-4ed5-9013-d0be3d3c183a)  
  
The reference is awfully cramped here on my screen.  
  
I did a width adjustment in the CSS and this is how it looks now:  
![image](https://github.com/user-attachments/assets/511d7475-f5d8-46d9-ba88-82c9eba6f34e)  
  
Let me know what you think. It's kind of nice how much we can really fine tune here.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-01-23 08:33:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2609158351  

An automated preview of the documentation is available at [https://303.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://303.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-01-23 08:45:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2609183188  

An automated preview of the documentation is available at [https://303.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://303.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 6

> Username: joaquintides  
> Created_at: 2025-01-23 10:17:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2609414464  

> The reference is awfully cramped here on my screen.  
>   
> I did a width adjustment in the CSS and this is how it looks now:   
> [...]  
> Let me know what you think. It's kind of nice how much we can really fine tune here.  
  
My concern with that is that less space is reserved for the actual content, and the whole page's width is constrained in boost.io. Is it possible to have the TOC's width be adjustable by the user by dragging its right border?

---

## Comment 7

> Username: cmazakas  
> Created_at: 2025-01-23 18:57:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2610780369  

> > The reference is awfully cramped here on my screen.  
> > I did a width adjustment in the CSS and this is how it looks now:  
> > [...]  
> > Let me know what you think. It's kind of nice how much we can really fine tune here.  
>   
> My concern with that is that less space is reserved for the actual content, and the whole page's width is constrained in boost.io. Is it possible to have the TOC's width be adjustable by the user by dragging its right border?  
  
Ah, that's a fair point. It looked okay when I sit the `width: auto;' but we can look into making it manually adjustable, yeah.

---

## Comment 8

> Username: joaquintides  
> Created_at: 2025-01-23 19:33:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/303#issuecomment-2610850802  

> Ah, that's a fair point. It looked okay when I sit the `width: auto;' but we can look into making it manually adjustable, yeah.  
  
As per our private conversation, moving this to another issue and merging this PR now.

---
