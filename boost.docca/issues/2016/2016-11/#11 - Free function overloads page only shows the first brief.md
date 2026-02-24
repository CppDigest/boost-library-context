# #11 - Free function overloads page only shows the first brief [Open]

> Username: vinniefalco  
> Created at: 2016-11-07 15:46:16 UTC  
> Updated at: 2016-11-07 18:29:48 UTC  
> Url: https://github.com/boostorg/docca/issues/11  

Given free function declarations:  
```  
/// X  
int& foo();  
  
/// Y  
int const& foo();  
```  
  
The overloads section will show only `X`.  
  
However, if these functions are class member functions, the overloads page correctly shows the different briefs.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-07 18:29:48 UTC  
> Url: https://github.com/boostorg/docca/issues/11#issuecomment-258920106  

This snippet in the right spot should fix it  
  
``````  
      <xsl:if test="position() &gt; 1">  
        <xsl:text>&#xd;</xsl:text>  
        <xsl:if test=" not(briefdescription = preceding-sibling::*/briefdescription)">  
          <xsl:text>```&#xd;</xsl:text>  
          <xsl:apply-templates select="briefdescription" mode="markup"/>  
          <xsl:text>```&#xd;</xsl:text>  
        </xsl:if>  
      </xsl:if>  
``````  
  
Beast has a fix in the works
