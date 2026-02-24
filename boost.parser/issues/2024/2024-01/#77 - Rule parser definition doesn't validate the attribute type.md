# #77 - Rule parser definition doesn't validate the attribute type [Closed]

> Username: akrzemi1  
> Created at: 2024-01-25 21:38:46 UTC  
> Updated at: 2024-01-29 17:08:52 UTC  
> Closed at: 2024-01-26 01:26:00 UTC  
> Url: https://github.com/boostorg/parser/issues/77  

I am surprised that the following rule parser definition compiles:  
  
```c++  
namespace myparser  
{  
  const bp::rule<struct str_tag, std::variant<std::string>> str = "str";  
  const auto str_def = bp::int_;  
  BOOST_PARSER_DEFINE_RULES(str);  
}  
```  
  
There is no way the attribute of parser `int_` could be converted to `variant<string>`. I would expect that the macro, among other magic, would verify this attribute mismatch. This would give rule parsers an additional feature of early/cleaner bug detection.  
  
The full example: https://godbolt.org/z/TcPfzhj9n

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-26 01:26:00 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1911262439  

Unfortunately, there's no way to do this detection.  You could have attached a semantic action to `bp::int_` that takes the `int` attribute, converts it to a string, and assigns it to the `_val()` of `str`.  It's easy for a human to see that this is not the case looking at the code above, but even your simple case looks identical to this:  
  
```c++  
auto a = [](auto & ctx) {  
    _val(ctx) = "Yay, it worked!";  
};  
const auto str_def = bp::int_ >> bp::eps[a];  
```  
  
This version of `str_def` and yours have the same attribute type.  Boost.Parser can't tell that one works and the other does not.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-26 19:56:05 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1912613288  

Yet, when I pass thus defined rule parser to function `parse`, I do get a compiler error:  
  
```c++  
namespace myparser  
{  
  const bp::rule<struct str_tag, std::variant<std::string>> str = "str";  
  const auto str_def = bp::int_;  
  BOOST_PARSER_DEFINE_RULES(str);  
}  
  
int main()  
{  
  std::variant<std::string> result;  
  auto b = bp::parse("3", myparser::str, bp::ws, result); // compiler error  
}  
```  
  
Which looks like the bug is detected at compile time in other cases. What am I missing?  
  
Full example: https://godbolt.org/z/Gff8rMMqr

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-26 20:07:05 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1912626035  

BTW, let me give you a context. I am trying to migrate a medium-sized parser originally written in Boost.Spirit: https://github.com/akrzemi1/wdungeon/blob/master/parser.cpp#L33  
  
I accepted the idea that I will be using rule parsers. Still it has been weeks and I am still seeing immensely long compiler errors that I do not understand. Small examples work fine, but this whole thing does not compile. I somehow expected that if I divide these combined parsers into rules, I will get cleaner/shorter error messages. This would make another convincing case for rule parsers: detect bugs earlier, provide cleaner error messages.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-01-27 03:38:53 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1912949705  

> Yet, when I pass thus defined rule parser to function `parse`, I do get a compiler error:  
>   
> ```c++  
> namespace myparser  
> {  
>   const bp::rule<struct str_tag, std::variant<std::string>> str = "str";  
>   const auto str_def = bp::int_;  
>   BOOST_PARSER_DEFINE_RULES(str);  
> }  
>   
> int main()  
> {  
>   std::variant<std::string> result;  
>   auto b = bp::parse("3", myparser::str, bp::ws, result); // compiler error  
> }  
> ```  
>   
> Which looks like the bug is detected at compile time in other cases. What am I missing?  
  
The thing that you're missing is that what you're asking for is impossible, for the reason I stated previously.  Or, rather, it's impossible without actually calling `parse()`.  I suppose the rule could do some kind of call to `parse()` to trigger any ill-formedness, but IMO, that's the user's job.  
  
When you define a rule, it's supposed to act as a building block that you can use to build more complicated parsers.  As such, I expect you to define your rule `R1`, use it in a bunch of tests to make sure it works, then put in on the shelf for later use.  Then you define rule `R2`, test it, and so on.  In that process of testing, you're going to call `parse()`, and you're going to see compilation errors.  And hopefully -- because your rules are reasonably-sized, you're going to spend a reasaonable amount of time fixing these errors.  
  
