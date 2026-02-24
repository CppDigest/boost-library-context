# #13 - warning: saved_conditional may be used uninitialized [Open]

> Username: alandefreitas  
> Created at: 2022-03-25 18:54:44 UTC  
> Updated at: 2022-03-25 18:54:44 UTC  
> Url: https://github.com/boostorg/quickbook/issues/13  

We get this warning every time we build the docs:  
  
```  
    inlined from ‘void quickbook::quickbook_grammar::impl::init_phrase_elements()’ at /home/alandefreitas/Documents/Code/C++/boost/tools/quickbook/src/phrase_element_grammar.cpp:41:65:  
/home/alandefreitas/Documents/Code/C++/boost/tools/quickbook/src/actions.hpp:184:12: warning: ‘<unnamed>.quickbook::cond_phrase_push::saved_conditional’ may be used uninitialized [-Wmaybe-uninitialized]  
  184 |     struct cond_phrase_push : scoped_action_base  
      |            ^~~~~~~~~~~~~~~~  
```
