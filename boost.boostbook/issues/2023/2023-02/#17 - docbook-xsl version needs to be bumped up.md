# #17 - docbook-xsl version needs to be bumped up [Closed]

> Username: vinniefalco  
> Created at: 2023-02-17 01:50:46 UTC  
> Updated at: 2024-09-26 07:56:09 UTC  
> Closed at: 2024-09-26 07:56:09 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17  

The Python and bash setup scripts need to use docbook-xsl version 1.79.1 or else there are errors applying templates. To make things interesting, the archives are only offered in bz2 and not gzip anymore, so the scripts also need to be modified to reflect that.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-09-22 18:06:03 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2366898125  

I get these errors as well. But `1.79.2` (one patch higher) gives documentation which is quite broken (the chunks), at least for Boost.Geometry  
  
The errors are caused by https://github.com/boostorg/boostbook/commit/116c7b0f23b578ece6f5c530584a987f4db6947b which should be reverted or adapted.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-09-22 18:11:25 UTC  
> Updated at: 2024-09-22 18:11:49 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2366900043  

This  
```  
diff --git a/xsl/sections.xsl b/xsl/sections.xsl  
index e076471..21fae40 100644  
--- a/xsl/sections.xsl  
+++ b/xsl/sections.xsl  
@@ -104,9 +104,6 @@  
   </xsl:variable>  
   
   <xsl:element name="h{$hlevel}">  
-    <xsl:call-template name="id.attribute">  
-      <xsl:with-param name="conditional" select="0"/>  
-    </xsl:call-template>  
     <xsl:call-template name="anchor">  
       <xsl:with-param name="conditional" select="0"/>  
     </xsl:call-template>  
```  
  
fixes the problems (for Boost.Geometry), but I don't know the consequences for other libraries.  
  
(@Lastique I don't see the comments on the commit itself anymore in github, at the moment)

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-09-22 18:22:13 UTC  
> Updated at: 2024-09-22 18:24:47 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2366902994  

@Lastique I'm moving the conversion (#116c7b0) on that commit/PR here (because it does not show me its contents anymore)  
  
  
>  I don't know the details about the problem that led to this change. From https://github.com/boostorg/boostbook/issues/12 it looks like boostbook.css only supports headings up to h6 (and it still does), hence the limit. If we want deeper headings (do we?) then we need to update the CSS first.  
  
> The id.attribute template is supposed to be provided by DocBook XSL. I guess, 1.75.2 is too old and doesn't provide it.  
  
But `1.75.2` the one supplied by default...  
  
> I don't think your proposed change is correct.   
  
All right, I'm not an xml expert, I just applied what you did somewhere else and it helped. But it's not a real proposal, it's an idea. However, a fix **is necessary**  
  
> > With docbook-xsl-1.79.2 this error disappears - but then the chunking is quite broken.  
  
> What do you mean by this? Is there a reproducer?  
  
That Boost.Geometry's documentation does not have pages anymore with a table of contents, apart from the first one.  
For example https://www.boost.org/doc/libs/1_85_0/libs/geometry/doc/html/geometry/reference/concepts.html would be empty. I tried modifying the `toc` related parameters, but to no avail, I could not get any combination which is giving the same documentation as we currently have (and I tried many).

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-09-23 07:03:08 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2367377651  

Or maybe that `1.79.1` fixes the problem (but `1.79.2`, the one that I tried, breaks the chunking - at least for me locally). I'll try it soon.  
I noticed that the published documentation has `<meta name="generator" content="DocBook XSL Stylesheets V1.79.1">` so - then it should somehow work.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-09-23 17:02:45 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2368859959  

Version `1.79.1` gives indeed correct documentation for Boost.Geometry

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-09-26 06:31:22 UTC  
> Updated at: 2024-09-26 06:33:25 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2376048594  

FYI: version `1.79.2` (non namespace version) also gives correct documentation.  
For Mac, the namespaced version is the default. For Ubuntu, `nons` is the default (but not named as such).  
That's a subtle but important detail that should be mentioned somewhere in the documentation.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2024-09-26 06:35:50 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2376055102  

I noticed the `setup_boost.sh` is removed, all right. And `setup_boost.py` is updated.  
So this issue can be closed.  
  
Maybe we can (in the future) point to https://github.com/boostorg/release-tools/tree/develop/build_docs in the readme and just remove `setup_boost.sh` to leverage maintenance.

---

## Comment 8

> Username: barendgehrels  
> Created at: 2024-09-26 06:40:29 UTC  
> Url: https://github.com/boostorg/boostbook/issues/17#issuecomment-2376062069  

@Lastique thanks for the big upgrade of `setup_boost.py`  
❤️
