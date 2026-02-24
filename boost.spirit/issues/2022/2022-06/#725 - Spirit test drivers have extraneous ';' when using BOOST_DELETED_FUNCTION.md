# #725 - Spirit test drivers have extraneous ';' when using BOOST_DELETED_FUNCTION [Closed]

> Username: mclow  
> Created at: 2022-06-01 00:24:07 UTC  
> Updated at: 2022-06-01 02:22:48 UTC  
> Closed at: 2022-06-01 02:22:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/725  

libs/spirit/test/lex/dedent_handling_phoenix.cpp:78:     BOOST_DELETED_FUNCTION(dumper& operator= (dumper const&));  
libs/spirit/test/lex/matlib.h:40:     BOOST_DELETED_FUNCTION(store_double& operator= (store_double const&));  
libs/spirit/test/lex/matlib.h:61:     BOOST_DELETED_FUNCTION(add_row& operator= (add_row const&));  
libs/spirit/test/lex/set_token_value.cpp:83:     BOOST_DELETED_FUNCTION(handle_whitespace& operator= (handle_whitespace const&));  
libs/spirit/test/qi/terminal_ex.cpp:72:         BOOST_DELETED_FUNCTION(ops_1_parser& operator= (ops_1_parser const&));  
libs/spirit/test/qi/terminal_ex.cpp:124:         BOOST_DELETED_FUNCTION(ops_2_parser& operator= (ops_2_parser const&));  
libs/spirit/test/qi/terminal_ex.cpp:181:         BOOST_DELETED_FUNCTION(ops_3_parser& operator= (ops_3_parser const&));  
  
See https://github.com/boostorg/iostreams/issues/147 for a similar report against iostreams.

---

## Comment 1

> Username: djowel  
> Created at: 2022-06-01 00:58:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/725#issuecomment-1142930387  

Would you care to do a PR?

---

## Comment 2

> Username: mclow  
> Created at: 2022-06-01 01:37:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/725#issuecomment-1143014572  

I could do a PR, but the fix is just to remove the trailing ';' from each of those lines.  Your call.

---

## Comment 3

> Username: djowel  
> Created at: 2022-06-01 02:03:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/725#issuecomment-1143035705  

I'll do it. Thanks, Marshall!
