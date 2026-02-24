# #27 - Support initializer_list [Closed]

> Username: KindDragon  
> Created at: 2017-04-28 15:35:23 UTC  
> Updated at: 2017-07-25 15:27:14 UTC  
> Closed at: 2017-07-25 15:27:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/27  

To do something like this:  
```  
argagg::parser argparser {{  
    { "help", {"-h", "--help"},  
      "shows this help message", 0},  
    { "delim", {"-d", "--delim"},  
      "delimiter (default: ,)", 1},  
    { "num", {"-n", "--num"},  
      "number", 1},  
  }};  
```

---

## Comment 1

> Username: vprus  
> Created at: 2017-07-24 19:41:57 UTC  
> Url: https://github.com/boostorg/program_options/issues/27#issuecomment-317532564  

Thanks for the suggestion. It might be a good idea, but it is feature, not an issue, and would require a fair deal of design. Therefore, I'm gonna close the issue.

---

## Comment 2

> Username: KindDragon  
> Created at: 2017-07-24 22:42:59 UTC  
> Url: https://github.com/boostorg/program_options/issues/27#issuecomment-317575885  

Ok, as you wish