If you then make new rules in terms of existing rules, you again can have relatively simple rules with problems of a tractable size.

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-01-27 03:39:44 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1912950005  

> BTW, let me give you a context. I am trying to migrate a medium-sized parser originally written in Boost.Spirit: https://github.com/akrzemi1/wdungeon/blob/master/parser.cpp#L33  
>   
> I accepted the idea that I will be using rule parsers. Still it has been weeks and I am still seeing immensely long compiler errors that I do not understand. Small examples work fine, but this whole thing does not compile. I somehow expected that if I divide these combined parsers into rules, I will get cleaner/shorter error messages. This would make another convincing case for rule parsers: detect bugs earlier, provide cleaner error messages.  
  
Yeah, I remember seeing that stuff earlier.  Could you show me the Boost.Parser version?

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-01-27 05:26:13 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913007277  

Sure, I can give you my entire example: https://godbolt.org/z/8aedWWr4d  
But I wouldn't want you to just fix it. I am using this painful exercise to learn the library, and to give you feedback.

---

## Comment 7

> Username: akrzemi1  
> Created at: 2024-01-27 05:30:49 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913009091  

Ok, so it looks we are arguing both over the implementability issues and the purpose of the rules.  
  
Regarding the implementability, I understand that this is doable, if not calling the entire `parse()` internally, then by performing a subset of its job.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2024-01-27 06:18:39 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913027856  

Regarding the motivation for attribute-vs-parser checking, I am not convinced by your argument that each rule parser should be first thoroughly tested in isolation by the programmer.  
  
So far, I got the impression that the intended use is to have a separate rule parser wherever I would use a `::=` in the BFN notation. (Or am I wrong?)  
A single `::=` line (rule) doesn't make sense on its own. It only makes sense in the context of a full grammar; or a reasonable "sub-grammar" (if such a thing exists). This seems in accord with the advice I received from you: "for anything not absolutely trivial, use a rule parser".  
  
In my use case above, a single rule doesn't make sense for sure. Also, because, as you said, the rules cannot be parametrized (either by values or by types) this reinforces the impression that rules are *not* reusable components.  
  
And the first stage of testing is to perform a type-system compiler check. This already sieves out the first 50% of the bugs. And at the point of defining a rule -- when I look at it form the user's perspective -- you have all the data required to perform this check: I gave you the desired type of the attribute, as well as the "accidental" attribute resulting from the definition of the parser. My expectation for a compile-time check is sound. The only reason not to do it would be implementation difficulties.  
  
My need for having those checks is reinforced by seeing these extremely long template error messages that do not fit into screen buffers. I expect they would be way shorter, if you could introduce early checks on a smaller scale. I treat -- under my present understanding -- defining the rules as injecting more information for the compiler to do the check. Much as if I were changing this  
  
```c++  
auto f = fun();  
auto g = gun(f);  
auto h = hun(f, g);  
auto z = zun(h, g, f); // error and I do not know why  
```    
  
to this:  
  
```c++  
F f = fun();  
G g = gun(f);  
H h = hun(f, g);  
Z z = zun(h, g, f, z); // now I can see why I select the wrong overload  
```

---

## Comment 9

> Username: akrzemi1  
> Created at: 2024-01-27 06:24:47 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913030028  

> Sure, I can give you my entire example: https://godbolt.org/z/8aedWWr4d But I wouldn't want you to just fix it. I am using this painful exercise to learn the library, and to give you feedback.  
  
Not sure if a parser definition alone makes sense. This is supposed to parse a text file representing a plot of a text-based game. HEre is an example of such file, unfortunately in Polish: https://raw.githubusercontent.com/akrzemi1/wdungeon/master/wdungeon.plot

---

## Comment 10

> Username: akrzemi1  
> Created at: 2024-01-27 09:35:55 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913094765  

BTW, I managed to compile my program, with the help of https://gcc-explorer.com/.

---

## Comment 11

> Username: tzlaine  
> Created at: 2024-01-27 21:38:33 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913342692  

> Ok, so it looks we are arguing both over the implementability issues and the purpose of the rules.  
>   
> Regarding the implementability, I understand that this is doable, if not calling the entire `parse()` internally, then by performing a subset of its job.  
  
