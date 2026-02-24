# #218 - Consider adding type erased `any_parser` (was: Combining parsers at runtime) [Closed]

> Username: rafal-c  
> Created at: 2025-04-03 06:28:59 UTC  
> Updated at: 2025-04-26 22:42:54 UTC  
> Closed at: 2025-04-26 22:42:53 UTC  
> Url: https://github.com/boostorg/parser/issues/218  

Hi, I couldn't find any examples or documentation regarding combining parsers at runtime. Specifically, in the following scenario:  
  
```cpp  
auto parsers = std::vector {bp::string("A"), bp::string("vector"), bp::string("of"), bp::string("parsers.")};  
  
auto sequence_parser = parsers[0] >> parsers[1] >> parsers[2] >> parsers[3];  // <---- how to do this in general case?  
```  
I suspect it might be impossible, given that the sequence elements are part of the template type. In that case, what is the preferred solution here? Calling `bp::prefix_parse` with each parser in turn?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-07 07:22:03 UTC  
> Url: https://github.com/boostorg/parser/issues/218#issuecomment-2782271474  

The short answer is there is no way to do this directly ('this' being make a sequence parser out of a vector of parsers).  This is not a use case I've seen before, and I'm intrigued.  What are you trying to do exactly?  If I agree that this is a compelling use case, maybe we can just add a way to make sequence/or/other common parsers from ranges of parsers.  Just thinking out loud here -- this may be unworkable in some way.  
  
A major question I have is why do you have so many parsers of the same type, so that they can fit in a vector?  Adjacent parsers within a sequence parser are commonly different types.  
  
Please explain the use case as completely as you're able.

---

## Comment 2

> Username: rafal-c  
> Created at: 2025-04-09 07:55:22 UTC  
> Url: https://github.com/boostorg/parser/issues/218#issuecomment-2788691315  

I would like to write a parser for files consisting of regularly formatted lines where the format of each file is provided by the user at runtime in the form a printf-like format string. For instance, one of the files may look like this:  
```  
username: john, connection attempts: 12, access: granted  
username: mike, connection attempts: 7, access: denied   
username: jake, connection attempts: 42, access: granted   
```  
for which a format string is provided: `"username: %s, connection attempts: %d, access: %s"`.  
  
The expected result of the parse is:   
```cpp  
std::vector<std::vector<SlotType>> {{"john", 12, "granted"}, {"mike", 7, "denied"}, {"jake", 42, "granted"}}  
```   
where  
```cpp  
using SlotType = std::variant<std::string, int, ...>; // corresponds to all supported slot types: %s, %d, etc.  
```  
  
My approach would be to dynamically create a vector of parsers, in this case:  
```cpp  
std::vector<some ugly variant> {   
    bp::string("username: "),   
    *(bp::cu - bp::eol),   
    bp::string("connection attempts: "),   
    bp::uint_,   
    bp::string("access: "),   
    *(bp::cu - bp::eol)  
};  
```  
and then somehow combine these parsers . For simplicity, I started experimenting only with string literal parsers but that is where I already got stuck.   
  
In the end, I decided to switch to regular expressions and implemented this with Boost.Xpressive but I would still like to know your thoughts if this is possibly doable with Boost.Parser?

---

## Comment 3

> Username: tzlaine  
> Created at: 2025-04-12 18:47:57 UTC  
> Url: https://github.com/boostorg/parser/issues/218#issuecomment-2798950789  

Huh.  Yeah, that is a pretty interesting use case.  Thought for something so simple, I think you're better off doing something like what you're already doing -- using a regex lib.  (I would recommend CTRE over Xpressive these days.)  
  
The problem with doing it in Boost.Parser is that we'd have to add a range-based, or maybe variadic, interface for constructing a sequence parser.  That might help in this particular case, but does not generalize well at all.  Concerns:  
  
* What if there were a single alternative you wanted to include?  You'd need support for alternative parsers as well probably.  
* What happens if all the parsers are not the same type?  This precludes a range-based interface for all the parsers, and implies the use of a variadic function template instead.  But a variadic function does not work for your case, where you have a free-form list of parsers stored in a vector, of unknown length.  
  
FWIW, I don't think your example even works with a vector of parsers.  Your example format is `lit1 >> p1 >> lit2 >> p2 >> lit3 >> p3`, where `litN` are string literal parsers, but `p1` and `p2` are not, and are different parsers from each other.  There's no putting all those parsers into the same vector.  
  
I think what it comes down to is that we would ultimately need a type erased `any_parser`, sort of like a Spirit 2 rule.  Then you could read your format string, make a concrete parser `pI` of the desired type, use that to construct the `any_parser` for that part of the parse.  Then you could have a range-based construction function for making each of the multi-parser higher-order parsers, like sequences, alternatives, etc.

---

## Comment 4

> Username: tzlaine  
> Created at: 2025-04-12 18:49:00 UTC  
> Url: https://github.com/boostorg/parser/issues/218#issuecomment-2798951187  

This sounds like a fun thing to work on, but I'll need some time to think about it for sure.

---

## Comment 5

> Username: tzlaine  
> Created at: 2025-04-26 22:42:53 UTC  
> Url: https://github.com/boostorg/parser/issues/218#issuecomment-2832680435  

I tried to work on this a bit, bit it turns out it doesn't actually work.  At some point, someone needs to generate a table that is the cartesian product of the set of all parsers with the set of all combinations of template parameters ever used to call any parser's `.call` member(s).  This is not easily knowable by any place in the code except right at the point that a `.call` member function call is instantiated.  
  
The alternative would be to have some kind of multi-dispatch in the C++ language itself, which we do not have.
