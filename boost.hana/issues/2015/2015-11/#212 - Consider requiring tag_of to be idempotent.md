# #212 - [tag_of] Consider requiring tag_of to be idempotent [Closed]

> Username: ldionne  
> Created at: 2015-11-20 16:41:06 UTC  
> Updated at: 2015-11-22 04:47:44 UTC  
> Closed at: 2015-11-22 04:47:44 UTC  
> Url: https://github.com/boostorg/hana/issues/212  

Right now, we pretty much rely on the fact that `hana::tag_of<hana::tag_of<T>::type>::type` is just `hana::tag_of<T>::type`. In other words, we take for granted that `hana::tag_of` is idempotent. If that's a requirement we want to keep, we should document it properly.
