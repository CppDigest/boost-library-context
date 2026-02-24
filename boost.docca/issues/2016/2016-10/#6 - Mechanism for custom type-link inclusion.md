# #6 - Mechanism for custom type-link inclusion [Open]

> Username: mellery451  
> Created at: 2016-10-10 23:44:59 UTC  
> Updated at: 2016-10-10 23:44:59 UTC  
> Url: https://github.com/boostorg/docca/issues/6  

We need a way for projects using docca to provide information about specific types in the project that have associated docs. The project authors should be able to provide an input or inclusion file that provides links we want to add for specified custom types. Docca currently has some hardcoded type-links [here](https://github.com/vinniefalco/docca/blob/master/include/docca/doxygen.xsl#L1572), but these types need to be moved out and the mechanism needs to be made generic.
