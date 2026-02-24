# #149 - Only inherit when the class is empty [Open]

> Username: pfultz2  
> Created at: 2016-03-15 20:14:25 UTC  
> Updated at: 2016-03-21 20:20:58 UTC  
> Url: https://github.com/boostorg/hof/issues/149  

The main reason for inheritance is to take advantage of EBO. So it might be best to only inherit when classes are empty. This is related to issue #129, and may supersede it.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-21 20:20:58 UTC  
> Url: https://github.com/boostorg/hof/issues/149#issuecomment-199459692  

The nice thing about inheritance is easy access to the base class. I think it would be better to keep inheritance and the user can use `final` or `fit::protect` to avoid possible problems that come from inheritance, but I am not sure which would be the better approach.
