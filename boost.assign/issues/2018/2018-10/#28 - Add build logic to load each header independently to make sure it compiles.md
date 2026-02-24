# #28 - Add build logic to load each header independently to make sure it compiles [Closed]

> Username: jeking3  
> Created at: 2018-10-15 17:50:05 UTC  
> Updated at: 2018-10-16 00:16:46 UTC  
> Closed at: 2018-10-16 00:16:46 UTC  
> Url: https://github.com/boostorg/assign/issues/28  

There is an example in the uuid test Jamfile one can follow.  This will ensure each header can be included independently and each includes everything it requires.  This is based on regressions in #6 that were not detected by the current build process.
