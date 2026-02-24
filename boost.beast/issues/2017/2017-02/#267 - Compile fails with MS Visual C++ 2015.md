# #267 - Compile fails with MS Visual C++ 2015 [Closed]

> Username: daniele77  
> Created at: 2017-02-17 16:40:42 UTC  
> Updated at: 2017-02-24 18:51:09 UTC  
> Closed at: 2017-02-24 18:51:09 UTC  
> Url: https://github.com/boostorg/beast/issues/267  

When compiling the examples with Visual C++ 2015, I get the following error:  
`Compiler Error C2899: "typename cannot be used outside a template declaration"`  
in file:  
`Beast\include\beast/http/detail/basic_fields.hpp`  
at lines: 97, 100, 142.  
  
I tried with the current git master HEAD: commit 9f10b11  
  
The code compiles with VS 2015, gcc 4.9.2 and clang 3.5.0 if I remove "typename" from the lines 97, 100, 142.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-17 17:14:29 UTC  
> Url: https://github.com/boostorg/beast/issues/267#issuecomment-280710020  

That is bizarre because I am on Visual Studio 2015. I suspect you are using something earlier than VS 2015 Update 3. However, since the `typename` is not technically necessary (since the class is not a template class) I will remove it. Thanks for the report!

---

## Comment 2

> Username: daniele77  
> Created at: 2017-02-20 14:52:59 UTC  
> Url: https://github.com/boostorg/beast/issues/267#issuecomment-281099036  

Thanks, I realized I had VS without updates. I'm gonna update it with update 3.  
Anyway: as you said, typename was not strictly necessary in that case.
