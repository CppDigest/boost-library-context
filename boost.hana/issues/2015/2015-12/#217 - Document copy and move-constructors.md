# #217 - [Set/Map] Document copy and move-constructors [Closed]

> Username: ldionne  
> Created at: 2015-12-01 21:13:59 UTC  
> Updated at: 2015-12-04 18:56:56 UTC  
> Closed at: 2015-12-04 18:56:56 UTC  
> Url: https://github.com/boostorg/hana/issues/217  

While the canonical way to create a `hana::map` and a `hana::set` is to use the `hana::make_xxx` functions, it should still be possible to copy/move them around when the elements in the container support it. Right now, the documentation states that no constructors whatsoever are provided, which is false.  
  
Also, it's quite important to have copy/move constructors because otherwise we can't even return these containers by value. The documentation needs to be adjusted to account for this.
