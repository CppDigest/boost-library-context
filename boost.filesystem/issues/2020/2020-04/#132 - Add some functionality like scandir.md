# #132 - Add some functionality like scandir [Closed]

> Username: soroshsabz  
> Created at: 2020-04-11 12:29:20 UTC  
> Updated at: 2020-04-11 14:23:33 UTC  
> Closed at: 2020-04-11 13:06:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/132  

ITNOA  
  
Hi,  
  
Many times we need to scanning directory instead of iterating of directories to see all directory with specific order like alphabetically or based on modified time or …  
  
So I think it is useful feature to boost::filesystem provide scan directories  
  
thanks for great library

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-11 13:06:02 UTC  
> Updated at: 2020-04-11 14:23:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/132#issuecomment-612417130  

I think this is better implemented on top of `directory_iterator`. `scandir` exists because C has rather limited standard library, in C++ you can simply do something like this:  
  
```  
std::set<path> paths;  
std::copy_if(directory_iterator(dir), directory_iterator(), std::inserter(paths), filter);  
```

---

## Comment 2

> Username: soroshsabz  
> Created at: 2020-04-11 13:28:16 UTC  
> Updated at: 2020-04-11 13:28:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/132#issuecomment-612421358  

@Lastique  Thanks for response, I think the main difference is performance, because in iterating, boost use readdir in internal implementation, and I think scandir is faster than readdir step by step for scanning usage.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-04-11 14:22:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/132#issuecomment-612432362  

`scandir` is also most likely using `readdir` or the underlying syscall internally. See it in [glibc](https://sourceware.org/git/?p=glibc.git;a=blob;f=dirent/scandir-tail-common.c;h=65853842c1f212376cfcb729c0c383935a0fbedb;hb=refs/heads/master) for instance. I would say, using C++ tools for this has better potential performance because of more aggressive inlining, although the raw code performance is probably offset by `readdir` cost.
