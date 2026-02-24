# #411 - invalid static_cast when using _val = style assignment in semantic action Boost 1.65.1 [Closed]

> Username: godmar  
> Created at: 2018-10-26 04:28:50 UTC  
> Updated at: 2019-02-09 01:41:02 UTC  
> Closed at: 2018-10-26 13:55:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/411  

I apologize if this comes across as a usage question rather than a bug report. I do not understand why this code does not compile and wonder whether it should:  
  
```  
qi::rule<It, std::string()> rule = qi::int_   
   [ _val = px::bind([](int i) -> std::string { return std::to_string(2*i); }, _1) ];  
```  
[Full example here](http://coliru.stacked-crooked.com/a/8582cb0d28d99bdc)  
  
Should it compile?  The example is adopted from [here](https://stackoverflow.com/questions/49859896/using-lambdas-or-external-functions-as-spirit-qi-semantic-actions) and I'll note an analogous example using [phx::new_ appears to compile](https://github.com/bingmann/2018-cpp-spirit-parsing/blob/master/spirit6_ast.cpp#L142).  
  
If it shouldn't compile, would it be a reasonable enhancement request to support this? Thank you.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-26 12:02:20 UTC  
> Updated at: 2018-10-26 12:20:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/411#issuecomment-433384831  

```cpp  
	qi::rule<It, std::string()> rule = ...  
    int data = 99;  
    if (parse(f, l, rule, data))  
```  
You are asking your parser to assign a string to the integer value. Just replace `int data = 99;` with `std::string data;`. https://wandbox.org/#wandbox-resultwindow-code-body-7

---

## Comment 2

> Username: godmar  
> Created at: 2018-10-26 12:50:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/411#issuecomment-433396646  

I'm so sorry, in my attempt to pare down the actual problem I'm having I went a step too far.  
The error really occurs not at the top-level where I get the value from the parser, but near the leaves of the grammar. Here's another attempt that hopefully avoids this bug (I have an inner rule that parses a string, but wants to use a semantic action to convert the attribute type from a string to an int.  Is that possible? I've looked through the [qi tests](https://github.com/boostorg/spirit/tree/develop/test/qi) and didn't see where this is tested.)  
  
```  
    qi::rule<It, std::string()> inputstring;    // produces string as _val  
    inputstring %= qi::alpha >> *qi::alnum;  
  
    qi::rule<It, int()> rule;     // produces int as _val  
    // convert (std::string _1) to (int _val)  
    rule %= inputstring [ _val = px::bind([](std::string &s) -> int { return std::stoi(s); }, _1) ];  
  
    It f = begin(input), l = end(input);  
    int data;  
    if (parse(f, l, rule, data))        // parse rule which returns an int  
        std::cout << "Parsed: " << data << " ";  
```  
Corrected example [here](http://coliru.stacked-crooked.com/a/cb92843dc4a73ffb).

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-10-26 13:06:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/411#issuecomment-433400971  

Ok, I will look into this a bit later.

---

## Comment 4

> Username: godmar  
> Created at: 2018-10-26 13:07:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/411#issuecomment-433401216  

PS: I figured it out!  I was using %= instead of =.  Working example is [here](http://coliru.stacked-crooked.com/a/037b95f1c39f41f5).  
  
May I make an enhancement suggestion: would it make sense to update the [documentation](https://www.boost.org/doc/libs/1_67_0/libs/spirit/doc/html/spirit/qi/reference/nonterminal/rule.html#spirit.qi.reference.nonterminal.rule.expression_semantics) so that it clearly states that for "Auto-rule definitions," the attribute of p must (not should!) be compatible with the synthesized attribute of r, and that unlike for =, any semantic actions associated with p may not change the attribute type? (If that's correct?)

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-10-26 13:11:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/411#issuecomment-433402301  

> I figured it out! I was using %= instead of =  
  
Glad you figured out the problem. I have spotted this, but the online compiler was killing itself with your example :smiley:.  
  
> May I make an enhancement suggestion  
  
Yeah, go ahead, open a PR.
