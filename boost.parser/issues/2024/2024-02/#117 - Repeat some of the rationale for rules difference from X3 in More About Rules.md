# #117 - Repeat some of the rationale for rules difference from X3 in More About Rules [Closed]

> Username: tzlaine  
> Created at: 2024-02-25 19:58:04 UTC  
> Updated at: 2024-03-15 21:29:28 UTC  
> Closed at: 2024-03-15 21:29:28 UTC  
> Url: https://github.com/boostorg/parser/issues/117  

Me, followed by Andrzej, from the Boost review:  
  
>> IMO, the problem with this is that, even though I specified a vector  
>> as the rule's attribute type, I can feed the rule a set (#define  
>> FIXED_ATTRIBUTE 1), and it happily populates it with the parsed  
>> values.  Note that this sorts, and in this case reorders, the values.  
>> I think this looseness is important in many cases, but there's no way  
>> to turn it off in X3.  In Parser the way to turn it off is to use a  
>> rule instead of a parser.  My inability to disable this looseness  
>> resulted in wrong results in an X3 YAML parser I was working on.  I  
>> really loved so much of the design of X3, that my frustration with  
>> this problem was a pretty big part of the motivation for writing  
>> Parser.  
>  
>  
> I would recommend putting something like the above in the docs, where the motivation for using rule parsers is listed. Currently it states "fix the attribute type produced by a parser to something other than the default" but what you sayin above is more: "prevent the parsers from doing things that they would otherwise do".
