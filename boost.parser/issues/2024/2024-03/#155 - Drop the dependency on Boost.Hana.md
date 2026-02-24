# #155 - Drop the dependency on Boost.Hana [Closed]

> Username: akrzemi1  
> Created at: 2024-03-02 08:47:47 UTC  
> Updated at: 2024-03-25 03:54:12 UTC  
> Closed at: 2024-03-25 03:54:12 UTC  
> Url: https://github.com/boostorg/parser/issues/155  

As part of the Boost Review process I was demonstrating Boost.Parser to my colleagues at work, none of whom have had any previous exposure to Boost.Spirit. They had great reservations about the overuse of operators, and were generally overwhelmed by the amount of concepts, such as attributes, directives, actions.   
  
But I completely lost them when I said that for using actions you have to learn another Hana library (hardly anyone had an exposure to this library -- try now explaining what Hana is for to developers), do `using namespace boost::hana` and then type the funny `[0_c]`, of course combined with other types of brackets and underscores: `{_attr(ctx)[0_c], _attr(ctx)[1_c]}`. I was no longer credible, and in fact I got a moral hangover.  
  
Really, typing `std::get<0>(ctx)` is not that bad, given that programmers have to learn it anyway: `std::tuple` is a vocabulary type. Admittedly, it is more verbose, but for making things short you now have https://github.com/tzlaine/parser/issues/106. And for longer things you can do the decomposition via structured binding.
