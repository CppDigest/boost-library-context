# #578 - Provide a Compound Attribute type for case `A | B | C` [Closed]

> Username: akrzemi1  
> Created at: 2020-03-07 09:42:31 UTC  
> Updated at: 2020-03-07 10:22:26 UTC  
> Closed at: 2020-03-07 10:22:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/578  

In the spirit of Compound Attributes described in https://www.boost.org/doc/libs/1_72_0/libs/spirit/doc/html/spirit/qi/quick_reference/compound_attribute_rules.html#spirit.qi.quick_reference.compound_attribute_rules.notation  
  
provide the following Compound Attribute:  
  
Alternative(`a | b`)  `a: A, b: B, c: C --> (a | b | c): variant<A, B, C>`  
  
Today, I get type `variant<A, variant<B, C>>` which I found surprising.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2020-03-07 10:22:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/578#issuecomment-596071733  

Sorry. It already works this way. I was confused by a long error message.
