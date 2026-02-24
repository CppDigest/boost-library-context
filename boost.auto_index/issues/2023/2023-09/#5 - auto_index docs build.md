# #5 - auto_index docs build [Closed]

> Username: sdarwin  
> Created at: 2023-09-14 14:14:34 UTC  
> Updated at: 2023-09-16 15:58:36 UTC  
> Closed at: 2023-09-16 15:58:36 UTC  
> Url: https://github.com/boostorg/auto_index/issues/5  

A standard way to build documentation for auto_index is something like this:  
```  
./b2 tools/auto_index/doc  
```  
  
That method works for most libraries.    
  
With auto_index it fails?  
  
```  
Could not open script file: autoindex.idx  
```  
  
autoindex.idx is present in `doc/`.  
  
A work-around is copying autoindex.idx to the current directory.  
  
```  
cp tools/auto_index/doc/autoindex.idx .  
```  
Could the Jamfile specify the path to autoindex.idx so that it's always discoverable? Or else copy autoindex.idx to the current directory.
