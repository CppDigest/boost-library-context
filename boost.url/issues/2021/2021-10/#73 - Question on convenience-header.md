# #73 - Question on convenience-header [Closed]

> Username: vinniefalco  
> Created at: 2021-10-31 19:27:34 UTC  
> Updated at: 2021-10-31 19:27:49 UTC  
> Closed at: 2021-10-31 19:27:49 UTC  
> Url: https://github.com/boostorg/url/issues/73  

How would I accomplish something like this?  
```  
  <xsl:template mode="convenience-header" match="@file[contains(., 'boost/url/bnf')]">url/bnf.hpp</xsl:template>  
  <xsl:template mode="convenience-header" match="@file[contains(., 'boost/url')]">url.hpp</xsl:template>  
  <xsl:template mode="convenience-header" match="@file"/>  
```  
  
I am getting "Ambiguous rule match." I want everything in url/bnf to use the bnf.hpp file, and everything else to use url.hpp.
