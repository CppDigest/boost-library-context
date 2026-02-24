# #45 - Please change std::regex::ECMAScript to std::regex::JavaScript [Closed]

> Username: jmichae3  
> Created at: 2017-10-18 02:40:16 UTC  
> Updated at: 2017-10-25 17:15:00 UTC  
> Closed at: 2017-10-25 17:15:00 UTC  
> Url: https://github.com/boostorg/regex/issues/45  

change ECMAScript value to Javascript (JavaScript is original language and still is)  
JavaScript is a fuller language put out by Danny Goodman. it probably has a better regex spec - contact him about it.  
  
the other name is offending lots of developers, which are 1/4 the population.  
the value std::regex::ECMAScript needs to be changed to std::regex::JavaScript in both the manuals, source file names, and source code for std::regex. JavaScript is the language's name. and you will probably find that the EBNF for JavaScript is not missing entries.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-10-25 17:14:59 UTC  
> Url: https://github.com/boostorg/regex/issues/45#issuecomment-339403032  

Are you talking about std::regex or boost::regex?  
  
The std quite deliberately chose to follow the ECMA standard rather than moving targets like Perl or Javascript.  If that's the issue then you need to report it to the std not boost.  
  
If Boost, then we actually follow Perl rather than Javascript.
