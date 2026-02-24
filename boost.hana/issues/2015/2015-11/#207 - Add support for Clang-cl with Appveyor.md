# #207 - [CI] Add support for Clang-cl with Appveyor [Closed]

> Username: ldionne  
> Created at: 2015-11-15 16:25:37 UTC  
> Updated at: 2015-11-25 22:11:19 UTC  
> Closed at: 2015-11-25 22:11:19 UTC  
> Url: https://github.com/boostorg/hana/issues/207  

This issue is extracted from #201. Technically, we're able to provide support for Clang-cl, but right now we have no CI testing it. This makes the support brittle, since we're basically relying solely on individual input from @m-j-w. It would be better to have this compiler tested everything we change something.  
  
In order to implement this, we likely need https://github.com/appveyor/ci/issues/495 to be resolved.
