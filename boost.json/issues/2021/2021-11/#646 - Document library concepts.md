# #646 - Document library concepts [Open]

> Username: grisumbras  
> Created at: 2021-11-03 12:54:37 UTC  
> Updated at: 2021-11-03 16:38:15 UTC  
> Url: https://github.com/boostorg/json/issues/646  

Related: https://github.com/boostorg/docca/pull/108

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-11-03 13:16:07 UTC  
> Url: https://github.com/boostorg/json/issues/646#issuecomment-959048493  

As far as I can see, if  
```XSLT  
<xsl:variable name="emphasized-template-parameter-types" select="  
    'Foo',  
"/>  
```  
is added to `custom-overrides.xsl`, any occurrence of `class Foo` in API docs will transform into a Quickbook macro `__Foo__`. So, if such macro is defined to expand into a link, resulting reference sections will link to concepts.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-11-03 16:38:15 UTC  
> Url: https://github.com/boostorg/json/issues/646#issuecomment-959683403  

Yes that's how it works
