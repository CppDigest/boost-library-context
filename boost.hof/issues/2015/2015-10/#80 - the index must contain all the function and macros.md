# #80 - the index must contain all the function and macros [Closed]

> Username: viboes  
> Created at: 2015-10-05 23:50:28 UTC  
> Updated at: 2017-01-31 21:36:55 UTC  
> Closed at: 2017-01-31 21:36:55 UTC  
> Url: https://github.com/boostorg/hof/issues/80  

Some times you group several function in one page with a single index entry. I can understand that there is only one page for several functions but the index must contain all the entries.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-06 20:30:58 UTC  
> Url: https://github.com/boostorg/hof/issues/80#issuecomment-145991005  

There is no index, and there is no simple tool to generate an index in C++. Each entry is per header file. That is why some entries are grouped together.

---

## Comment 2

> Username: viboes  
> Created at: 2016-03-01 00:48:07 UTC  
> Url: https://github.com/boostorg/hof/issues/80#issuecomment-190472474  

I'm not talking of generating a index. I'm talking about having access to all the functions of the library.  
  
If the an entry is corresponds to a file this must be stated clearly on how to read this documentation.  
  
Or maybe it is better to add in the entries the file name.

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-03-01 01:35:25 UTC  
> Url: https://github.com/boostorg/hof/issues/80#issuecomment-190483784  

I may be improving this to allow for a further detailed table of contents. This is pending a pull request from mkdocs [here](https://github.com/mkdocs/mkdocs/pull/851).

---

## Comment 4

> Username: pfultz2  
> Created at: 2017-01-31 21:36:55 UTC  
> Url: https://github.com/boostorg/hof/issues/80#issuecomment-276500449  

So now almost everything is indexed now with more detail.
