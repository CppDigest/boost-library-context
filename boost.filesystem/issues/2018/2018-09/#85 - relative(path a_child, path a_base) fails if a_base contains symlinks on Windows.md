# #85 - relative(path a_child, path a_base) fails if a_base contains symlinks on Windows [Closed]

> Username: IncineratorBurns  
> Created at: 2018-09-20 20:51:44 UTC  
> Updated at: 2020-05-02 17:20:01 UTC  
> Closed at: 2020-05-02 17:20:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/85  

relative() calls weakly_canonical() passing a_base as argument, which in turn calls canonical(), which returns an empty path, and then relative() throws.  
The message reads:   
boost::filesystem::relative: The system cannot find the file specified: "T:\src\\_wxWidgets_\\interface\wx"  
  
This _wxWidgets_ is a symlink to _wxWidgets-3.1.0_.  
If instead of "T:\src\wxWidgets\interface\wx" I call relative() with "T:\src\wxWidgets-3.1.0\interface\wx", the function succeeds.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-02 17:20:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/85#issuecomment-622986401  

Probably fixed by https://github.com/boostorg/filesystem/pull/100.
