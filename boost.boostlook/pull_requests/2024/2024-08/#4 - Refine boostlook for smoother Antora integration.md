# #4 Refine boostlook for smoother Antora integration [Merged]

> Username: julioest  
> Created at: 2024-08-28 13:57:22 UTC  
> Updated at: 2024-08-28 21:07:43 UTC  
> Merged at: 2024-08-28 21:07:42 UTC  
> Closed at: 2024-08-28 21:07:43 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4  

Adjusted font sizes, margins, paddings, and table styles. Removed unnecessary font backgrounds in code snippets.

---

## Review 1 [Commented]

> Username: rbbeeston  
> Created_at: 2024-08-28 15:36:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/4#pullrequestreview-2266645297  

📁 boostlook.css

```diff
 162 | 
 149 |- .boostlook:not(.body) {
 163 |+ .article.toc2.toc-left {
```

> Username: rbbeeston  
> Created_at: 2024-08-28 15:36:51 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1734910860  

```diff  
+.boostlook .article.toc2.toc-left {  
```

> Username: julioest  
> Created_at: 2024-08-28 15:41:12 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1734916913  

We're not able to do that since `.boostlook` is inside `.article.toc2.toc-left` for asciidoc

> Username: rbbeeston  
> Created_at: 2024-08-28 15:49:06 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1734927588  

actually, since these are formatting issues, they should  probably belong in the docs css, as the boostlook code should not contain layout formatting, which is what that appears to do. boostlook classes should be universal to whichever doc they are being used in.

> Username: julioest  
> Created_at: 2024-08-28 16:03:43 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1734949007  

I agree, but the suggestion breaks the layout for asciidoc. The rule has to live here since we're not modifying `asciidoc.css` directly.  
  
Here's the built html: [mp11.html.zip](https://github.com/user-attachments/files/16785997/mp11.html.zip)

> Username: rbbeeston  
> Created_at: 2024-08-28 16:11:20 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1734959376  

got it, in that case we should try to have any specific content changes inside a commented block, something like:  
```  
/*----------------- changes specific to asciidocs -----------------*/  
  
/*-----------------end  changes specific to asciidocs -----------------*/  
```  
  
because we want future devs to be aware that these aren't global effects. and where specifically they are used.

> Username: julioest  
> Created_at: 2024-08-28 16:19:03 UTC  
> Updated_at: 2024-08-28 16:35:41 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1734968971  

Love that ❤️. I'll update   
  
Edit: updated, resolving comment


---

## Review 2 [Changes requested]

> Username: rbbeeston  
> Created_at: 2024-08-28 15:38:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boostlook/pull/4#pullrequestreview-2266648588  

one minor thing otherwise LGTM

---

## Review 3 [Commented]

> Username: rbbeeston  
> Created_at: 2024-08-28 18:26:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/4#pullrequestreview-2266986496  

📁 boostlook.css

```diff
 138 |+   --bl-background: rgb(255, 255, 255);
 139 |+   --bl-card-background-color: rgb(255, 255, 255);
 140 |+ }
```

> Username: rbbeeston  
> Created_at: 2024-08-28 18:26:46 UTC  
> Updated_at: 2024-08-28 18:29:16 UTC  
> Url: https://github.com/boostorg/boostlook/pull/4#discussion_r1735121209  

root applies to all content, not just asciidoc. This should probably go at the top of the file.  
above that there should be the "read me" of explaining what classes belong to antora or asciidoc so they can be understood.


---

## Review 4 [Approved]

> Username: rbbeeston  
> Created_at: 2024-08-28 20:34:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/4#pullrequestreview-2267217814  

looks great! Push it!!

---
