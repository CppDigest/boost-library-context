# #141 Support doxygen 1.9.7 [Merged]

> Username: sdkrystian  
> Created at: 2023-07-25 16:05:46 UTC  
> Updated at: 2023-07-26 14:59:56 UTC  
> Merged at: 2023-07-26 14:59:55 UTC  
> Closed at: 2023-07-26 14:59:55 UTC  
> Url: https://github.com/boostorg/docca/pull/141  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-25 16:14:50 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650139165  

An automated preview of the documentation is available at [https://141.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://141.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-07-25 20:49:20 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650540711  

I guess this obsoletes #140. We can actually put everything in a single PR that fixes the transition from 1.8.15 to 1.9.7.  
  
Just tested this locally. So this fixes errors like  
  
```  
Error: no ID for constraint linkend: "url.ref.boost__urls__url_view_base.url_view".  
```  
  
but we still have   
  
```  
bin.v2/libs/url/doc/reference.qbk:65373: warning: in column:69, unexpected character: `  
```  
  
which is having a big impact on the docs, since most pages have links in the javadoc details. Many of the autolinks in the BNF might be just wrong but the ones in the examples are usually not.  
  
I can see how autolink is *related* to the issue in the sense that there would be no broken links if there were links there. But I can't see why autolink would be *causing* the issue even if it's a good idea to remove it (we'd have to think about the examples) since doxygen 1.8.15 did work OK with the links it generated.

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-07-25 21:06:32 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650560229  

Updating the problem...  
  
docca generates this via doxygen 1.9.7  
  
```  
assert( ! [link url.ref.boost__urls__grammar__ci_is_less `ci_is_less`]( "Boost" , "boost" ) );  
```  
  
and this via doxygen 1.8.15  
  
```  
assert( ! ci_is_less( "Boost", "boost" ) );  
```  
  
Maybe this has less to do with docca and doxygen 1.8.15 just doesn't enable auto-linking by default?

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-07-25 21:29:26 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650587545  

An automated preview of the documentation is available at [https://141.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://141.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2023-07-25 22:03:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/141#pullrequestreview-1546520400  

I think this is good to go as long as code blocks don't support links.  
  
I don't even like that pattern that much, but could you capitalize the commit messages to match the rest of the project and change the latest to commit to reflect it's no longer a workaround?  
  
Some of the messages are also not as descriptive now that we understand the cause of the issues. It could be something like:  
  
- Code blocks don't include links  
- Suppress private friends in ____  
- Support operator IDs with extra spaces  
  
(The last one is because the thing is already backward compatible... the commit is actually making it "forward" compatible with the new versions, so it's just supporting something else.)

📁 include/docca/base-stage2.xsl

```diff
  93 |+   <xsl:template match="codeline//ref"                  >[link {$doc-ref}.{@d:refid} {d:qb-escape(.)}]</xsl:template>
  94 |+   -->
  95 |+   <xsl:template match="codeline//ref"                  >{.}</xsl:template>
```

> Username: alandefreitas  
> Created_at: 2023-07-25 21:57:16 UTC  
> Updated_at: 2023-07-25 22:03:40 UTC  
> Url: https://github.com/boostorg/docca/pull/141#discussion_r1274159575  

If code blocks support links, it's undocumented and no one has ever used it. So this is not really a workaround and we don't need to keep the old code in comments.


---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-07-25 22:04:27 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650623613  

An automated preview of the documentation is available at [https://141.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://141.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2023-07-25 22:24:10 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650644060  

> I think this is good to go as long as code blocks don't support links.  
  
OK... So "``" fixes this. And we have a problem with private friends again. :neutral_face:

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2023-07-25 22:30:33 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650650236  

Also the problem with AUTOLINK_SUPPORT not being enforced seems to come from here:  
  
https://github.com/boostorg/docca/blob/8e2c71d2ea84f45016af02162a87790872e22a45/docca.jam#L44-L51

---

## Comment 9

> Username: sdkrystian  
> Created_at: 2023-07-25 23:28:52 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650701111  

> Also the problem with AUTOLINK_SUPPORT not being enforced seems to come from here:  
>   
> https://github.com/boostorg/docca/blob/8e2c71d2ea84f45016af02162a87790872e22a45/docca.jam#L44-L51  
  
Already tried setting `AUTOLINK_SUPPORT=OFF` there & it had no effect

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2023-07-25 23:36:09 UTC  
> Url: https://github.com/boostorg/docca/pull/141#issuecomment-1650706648  

> Already tried setting AUTOLINK_SUPPORT=OFF there & it had no effect  
  
Yes. This thing has many points of failure and each test takes a few minutes, which is annoying.

---
