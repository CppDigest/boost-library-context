# #133 - Tables? [Closed]

> Username: vinniefalco  
> Created at: 2022-09-01 04:57:15 UTC  
> Updated at: 2022-09-01 22:54:21 UTC  
> Closed at: 2022-09-01 22:54:21 UTC  
> Url: https://github.com/boostorg/docca/issues/133  

Doxygen supports tables:  
```  
    /** Table example  
  
        First Header  | Second Header  
        ------------- | -------------  
        Content Cell  | Content Cell  
        Content Cell  | Content Cell  
    */  
```  
  
do we get the information in the XML? I would like to explore this, if it is practical to do so.

---

## Comment 1

> Username: evanlenz  
> Created at: 2022-09-01 20:33:13 UTC  
> Url: https://github.com/boostorg/docca/issues/133#issuecomment-1234750777  

Looks like we get basic information but unfortunately XML is a second-class citizen again here, so colspan, rowspan, and column alignments are not supported. That said, we do get rows and columns and the distinction between a header and non-header cell. I should be able to implement those at least, so the example you posted will work.
