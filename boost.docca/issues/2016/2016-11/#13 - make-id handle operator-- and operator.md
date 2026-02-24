# #13 - make-id handle operator<< and operator>> [Closed]

> Username: vinniefalco  
> Created at: 2016-11-07 18:10:13 UTC  
> Updated at: 2020-12-30 05:58:48 UTC  
> Closed at: 2020-12-30 05:58:47 UTC  
> Url: https://github.com/boostorg/docca/issues/13  

Simple fix, just add:  
```  
    <xsl:when test="substring($name, string-length($name) - 1) = '&lt;&lt;'">  
      <xsl:call-template name="make-id">  
        <xsl:with-param name="name"  
         select="concat(substring-before($name, '&lt;&lt;'), '_ls_')"/>  
      </xsl:call-template>  
    </xsl:when>  
    <xsl:when test="substring($name, string-length($name) - 1) = '&gt;&gt;'">  
      <xsl:call-template name="make-id">  
        <xsl:with-param name="name"  
         select="concat(substring-before($name, '&gt;&gt;'), '_rs_')"/>  
      </xsl:call-template>  
    </xsl:when>  
```

---

## Comment 1

> Username: evanlenz  
> Created at: 2020-12-30 05:12:22 UTC  
> Url: https://github.com/boostorg/docca/issues/13#issuecomment-752330069  

Is this issue still a requirement? If I understand correctly, it would mean that the output URI for, for example, this page:  
  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__operator_lt__lt_.html  
  
Would instead look like this:  
  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__operator_ls_.html  
  
Other than that, it wouldn't produce any changes visible to the user. @vinniefalco, let me know if you'd like me to make that change to the latest code. (It would of course use the new code's mechanism but produce the same result as your fix above to the old code would have produced).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-12-30 05:58:47 UTC  
> Url: https://github.com/boostorg/docca/issues/13#issuecomment-752338559  

I thought it was broken, but it looks like it works? We can close this.
