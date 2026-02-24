# #367 - vector_indexing_suite should implement MutableSequence [Open]

> Username: Timmmm  
> Created at: 2021-07-06 14:07:33 UTC  
> Updated at: 2021-07-06 14:08:16 UTC  
> Url: https://github.com/boostorg/python/issues/367  

Currently `vector_indexing_suite` implements a few methods that you would expect from `list` but not all. It means I have to put this type in `my_cpp_modules.pyi`:  
  
```  
class vector(Iterable[T], Container[T], Sized):  
  # From indexing_suite  
  def __len__(self) -> int: ...  
  def __setitem__(self, index: int, value: T) -> None: ...  
  def __delitem__(self, index: int) -> None: ...  
  def __getitem__(self, index: int) -> T: ...  
  def __contains__(self, value: T) -> bool: ...  
  def __iter__(self) -> int: ...  
  # From vector_indexing_suite  
  def append(self, v: T) -> None: ...  
  def extend(self, v: Any) -> None: ...  
```  
  
Instead of deriving from `Iterable[T], Container[T], Sized` it would be much better if it were just a `MutableSequence[T]`. However that needs a few more methods (see the table at the top of [this page](https://docs.python.org/3/library/collections.abc.html)):  
  
Specifically:  
  
* `insert`  
* `reverse`  
* `pop`  
* `remove`  
* `__iadd__`  
  
Those methods should be added. It looks like it should be pretty easy.  
  
A similar issue may exist for `map_indexing_suite`/`MutableMapping` but I haven't used it yet.
