# #154 - Create an optional packrat optimization [Closed]

> Username: tzlaine  
> Created at: 2024-03-02 03:23:47 UTC  
> Updated at: 2024-03-23 21:18:11 UTC  
> Closed at: 2024-03-23 21:18:11 UTC  
> Url: https://github.com/boostorg/parser/issues/154  

A packrat parser is a PEG parser like Parser that memoizes parts of the parse, so as to make backtracking much cheaper.  It guarantees linear parse time.  This comes at the cost of space, and potentially a lot of it.  It should therefore be an optional strategy, perhaps controlled via a parameter to `*parse()`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-23 21:18:11 UTC  
> Url: https://github.com/boostorg/parser/issues/154#issuecomment-2016605359  

This turns out not to be bpossible, because of actions.  It took me a long time to realize this.  An action may have side effects outside of the parse, or inside of the parse.  By this, I mean that the side effect could be completely remote, like logging or launching a missile.  It could also be technically a side effect, but still contribute to some attribute, like:  
  
```c++  
#include <boost/parser/parser.hpp>  
  
#include <iostream>  
#include <string>  
  
  
namespace bp = boost::parser;  
  
int main()  
{  
    std::cout << "Enter a list of doubles, separated by commas. ";  
    std::string input;  
    std::getline(std::cin, input);  
  
    std::vector<double> result;  
    auto const action = [&result](auto & ctx) {  
        std::cout << "Got one!\n";  
        result.push_back(_attr(ctx));  
    };  
    auto const action_parser = bp::double_[action];  
    auto const success = bp::parse(input, action_parser % ',', bp::ws);  
  
    if (success) {  
        std::cout << "You entered:\n";  
        for (double x : result) {  
            std::cout << x << "\n";  
        }  
    } else {  
        std::cout << "Parse failure.\n";  
    }  
}  
```  
  
In a packrat parser, nonterminals' parse results are recorded.  So for a parser like `action_parser % ','` above ("P" hereafter), were it part of a larger parse, we would memoize its result the first time we used it at location L within the parse.  Then, later on, we would re-use that memoized result if trying to use P at that same location L.  That works in this case; even though we don't re-run the actions within `action_parser`, the result is correct, because we captured the result of those actions in an external variable.  However, if something else in the code were to reset the external variable `result`, we would *inappropriately* not have re-run the actions within P.  There is no way for a memoization system to know whether to re-execute the actions or not; the right answer depends on the user's particular use of actions.  
  
A similar problem exists for "external" (that is non-attribute-affecting) side effects.  If the user writes actions to count successful parses, re-running them is an error.  If the user writes actions to count all parses, not re-running them is an error.  Either convention will turn out to be wrong for some users.  
  
In short, the packrat parser change would not be an optimization, since it changes the library's semantics.
