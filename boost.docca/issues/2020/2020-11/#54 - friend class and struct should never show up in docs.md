# #54 - friend class and struct should never show up in docs [Closed]

> Username: vinniefalco  
> Created at: 2020-11-10 00:26:54 UTC  
> Updated at: 2020-11-17 19:46:45 UTC  
> Closed at: 2020-11-17 19:46:45 UTC  
> Url: https://github.com/boostorg/docca/issues/54  

A class or struct as a friend is an implementation detail and never part of a public interface. Therefore, class and struct friend declarations should never appear.
