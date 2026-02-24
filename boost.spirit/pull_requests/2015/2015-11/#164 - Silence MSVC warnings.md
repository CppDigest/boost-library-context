# #164 Silence MSVC warnings [Merged]

> Username: Lastique  
> Created at: 2015-11-15 15:57:48 UTC  
> Updated at: 2015-12-08 06:44:21 UTC  
> Merged at: 2015-11-16 02:18:58 UTC  
> Closed at: 2015-11-16 02:18:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/164  

Silence mass MSVC-14 warnings:  
  
boost/spirit/home/support/terminal.hpp(264): warning C4348: 'boost::spirit::terminalboost::spirit::tag::lit::result_helper': redefinition of default parameter: parameter 3  
boost/spirit/home/support/terminal.hpp(270): note: see declaration of 'boost::spirit::terminalboost::spirit::tag::lit::result_helper'  
boost/spirit/home/support/common_terminals.hpp(142): note: see reference to class template instantiation 'boost::spirit::terminalboost::spirit::tag::lit' being compiled  
boost/spirit/home/support/terminal.hpp(264): warning C4348: 'boost::spirit::terminalboost::spirit::tag::lit::result_helper': redefinition of default parameter: parameter 4  
boost/spirit/home/support/terminal.hpp(270): note: see declaration of 'boost::spirit::terminalboost::spirit::tag::lit::result_helper'  
  
and so on for all terminals.

---
