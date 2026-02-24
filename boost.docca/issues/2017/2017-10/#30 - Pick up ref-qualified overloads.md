# #30 - Pick up ref-qualified overloads [Open]

> Username: vinniefalco  
> Created at: 2017-10-30 22:24:21 UTC  
> Updated at: 2017-10-30 22:24:21 UTC  
> Url: https://github.com/boostorg/docca/issues/30  

For example `value_type body()&&` or `value_type body() const&&`. The ref qualifier is not picked up in the documentation, it might be in doxygen's generated xsl but not extracted by the XSLT.
