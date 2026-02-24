# #242 - Eliminate memory allocation in hot paths [Closed]

> Username: vinniefalco  
> Created at: 2022-07-08 15:49:20 UTC  
> Updated at: 2022-07-09 15:34:54 UTC  
> Closed at: 2022-07-09 15:34:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/242  

For programs that need to perform calculations using `path`, having these functions available could allow the program to prevent the need for dynamic allocations in hot paths:  
  
```  
void      path::reserve( size_type n );  
size_type path::capacity() const noexcept;  
void      path::clear() noexcept;  
```  
  
These would be simple one-line functions that call the corresponding `string_type` member function. It will be necessary to also audit the special member functions like assignment or appending, to make sure that they preserve the underlying string's capacity when it is possible to do so, instead of causing a reallocation. I did a cursory review and didn't see anything so it is likely that little if anything has to change.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-08 16:26:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/242#issuecomment-1179168215  

There already is a `path::clear` method.  
  
Technically, `filesystem::path` does not necessarily have to use a string as the underlying storage. I know Boost.Filesystem's path does, but e.g. libstdc++ doesn't, for example. Having `reserve` and `capacity` would not make sense in those implementations. So, while they would be trivial to add in Boost.Filesystem, I'd rather not to without a strong motivating use case, to allow for a possible future redesign, if one is deemed necessary.  
  
> It will be necessary to also audit the special member functions like assignment or appending, to make sure that they preserve the underlying string's capacity when it is possible to do so, instead of causing a reallocation.  
  
In general, this will be impossible to guarantee. Depending in the input, assignment may or may not reuse storage of the string used internally in `path`. For example, assignment from iterators first copies the input to a temporary string to ensure contiguity. Move assignment may cause transferring the storage from one string to another. There may be other cases.  
  
In general, I don't see the "no allocations" for `path` as a worthy goal to pursue. Memory allocations, if they happen, are likely much less overhead than the filesystem operations that you perform with the path.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-08 17:05:11 UTC  
> Updated at: 2022-07-08 17:14:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/242#issuecomment-1179198735  

> There already is a `path::clear` method.  
  
Ah, I did not see that at first. Thanks.  
  
> Technically, `filesystem::path` does not necessarily have to use  
> a string as the underlying storage. I know Boost.Filesystem's path  
> does, but e.g. libstdc++ doesn't, for example. Having `reserve` and  
> `capacity` would not make sense in those implementations. So, while  
> they would be trivial to add in Boost.Filesystem, I'd rather not to without  
> a strong motivating use case, to allow for a possible future redesign,  
> if one is deemed necessary.  
  
I think there is the opportunity to innovate beyond the standard here. The motivating use case is as described in the issue title. To eliminate the need for reallocation in the common case when an algorithm is composing a path.  
  
> In general, this will be impossible to guarantee....For example, assignment from iterators...  
  
Right, I'm not suggesting it for all cases just well-defined ones. For example there must be some way to invoke `append` with a caller-provided string view, which guarantees to use the path's existing capacity. Copy assignment from a path or string view should guarantee to use the path's existing capacity (in this case I think it does). For some of these cases, the only change would be to the documentation.  
  
> In general, I don't see the "no allocations" for `path` as a worthy goal to pursue.  
> Memory allocations, if they happen, are likely much less overhead than the  
> filesystem operations that you perform with the path.  
  
This is exactly the argument presented in the cpplang Slack when we discussed it this morning, and it is just as incorrect here as it was there.  
  
Consider this simple real-world use case: An HTTP server builds a `filesystem::path` by starting with a copy of the document root's `path`, and then appending each segment in the GET request target after applying percent-decoding to the path as a component (using `path::append`, or `operator/=`). Once this path is formed, the server checks a local cache to determine if it can elide the expensive filesystem operation. The cache uses this container:  
  
```  
std::unordered_map< filesystem::path, cached_attributes > cache;  
```  
  
The current API for `path` is defective for this use case, as it is impossible to avoid dynamic allocation even to perform a simple lookup. The problem with dynamic allocations in server programs is well known and should not require further elaboration other then to point out that the worst-case performance can be significantly expensive.  
  
The simplest solution here is to do something like  
  
```  
static thread_local work_path = []{  
  filesystem::path p;  
  p.reserve( 4096 );  
  return p; }();  
```  
  
and then use `work_path` to construct the key for lookup, being careful only to use the `path` APIs which guarantee the utilization of existing capacity (e.g. `append( string_view )` or maybe it is `append( Source )`.  
  
Here is a naive example program which uses Boost.URL in a fashion similar to the use-case describe above:  
  
https://github.com/CPPAlliance/url/blob/593c80c461d1c1535eb13f2531d1d169b50a7501/example/const_string/path.cpp#L46  
  
Every call to `append` ( `/=` in the code) has the potential to cause an allocate/copy/free cycle which kills performance. Furthermore there is the implicit construction of the `path` temporary on the right hand side. With the modest changes described in this issue, this can be fixed, and Boost can solve a major complaint of filesystem. I will note that there is an entire paper which goes through considerable lengths proposing `path_view`, to give users the capability to opt-out of dynamic allocation:  
  
https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1030r4.pdf  
  
This paper has already been accepted into the IS if I read correctly. I feel that my solution, which only adds two light member functions and some required guarantees on a limited set of member functions, fits the Boost ethos of pragmatic solutions that speak to the strengths of C++ (in this case the zero-overhead principle).

---

## Comment 3

> Username: Lastique  
> Created at: 2022-07-08 17:44:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/242#issuecomment-1179227792  

I do want to support string views as arguments to `path` methods, and I think it was requested before. But that is a separate work from guaranteeing storage capacity.  
  
I did think of a `path_view` class on my own. The main problem I see with it is character conversion. Since `path_view` doesn't own storage, it will have to be character type aware, which complicates interaction with it. Thanks for the link to the paper, I will have a look.  
  
Regarding storage capacity in `path`, I'm not convinced. If the path is string-based, like in Boost.Filesystem, you already get stable capacity as long as you don't destroy the `path` and don't cause storage replacement. That is, if you only `append` and `clear` the path, its storage remains stable in the long run. If the path is something else (e.g. a list of path elements) then there is no way to guarantee storage capacity. I remain of the opinion that the internal design of `path` should stay an implementation detail, so I can't guarantee that the current Boost.Filesystem behavior will remain as is in the future. But you may enjoy it while it is.  
  
My opinion is that if you want strict control over memory allocations, you should not be using standard containers, strings and `path`, since, to my knowledge, no container gives you guarantees on the allocations it makes. Sure, there is `reserve`, but that's not a strict instruction (it sets the lower bound of the capacity and doesn't shrink; it doesn't guarantee no other allocations are made).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-07-09 01:41:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/242#issuecomment-1179456187  

> you already get stable capacity as long as you don't destroy the path and don't cause storage replacement  
  
Good idea, I think I can work with this.
