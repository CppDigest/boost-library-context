# #171 - Add equality operators to all wrapper classes [Closed]

> Username: kylelutz  
> Created at: 2014-07-03 18:46:40 UTC  
> Updated at: 2014-07-30 01:50:11 UTC  
> Closed at: 2014-07-30 01:50:11 UTC  
> Url: https://github.com/boostorg/compute/issues/171  

Add `operator==` and `operator!=` to all wrapper classes. Currently some classes (like `context`) have the equality operators implemented and others (like `device`) do not. Objects of the wrapper classes should compare equal if their underlying OpenCL objects are the same.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-30 01:50:11 UTC  
> Url: https://github.com/boostorg/compute/issues/171#issuecomment-50564538  

Implemented in PR #178
