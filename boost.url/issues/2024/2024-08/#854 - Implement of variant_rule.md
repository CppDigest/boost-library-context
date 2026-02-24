# #854 - Implement of variant_rule [Closed]

> Username: A2ureStone  
> Created at: 2024-08-01 11:42:43 UTC  
> Updated at: 2024-08-08 13:42:44 UTC  
> Closed at: 2024-08-08 13:42:44 UTC  
> Url: https://github.com/boostorg/url/issues/854  

According to the document, function parse has 2 implementation. One of them is required to match the entire string.  
The variant_rule should implement the ABNF rule variant = rule1 / rule2 / rule3 ....  
When I combine parse and variant_rule, I expect it to match the entire string. However, if the preceding rules can partially match the string, based on the current implementation, it will return an error, and subsequent rules that may fully match will be ignored.  
This is a test I conducted while implementing this set of rules.  
```cpp  
// obs-FWS         =   1*WSP *(CRLF 1*WSP)  
// FWS             =   ([*WSP CRLF] 1*WSP) /  obs-FWS  
  constexpr auto wsp_chars = grammar::lut_chars(" \t");  
  constexpr auto obs_fws_rule = grammar::tuple_rule(  
      grammar::token_rule(wsp_chars),  
      grammar::range_rule(grammar::literal_rule("\r\n"),  
                          grammar::token_rule(wsp_chars)));  
  
  /// Rule for FWS = ([*WSP CRLF] 1*WSP) / obs-FWS  
  constexpr auto fws_rule = grammar::variant_rule(  
      grammar::tuple_rule(  
          grammar::optional_rule(grammar::tuple_rule(  
              grammar::optional_rule(grammar::token_rule(wsp_chars)),  
              grammar::literal_rule("\r\n"))),  
          grammar::token_rule(wsp_chars)),  
      obs_fws_rule);  
  auto cv = parse("  \r\n  \r\n  ", variant_rule(fws_rule, obs_fws_rule));  
  std::cout << cv.error() << std::endl;  
```  
here is the output  
```  
boost.url.grammar: 4   
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-08-01 11:56:31 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2262856542  

So, you are using the `grammar` algorithms! Great :)

---

## Comment 2

> Username: A2ureStone  
> Created at: 2024-08-01 12:08:27 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2262879047  

yep, i want to use it to implement some rules in rfc5322.I wonder if the the combination of variant and parse is designed like this, or is it a bug?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-08-01 12:25:01 UTC  
> Updated at: 2024-08-01 12:26:32 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2262911006  

Can you please show which part of RFC5322 you are trying to use the variant rule with?  
  
Note that these algorithms, are not intended to be a perfect, general purpose parser combinator library in the same style as Boost.Spirit or Boost.Parser. Sometimes you need to write an algorithm yourself in order to express certain ABNFs. This could be one such case.

---

## Comment 4

> Username: A2ureStone  
> Created at: 2024-08-01 12:37:48 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2262935987  

https://datatracker.ietf.org/doc/html/rfc5322#section-3.6.4. This section about message-id.  
I looked at the documentation for grammar and thought it was designed to deal with the ABNF syntax in rfc.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-08-01 12:43:44 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2262948076  

> I looked at the documentation for grammar and thought it was designed to deal with the ABNF syntax in rfc.  
  
Yes but there is no code which looks for the longest match. You have to do that yourself, by writing a regular function. We could look into improving the variant rule, maybe. In most cases where variant rule is an attractive option there is usually a better performing solution in writing a custom function.

---

## Comment 6

> Username: A2ureStone  
> Created at: 2024-08-01 14:28:33 UTC  
> Updated at: 2024-08-01 14:30:15 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2263221100  

Perhaps my explanation was not clear enough. The variant_rule function performs non-full matches, which is expected behavior. The problem arises from the parse function, which has a full match version. Combining these two can lead to inconsistencies. I've seen examples in the code where this combination is used. In the cases for uri_reference_rule,   
```cpp  
// URI-reference = URI / relative-ref  
system::result< url_view > rv = grammar::parse( "ws://echo.example.com/?name=boost#demo" , uri_reference_rule );  
```  
our BNF rules require either a full match with URI or a full match with relative-ref. This example clearly demands a full match. If a string is a completely match relative-ref but only partially matches URI, the current parse implementation will incorrectly return a non-match, even though such a string is impossible due to relative-refs starting with `/`.

---

## Comment 7

> Username: alandefreitas  
> Created at: 2024-08-07 20:59:33 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2274337643  

@A2ureStone, thanks for starting this discussion. At worst, the behavior of these functions should be better documented.  
  
Yes. This is not a bug. Almost all basic grammar library implementations are greedy, including regex implementations. For instance:  
  
```js  
const regex = /abc|abcd/;  
const input = "abcd";  
const result = regex.exec(input);  
console.log("Match found:", result[0]);  
```  
  
will match `abc`. When dealing with alternatives, the order of alternatives affects the match because regular expressions are by default greedy.   
  
The equivalent of `regex.exec` in C++ would be `std::regex_search`:  
  
```cpp  
    std::regex regex("abc|abcd");  
    std::string input = "abcd";  
    std::smatch match;  
    if (std::regex_search(input, match, regex)) {  
        std::cout << "Match found: " << match.str(0) << std::endl;  
    }  