Hm.  I don't know what part that would be.  I think the right thing to do is just actually to call `parse()`.

---

## Comment 12

> Username: tzlaine  
> Created at: 2024-01-27 21:49:55 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1913345214  

> Regarding the motivation for attribute-vs-parser checking, I am not convinced by your argument that each rule parser should be first thoroughly tested in isolation by the programmer.  
  
Sure, you don't ever have to unit test code.  You can just put it all together and use/test the whole program.  You are however shooting yourself in the foot if you do so.  When I write parsers, I do exactly what I described earlier -- I write a rule, test it, and then move on to the next rule.  
  
> So far, I got the impression that the intended use is to have a separate rule parser wherever I would use a `::=` in the BFN notation. (Or am I wrong?) A single `::=` line (rule) doesn't make sense on its own. It only makes sense in the context of a full grammar; or a reasonable "sub-grammar" (if such a thing exists). This seems in accord with the advice I received from you: "for anything not absolutely trivial, use a rule parser".  
  
Maybe?  This is season-to-taste territory.  Break down your program into discrete pieces however you like.  I tend to take things that seem like complete things as my lowest-level rules.  For instance, in a JSON parser, you have string parsing.  Escaping is involved in that.  I would probably make the string itself the rule, not the escpaing subparsers.  However, if I wanted some part(s) of the escape parsing to produce good error messages, I might introduce rule(s) to do that.  I would unit test the "string" rule only, however.  
  
> In my use case above, a single rule doesn't make sense for sure. Also, because, as you said, the rules cannot be parametrized (either by values or by types) this reinforces the impression that rules are _not_ reusable components.  
  
This is a weird statement.  A nontemplate `void ()` function cannot be parameterized.  Is it therefore  not a reusable component?  
  
> And the first stage of testing is to perform a type-system compiler check. This already sieves out the first 50% of the bugs. And at the point of defining a rule -- when I look at it form the user's perspective -- you have all the data required to perform this check: I gave you the desired type of the attribute, as well as the "accidental" attribute resulting from the definition of the parser. My expectation for a compile-time check is sound. The only reason not to do it would be implementation difficulties.  
  
Well, almost.  I also need the type of the iterator you're using, or more precisely, the `value_type` of that iterator.  Parsing sequences of `char` and `char32_t` might produce different atttributes -- or they might not.  It depends on your parser.  Let's say I just decided I'm going to instantiate both of these cases -- call `parser::parse("", your_parser)` *and* call `parser::parse(U"", your_parser)`.  If either one of these `static_assert`s of correctness fails, your code is ill-formed, even if you only need one, and that one is *not* ill-formed.  So, maybe I only do one of them.  Ok, which one?  And what happens if the one I pick is fine, but later you use the other one, and everything fails?  
  
> My need for having those checks is reinforced by seeing these extremely long template error messages that do not fit into screen buffers. I expect they would be way shorter, if you could introduce early checks on a smaller scale.  
  
On this we agree.  Fortunately, this is in the user's control.  They can just write 'parse(...)`.  
  
> I treat -- under my present understanding -- defining the rules as injecting more information for the compiler to do the check. Much as if I were changing this  
>   
> ```c++  
> auto f = fun();  
> auto g = gun(f);  
> auto h = hun(f, g);  
> auto z = zun(h, g, f); // error and I do not know why  
> ```  
>   
> to this:  
>   
> ```c++  
> F f = fun();  
> G g = gun(f);  
> H h = hun(f, g);  
> Z z = zun(h, g, f, z); // now I can see why I select the wrong overload  
> ```

---

## Comment 13

> Username: akrzemi1  
> Created at: 2024-01-29 17:08:51 UTC  
> Url: https://github.com/boostorg/parser/issues/77#issuecomment-1915189234  

Ok, so you are saying that whether a `parse` ivocation is ill-formed depends on three things:  
  
 1. declared attribute type,  
 2. parser type used in the implementation,  
 3. type of input character.  
  
I still believe that there is a subset of all possible incompatibilities between 1) and 2) that do not depend on 3), and they could be diagnosed earlier. But I see that this is not that easily implementable.  
  
I rest my case.
