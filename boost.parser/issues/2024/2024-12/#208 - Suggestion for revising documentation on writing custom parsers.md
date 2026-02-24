# #208 - Suggestion for revising documentation on writing custom parsers [Open]

> Username: intractabilis  
> Created at: 2024-12-05 05:01:52 UTC  
> Updated at: 2024-12-05 05:01:52 UTC  
> Url: https://github.com/boostorg/parser/issues/208  

https://github.com/boostorg/parser/blob/f468d529fe440313d9d67d092f360b1a64a6f702/doc/tutorial.qbk#L3792  
  
Thank you for your work on Boost Parser. It's an impressive library, and I've found it quite powerful.  
  
While I appreciate the humor in the writing, I found the tone to be slightly dismissive of scenarios where writing custom parsers might genuinely be the most effective solution. For instance, I am working on parsing SystemVerilog code, which includes number literals with unique syntax, such as `5 'D 3`. Parsing such constructs character by character using char_ primitives seems neither convenient nor effective, and it would make a strong case for writing a custom parser akin to `uint_`.  
  
I believe this use case illustrates that there are situations where users might reasonably need more guidance on creating low-level parsers. If this section of the documentation could include:  
  
1. A more neutral tone when addressing the use of custom parsers.  
2. An example of how to effectively handle complex cases (like SystemVerilog number literals) using the library's primitives or via a custom parser.  
  
This approach would make the documentation more inclusive and better serve a wider range of users.  
  
Thank you again for your efforts, and I hope my feedback helps in further refining the excellent resources you provide.