```  
  
However, in some cases, such as std::regex_match, the implementation should backtrack: https://godbolt.org/z/zaqnve449 as it should backtrack when forced to match the end of the input with a `$`:  
  
```js  
 /(abc|abcd)$/.exec('abcd');  
```  
  
In any case, the base case for grammar is always greedy. The same greedy logic applies to almost all parsing libraries (Boost.Spirit, Boost.Parse, PEGTL, ...) and grammars because the alternative is always worse: For every way that any rule could match any subset of the string, the algorithm would have to roll the tape to the end to check if it consumes the whole input, and otherwise, exponentially backtrack to all other alternatives.   
  
The cost of backtracking is so high, and the use cases are so different that a `parse` variant that backtracks should be a new function. The API of rules also needs to be updated somehow so that rules can return all alternative matches it can produce so that the parent algorithm can iterate them. Because of this extra complexity, we usually want to write a grammar that works with greedy rules.  
  
One easy way to do that (when it makes sense) is to list the rules that should match the longer string first in the variant. It's often the case that we have multiple rules but we know from context that some rules will always match longer strings than others.  
  
In your case, the rules are:  
  
```  
obs-FWS         =   1*WSP *(CRLF 1*WSP)  
FWS             =   ([*WSP CRLF] 1*WSP) /  obs-FWS  
```  
  
so I don't even think it makes sense to parse `variant_rule(fws_rule, obs_fws_rule)` at all because `obs-FWS` is already part of `FWS.` In any case, the first alternative is to use existing boost::urls::grammar rules and to change how the rules are combined so the algorithm is still greedy and correct. In the case of communications protocols, there should usually be a correct greedy algorithm to parse the input because the input is being received in a stream, so there's no concept of "end of message" at that level.  
  
As a second option, you can also write your own non-greedy rule for this variant only, making it a rule that works with other greedy rules. The rule would inspect both paths and match the longest one. Or it would be a choice depending on the rules to come.   
  
In a way, it's not impossible for `url` to implement a new `longest_variant_rule`, which would be an expensive variant of variant_rule that returns the longest match. However, it wouldn't be very useful since it's very common just to be able to fix everything by changing the order of the rules and not having this extra cost. Also because it doesn't completely solve your problem (more on that below).  
  
I think Vinnie has a good point here:  
  
> In most cases where variant rule is an attractive option there is usually a better performing solution in writing a custom function.  
  
Custom parsers are better than general parsers because there are alternatives like these optional or variant rules; custom parsers can use contextual information to eliminate alternatives in a way a general solution just can't. If you think about the other kinds of rule composition, a custom parser shouldn't be able to outperform a general parser because it's just iterating the string anyway.   
  
For instance, contextual information is what solves the problem in the cases you mentioned, like "This example clearly demands a full match. If a string is a complete match relative-ref but only partially matches URI". This is not a problem because we know from context that these rules are not ambiguous so whatever fails to match would fail in both overloads of `parse`. Also note how your variant_rule is, on the other hand, ambiguous. It includes obs-FWS, which is also an alternative of FWS.  
  
Also, matching the longest substring in variant_rule doesn't automatically solve your problem either. It's possible that matching the longest substring here means the rest of the input is now invalid for the following rules in a way that it may not happen in the shortest sequences. So, in any case, you'll need to backtrack unless you have contextual information that lets you know that the longest match will always solve the problem.   
  
Another alternative is a backtracking rule of sorts to indicate that, unlike most rules, that variant rule should be considered an inflection point where all alternatives should roll the tape to the end. Still, the design of such a rule could become quite complex. One design is where it would take the alternatives AND the following rules so it can backtrack on the following rules. Another design is one where the rules would somehow return lists of matches instead of a match and the parent algorithm would need to be aware of that special logic.  
  
Apart from that, if you can't find a greedy rule but you're writing your own `parse_x` algorithm for your rule, like `parse_uri`, etc., you can foresee that this rule might match prematurely and backtrack there. It's some work, but it's best to have and use the specific cases that might need to backtrack rather than rely on an implementation that tries to backtrack on all rules. And it can only "try" to backtrack because the library supports customs rules.

---

## Comment 8

> Username: A2ureStone  
> Created at: 2024-08-08 13:30:19 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2275840236  

I have grasped the logic inside, theoretically it's best to use variant rules under the premise that the different rules do not contain each other. Due to my lack of parsing knowledge, I directly translated the ABNF grammar from the RFC, some of the variant rules have overlapping parts, but it can still be solved by arranging the rules. Thank you for your patient explanation!

---

## Comment 9

> Username: alandefreitas  
> Created at: 2024-08-08 13:42:44 UTC  
> Url: https://github.com/boostorg/url/issues/854#issuecomment-2275868030  

I'm glad you found a solution. You're correct: translating the grammar directly as documented doesn't always work well because rules can be implemented differently depending on what contextual information we have.
