# #1206 - Review 2: Examples [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 17:57:49 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-09 15:24:46 UTC  
> Url: https://github.com/boostorg/decimal/issues/1206  

You can never have enough, or good enough examples for Boost reviewers...  
  
>In my opinion the examples need text, and I would put that before the code.  
Similarly how "Construction from an Integer and Exponent" has text.  I will  
suggest either some text as a start at every example or what  
questions/information the text should answer/cover (in my opinion).  
  
>The examples contain `using namespace boost::decimal` while within the examples  
some things are qualified with `boost::decimal` while others aren't.  My  
general suggestion is that, except for  
`using namespace boost::decimal::literals`, the examples should not have  
`using namespace` but rather `using` declarations of the specific decimal types  
(and other names that won't be found using ADL) that are named in the example  
code, such as for example for "Literals and Constants" `using decimal32_t`.  
  
and:  
  
>Given that this domain is probably not the easiest for most people to understand unless they have full understanding of the IEEE 754 spec/standard, I think a few more real world examples would be beneficial. I'm in the trading/financial industry and examples geared there would have helped from the initial consumption of the library could have helped greatly. Things like showing the benefits of using this library vs what a novice would do using just floating point math with the C++ float and double types.  
  
In general then, this means a bit more "hand holding" while walking potential users through the library.  I haven't compared the current status to the review version, so you may well have worked on this already.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 07:54:33 UTC  
> Url: https://github.com/boostorg/decimal/issues/1206#issuecomment-3495646712  

I haven't touched the docs too much outside of obvious changes in review. Since other features are getting added in I'd rather have that all worked out before writing how-tos
