# #320 - Allow pattern-matching on hana::type [Open]

> Username: ldionne  
> Created at: 2017-02-12 23:11:28 UTC  
> Updated at: 2017-02-12 23:12:07 UTC  
> Url: https://github.com/boostorg/hana/issues/320  

Related to https://github.com/boostorg/hana/issues/319. Instead of `hana::type` being twisted, we should make `hana::type` equivalent to `hana::basic_type`, and then provide something like`hana::protect` to work around obscure ADL issues.  
  
Will wait for breaking changes.
