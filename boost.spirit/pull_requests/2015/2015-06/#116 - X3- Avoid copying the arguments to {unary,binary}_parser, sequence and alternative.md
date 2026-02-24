# #116 X3: Avoid copying the arguments to {unary,binary}_parser, sequence and alternative. [Merged]

> Username: mlang  
> Created at: 2015-06-13 17:35:18 UTC  
> Updated at: 2015-06-14 20:48:35 UTC  
> Merged at: 2015-06-13 22:20:00 UTC  
> Closed at: 2015-06-13 22:20:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/116  

For consistency with the rest.  Apparently, this brings the executable size of  
an X3 grammar of mine down from 440936 to 420000.  It also reduces compilation  
time a bit, which was rather unexpected (18.3s to 17.8s).

---
