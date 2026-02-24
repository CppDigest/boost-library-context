# #93 - High-ascii char intentional? [Open]

> Username: vinniefalco  
> Created at: 2021-09-22 14:28:23 UTC  
> Updated at: 2021-09-22 14:28:23 UTC  
> Url: https://github.com/boostorg/docca/issues/93  

It looks like when the xslt emits silver text there is a stray high-ascii character emitted:  
![image](https://user-images.githubusercontent.com/1503976/134362608-42152f64-1191-4941-9da4-9ab95e52a5c6.png)  
  
Note that `<0xa0>` is not literal text it is actually the character hex A0 (decimal 160, or non-breaking space). Is this intentional?
