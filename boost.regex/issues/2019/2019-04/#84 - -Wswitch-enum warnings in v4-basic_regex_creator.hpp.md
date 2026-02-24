# #84 - -Wswitch-enum warnings in v4/basic_regex_creator.hpp [Open]

> Username: anadon  
> Created at: 2019-04-29 16:20:35 UTC  
> Updated at: 2020-01-22 18:48:29 UTC  
> Url: https://github.com/boostorg/regex/issues/84  

Testing graph with new options, the following warnings came up which should be addressed:  
  
```  
clang-linux.compile.c++.without-pch ../../../bin.v2/libs/graph/test/graphviz_test.test/clang-linux-8.0.0/debug/threading-multi/visibility-hidden/graphviz_test.o  
In file included from graphviz_test.cpp:16:  
In file included from ../../../boost/regex.hpp:31:  
In file included from ../../../boost/regex/v4/regex.hpp:70:  
../../../boost/regex/v4/basic_regex_creator.hpp:717:14: warning: 26 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_endmark', 'syntax_element_literal'... [-Wswitch-enum]  
      switch(state->type)  
             ^  
../../../boost/regex/v4/basic_regex_creator.hpp:828:32: warning: 28 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_literal', 'syntax_element_start_line'... [-Wswitch-enum]  
                        switch(p->type)  
                               ^  
../../../boost/regex/v4/basic_regex_creator.hpp:757:14: warning: 32 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_endmark', 'syntax_element_literal'... [-Wswitch-enum]  
      switch(state->type)  
             ^  
../../../boost/regex/v4/basic_regex_creator.hpp:903:14: warning: 26 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_endmark', 'syntax_element_literal'... [-Wswitch-enum]  
      switch(state->type)  
             ^  
../../../boost/regex/v4/basic_regex_creator.hpp:984:14: warning: 18 enumeration values not explicitly handled in switch: 'syntax_element_start_line', 'syntax_element_end_line', 'syntax_element_match'... [-Wswitch-enum]  
      switch(state->type)  
             ^  
../../../boost/regex/v4/basic_regex_creator.hpp:1083:14: warning: 10 enumeration values not explicitly handled in switch: 'syntax_element_start_line', 'syntax_element_word_boundary', 'syntax_element_within_word'... [-Wswitch-enum]  
      switch(state->type)  
             ^  
../../../boost/regex/v4/basic_regex_creator.hpp:1384:14: warning: 28 enumeration values not explicitly handled in switch: 'syntax_element_literal', 'syntax_element_end_line', 'syntax_element_wild'... [-Wswitch-enum]  
      switch(state->type)  
             ^  
../../../boost/regex/v4/basic_regex_creator.hpp:1430:11: warning: 29 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_endmark', 'syntax_element_literal'... [-Wswitch-enum]  
   switch(pt->type)  
          ^  
../../../boost/regex/v4/basic_regex_creator.hpp:1452:11: warning: 29 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_endmark', 'syntax_element_literal'... [-Wswitch-enum]  
   switch(pt->type)  
          ^  
../../../boost/regex/v4/basic_regex_creator.hpp:1480:17: warning: 30 enumeration values not explicitly handled in switch: 'syntax_element_startmark', 'syntax_element_endmark', 'syntax_element_start_line'... [-Wswitch-enum]  
         switch(state->next.p->type)  
                ^  
../../../boost/regex/v4/basic_regex_creator.hpp:1507:14: warning: 19 enumeration values not explicitly handled in switch: 'syntax_element_literal', 'syntax_element_wild', 'syntax_element_match'... [-Wswitch-enum]  
      switch(state->type)  
```  
  
These looks easy and I can make the patch if desired.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-22 18:48:29 UTC  
> Url: https://github.com/boostorg/regex/issues/84#issuecomment-577329857  

Suggested fix?  
  
Note that the switch statements do have `default:` statements, so these are correct as they are IMO.  I don't want to explicitly list out all the values, as that risks a breakage as and when additional values are added (and are no longer handled by a `default:`.
