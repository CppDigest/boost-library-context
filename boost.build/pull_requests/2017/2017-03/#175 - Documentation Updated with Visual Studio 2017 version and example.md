# #175 Documentation Updated with Visual Studio 2017 version and example [Merged]

> Username: teeks99  
> Created at: 2017-03-13 21:07:46 UTC  
> Updated at: 2021-10-02 22:18:49 UTC  
> Merged at: 2017-03-15 17:09:28 UTC  
> Closed at: 2017-03-15 17:09:28 UTC  
> Url: https://github.com/boostorg/build/pull/175  

This updates the toolset documentation to include the visual studio 2017 version as well as  
a basic msvc usage example.   
  
I think I've put it in correctly, but I don't have the toolchain to actually build the docs and see what they look like. Can someone who does have the toolchain try that before this is merged?

---

## Review 1 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-14 02:18:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/175#pullrequestreview-26711265  

📁 doc/src/reference.xml

```diff
1038 |+           example to build with Visual Studio 2017 one could run:
1039 |+           <programlisting>
1040 |+ .\b2 toolset=msvc-14.1 target
```

> Username: KindDragon  
> Created_at: 2017-03-14 02:18:47 UTC  
> Updated_at: 2017-03-15 11:59:19 UTC  
> Url: https://github.com/boostorg/build/pull/175#discussion_r105814793  

I think it should be msvc-14.10


---
