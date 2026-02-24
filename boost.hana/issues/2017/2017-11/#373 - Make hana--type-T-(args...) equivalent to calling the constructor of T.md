# #373 - Make hana::type<T>(args...) equivalent to calling the constructor of T [Closed]

> Username: ldionne  
> Created at: 2017-11-25 20:16:58 UTC  
> Updated at: 2017-11-25 20:17:12 UTC  
> Closed at: 2017-11-25 20:17:12 UTC  
> Url: https://github.com/boostorg/hana/issues/373  

This issue is created to track a feature that I once thought would be a good idea, but don't anymore (and won't implement). This is just for completeness.  
  
The idea is that `hana::type<T>(args...)` could create an object of type `T`, as if calling `T(args...)`. This would give a neat way of referring to the name of a constructor, which is normally impossible. It turns out I don't think this is a good idea, since one can use `hana::make<T>` instead.  
  
I'm attaching the patch I was working on so as to not lose it forever, even though this won't be implemented: [type-construct-patch.txt](https://github.com/boostorg/hana/files/1503355/type-construct-patch.txt)
