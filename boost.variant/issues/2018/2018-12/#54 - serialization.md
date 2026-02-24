# #54 - serialization [Closed]

> Username: robertramey  
> Created at: 2018-12-18 17:44:18 UTC  
> Updated at: 2020-12-16 08:12:37 UTC  
> Closed at: 2020-12-16 08:12:37 UTC  
> Url: https://github.com/boostorg/variant/issues/54  

The boost serialization library includes an implementation for boost/variant.  This breaks the general rule that serialization of boost components be implemented as part of that component and that only the boost serialization only contain implementations of serialization for std components.  Now that there is a std::variant as part of C++17 we want to add another variant implementation to serialization.  But on order to do that we want to move the current implementation here  in order to "make room".  I need to know if the maintainers of boost::variant are OK with this and will be willing to merge the PR. My inclination is to do this via a fork/pull request.  Please let me know if this is all OK.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-27 19:36:29 UTC  
> Url: https://github.com/boostorg/variant/issues/54#issuecomment-477317209  

I would not recommend doing this. [BCP](https://www.boost.org/doc/libs/1_69_0/tools/bcp/doc/html/index.html) and [boostdep](https://github.com/boostorg/boostdep) tools are scanning every headers in the library and treating every include in them as a dependency. For example optional serialization header in [date_time](https://pdimov.github.io/boostdep-report/master/date_time.html) BCPing a huge part of Boost, or again because of it Serialization and Spirit are considered circular dependencies.

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-12-15 15:16:39 UTC  
> Url: https://github.com/boostorg/variant/issues/54#issuecomment-745358410  

@robertramey is it possible to avoid inclusion of any Boost.Serialization headers to support serialization of variant?  
  
In other words, it's doable iff https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/variant.hpp included nothing from boost/serialization or boost/archive

---

## Comment 3

> Username: robertramey  
> Created at: 2020-12-15 16:21:04 UTC  
> Url: https://github.com/boostorg/variant/issues/54#issuecomment-745400468  

I'm not really understanding the question.  Inclusion of all boost serialization headers are optional.  They are only required when one includes any class from boost::archive.   In other words, the code from serialization headers is header only and and only required if instantiated and it's instantiated only when actually used.  I don't know if that answers your question, but there it is.  
  
Note that it's helpful to remember that the boost serialization library is really two namespaces: serialization (for serializable types) and archive (for functions which actually do the serialization).   If one forgets about this, things can get confusing pretty fast.  Not to mention that it's easy to introduce gratuitous dependencies.  There are still a few of these in the library so de-coupling might still be incomplete (RTTI is an issue here).

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-12-16 08:12:37 UTC  
> Url: https://github.com/boostorg/variant/issues/54#issuecomment-745886688  

Main reason for not moving the serialization is described by @Kojoley :  
>  [BCP](https://www.boost.org/doc/libs/1_69_0/tools/bcp/doc/html/index.html) and [boostdep](https://github.com/boostorg/boostdep) tools are scanning every headers in the library and treating every include in them as a dependency.   
  
Another reason is preprocessing times. Tools like `ccache` use preprocessing to detect changes. Inclusion of big headers (or just many headers) increase preprocessing times and slow down the compilation. For example inclusion of Boost.Iostreams slows down the preprocessing on 250ms for each translation unit. Slow preprocessing affects compile times even without ccache. Serialization library does not seem lightweight.  
  
Sorry, this issue is a "won't fix" issue
