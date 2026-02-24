# #18 - Unused static_string doc customizations [Closed]

> Username: evanlenz  
> Created at: 2021-02-25 21:02:27 UTC  
> Updated at: 2021-02-25 21:08:23 UTC  
> Closed at: 2021-02-25 21:08:23 UTC  
> Url: https://github.com/boostorg/static_string/issues/18  

We recently deleted the following code that had been unused. Do we need to introduce any of the originally intended behavior behind this code? (I'm guessing the second one below can be left out. 🥚)  
  
```  
<!-- CLASS_DETAIL_TEMPLATE BEGIN -->  
<xsl:when test="$normal-tparam = 'InputIterator'"><xsl:text>__InputIterator__</xsl:text></xsl:when>  
<!-- CLASS_DETAIL_TEMPLATE END -->  
```  
  
```  
<!-- INCLUDES_FOOT_TEMPLATE BEGIN -->  
  <xsl:choose>  
    <xsl:when test="contains($file, 'supercalifragilisticexpialidocious')">  
      <xsl:text>&#xd;&#xd;Easter Egg&#xd;</xsl:text>  
    </xsl:when>  
  </xsl:choose>  
<!-- INCLUDES_FOOT_TEMPLATE END -->  
```

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-02-25 21:08:23 UTC  
> Url: https://github.com/boostorg/static_string/issues/18#issuecomment-786203832  

You know what, this is using the old docca code's mechanism, so it's quite obsolete, and since this is just reformatting the same name (InputIterator), there's no real knowledge to retain here. Sorry for the noise.
