# #506 - Unused json doc customizations [Closed]

> Username: evanlenz  
> Created at: 2021-02-25 21:05:01 UTC  
> Updated at: 2021-02-25 21:08:41 UTC  
> Closed at: 2021-02-25 21:08:41 UTC  
> Url: https://github.com/boostorg/json/issues/506  

We recently deleted the following code that had been unused. Do we need to introduce any of the originally intended behavior behind this code?  
  
```  
<!-- CLASS_DETAIL_TEMPLATE BEGIN -->  
<xsl:when test="$normal-tparam = 'Allocator'"><xsl:text>__Allocator__</xsl:text></xsl:when>  
<xsl:when test="$normal-tparam = 'InputIterator'"><xsl:text>__InputIterator__</xsl:text></xsl:when>  
<xsl:when test="$normal-tparam = 'ConstBufferSequence'"><xsl:text>__ConstBufferSequence__</xsl:text></xsl:when>  
<xsl:when test="$normal-tparam = 'MutableBufferSequence'"><xsl:text>__MutableBufferSequence__</xsl:text></xsl:when>  
<!-- CLASS_DETAIL_TEMPLATE END -->  
```

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-02-25 21:08:41 UTC  
> Url: https://github.com/boostorg/json/issues/506#issuecomment-786203977  

You know what, this is using the old docca code's mechanism, so it's quite obsolete, and since these are just reformatting the same names, there's no real knowledge to retain here. Nothing to see here...
