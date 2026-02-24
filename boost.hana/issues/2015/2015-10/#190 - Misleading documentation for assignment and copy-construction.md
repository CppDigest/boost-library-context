# #190 - [Optional] Misleading documentation for assignment and copy-construction [Closed]

> Username: ldionne  
> Created at: 2015-10-18 15:08:34 UTC  
> Updated at: 2015-11-27 23:56:40 UTC  
> Closed at: 2015-11-27 23:56:40 UTC  
> Url: https://github.com/boostorg/hana/issues/190  

The current documentation of `optional` contains `operator=` and copy constructors. Of course, these operations only make sense between similarly-empty `optional`s, because the emptiness plays in the type of the `optional`. In other words, only an empty `optional` may be assigned to an empty `optional`, and only a non-empty `optional` may be assigned to a non-empty `optional`. There is an analogous limitation for copy-constructors. The documentation currently does not show this.
