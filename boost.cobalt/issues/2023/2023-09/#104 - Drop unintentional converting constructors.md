# #104 - Drop unintentional converting constructors [Closed]

> Username: akrzemi1  
> Created at: 2023-09-02 17:12:56 UTC  
> Updated at: 2023-09-04 07:32:29 UTC  
> Closed at: 2023-09-04 07:32:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/104  

Constructors callable with a single argument (when other arguments have default function arguments) should be declared `explicit` to avoid unintentional conversions. This applies to `channel` and `wait_group`.
