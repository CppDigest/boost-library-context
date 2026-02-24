# #142 - Docs: typo in Error Handling and Debugging [Closed]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 19:31:03 UTC  
> Updated at: 2024-03-15 21:29:30 UTC  
> Closed at: 2024-03-15 21:29:30 UTC  
> Url: https://github.com/boostorg/parser/issues/142  

On the page [Error Handling and Debugging](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/error_handling_and_debugging.html), the code snippet for diagnosing missing XML close tags ends with:  
  
```  
        // Explicitly fail the parse.  Diagnostics to not affect parse success.  
```  
  
"Diagnostics to not affect" should be "Diagnostics do not affect".
