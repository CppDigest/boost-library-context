# #130 Perform 'see-below', etc. replacements in @code blocks [Closed]

> Username: evanlenz  
> Created at: 2022-08-06 00:14:26 UTC  
> Updated at: 2023-01-31 06:31:29 UTC  
> Closed at: 2022-08-06 13:14:34 UTC  
> Url: https://github.com/boostorg/docca/pull/130  

Fix #129

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-06 00:17:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/130#pullrequestreview-1064240921  

📁 include/docca/base-stage2.xsl

```diff
 260 |+     <xsl:sequence select="d:perform-replacements(string(.), $qb-type-replacements)"/>
 261 |+   </xsl:template>
 262 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-08-06 00:17:57 UTC  
> Updated_at: 2022-08-06 00:17:58 UTC  
> Url: https://github.com/boostorg/docca/pull/130#discussion_r939454589  

That's amazing! !!!


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-06 00:24:11 UTC  
> Url: https://github.com/boostorg/docca/pull/130#issuecomment-1207102945  

An automated preview of the documentation is available at [https://130.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://130.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---
