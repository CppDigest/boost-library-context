# #75 - install_all/install_by does "nothing" [Closed]

> Username: Flamefire  
> Created at: 2022-04-17 10:00:16 UTC  
> Updated at: 2022-05-31 08:28:38 UTC  
> Closed at: 2022-05-31 08:28:38 UTC  
> Url: https://github.com/boostorg/locale/issues/75  

@artyom-beilis What exactly are the `install_all`/`install_by` structs supposed to do?  
  
They call `has_facet` on the classic locale at e.g. https://github.com/boostorg/locale/blob/9259845c6fd95ce6b8bf11e8ec5d8bdf38836c4e/src/shared/ids.cpp#L71   
  
However as it ignores the return value the compiler rightfully warns on such lines as they do nothing.  
  
What is the intended purpose of those `has_facet` calls? I'd like to at least rename them to reflect what they do. And "install" is not the right word as they do effectively nothing.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-04-17 11:14:06 UTC  
> Url: https://github.com/boostorg/locale/issues/75#issuecomment-1100855145  

It initializes special counters in locale::id() per facet. On some systems this initialization isn't thread safe and will fail in multi-threaded environment unless it is initialized once. Has facet uses the id and thus allows proper iniitalization.
