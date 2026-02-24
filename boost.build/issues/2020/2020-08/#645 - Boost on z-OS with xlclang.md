# #645 - Boost on z/OS with xlclang [Closed]

> Username: GregIzos  
> Created at: 2020-08-18 12:28:17 UTC  
> Updated at: 2020-09-12 21:01:34 UTC  
> Closed at: 2020-09-12 21:01:34 UTC  
> Url: https://github.com/boostorg/build/issues/645  

I am trying to port boost to z/OS.  Have have altered the scripts so they are running and the code will compile with no errors.  
Problem is when I execute  ./bootstrap.sh xlclang   Attached is the bootstrap.log  
  
The compile fails. Why?, I do not know. It is being run from a program jam0, which was just created.  
If I take that compile line   
 "xlclang"   -o bin.mvs390/b2 "-DNDEBUG" "-DOPT_HEADER_CACHE_EXT".......  
and put it in a script by it self it works just fine.  
Creates b2 executable in bin.mvs390  
  
Question1: Any idea why compile link is failing when executed in the program?  
  
Question 2: I am able to create b2 manually in folder ./boost/tools/build/src/engine/ bin.mvs390.  
Any Idea what I should be trying to run for   
skipped bjam for lack of b2...  ?  
(Maybe I can run the rest manually?)  
  
Thanks for any help.  
  
  
  
  
[bootstrap.log](https://github.com/boostorg/website/files/5090176/bootstrap.log)

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-12 21:01:34 UTC  
> Url: https://github.com/boostorg/build/issues/645#issuecomment-691547643  

Please try with a current version of B2.
