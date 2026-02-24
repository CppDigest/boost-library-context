# #45 - Add replace method to utf-to-utf [Open]

> Username: Flamefire  
> Created at: 2019-11-28 16:24:29 UTC  
> Updated at: 2019-11-28 16:24:29 UTC  
> Url: https://github.com/boostorg/locale/issues/45  

During conversion between UTF encodings it might be desirable to replace invalid characters by a replacement character, e.g. `0xFFFD`. But the current methods only allow to skip or abort.
