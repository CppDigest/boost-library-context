# #395 - Please add variant=coverage [Closed]

> Username: HDembinski  
> Created at: 2019-02-20 08:58:17 UTC  
> Updated at: 2019-02-20 23:04:49 UTC  
> Closed at: 2019-02-20 15:10:30 UTC  
> Url: https://github.com/boostorg/build/issues/395  

Several libraries set the --coverage flags manually in their CI scripts:  
beast, histogram, type_index, process, lexical_cast, stacktrace, geometry, dll, variant, conversion, any, test.  
  
beast introduced locally a `variant coverage` rule (which I copied and this caused the global builds to break, but that's a different story), which seems generally useful to introduce top-level in `builtin.jam`.  
  
```  
variant coverage  
  : debug  
  : <cxxflags>"--coverage"  
    <linkflags>"--coverage"  
  ;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-20 14:59:40 UTC  
> Url: https://github.com/boostorg/build/issues/395#issuecomment-465612851  

Seems benign, but I'm not a bjam expert.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2019-02-20 15:10:30 UTC  
> Url: https://github.com/boostorg/build/issues/395#issuecomment-465617652  

It's not a good idea to put toolset specific flags in a `variant`. As they are meant to be aggregations / aliases for a collection of common b2 features. And for built-in ones we *only* and *exclusively* have them be composed of features: https://github.com/boostorg/build/blob/develop/src/tools/builtin.jam#L48  
  
We would have to create built-in features for coverage, and related, aspects. Which would then be implemented/specified in the individual toolsets. And then finally add built-in global variants that turn on/off those coverage features.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2019-02-20 15:11:36 UTC  
> Url: https://github.com/boostorg/build/issues/395#issuecomment-465618182  

PS. Please reopen when those prerequisites are met and this one redone accordingly.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-02-20 15:15:26 UTC  
> Url: https://github.com/boostorg/build/issues/395#issuecomment-465619901  

Looks like we need `<coverage>on`.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-02-20 23:04:49 UTC  
> Url: https://github.com/boostorg/build/issues/395#issuecomment-465791604  

I added another issue for the prerequisite.
