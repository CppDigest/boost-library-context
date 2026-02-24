# #68 - zero padding does not work in combination with plus sign [Open]

> Username: maddanio  
> Created at: 2019-08-13 12:03:15 UTC  
> Updated at: 2019-08-13 12:03:37 UTC  
> Url: https://github.com/boostorg/format/issues/68  

i.e. %07d will result in padding to 7 digits with zeros and %+d will show unpadded digits with plus sign if positive, but %+07d only shows the plus sign (if positive) but does not zero pad
