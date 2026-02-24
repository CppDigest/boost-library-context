# #49 - Consider adding support for constructing `class` types from tuples, as a generalization of the tuple -> aggregate construction we do now [Closed]

> Username: tzlaine  
> Created at: 2024-01-05 05:37:51 UTC  
> Updated at: 2024-01-20 03:15:09 UTC  
> Closed at: 2024-01-20 03:15:09 UTC  
> Url: https://github.com/boostorg/parser/issues/49  

Right now, if you use a rule parser with an aggregate `struct` attribute type `S` and a seq_parser for its definition, it will parse the individual attributes of the seq_parser's subparsers directly into the members of `S`.  
  
It might be nice to support construction of non-aggregate `struct`s, too.  This would avoid the need to write a semantic action just to take ctor args and construct a rule's attribute.  
  
For instance:  
  
```c++  
struct foo {  
    foo(int i, std::string const & str) : i_(i), str_(str) {}  
  
    // etc.  
  
private:  
    int i_;  
    std::string str_;  
};  
  
auto parser = int_ >> lexeme[+(char_ - ws)]; // ATTR() is tuple<int, std::string>  
rule<tag, foo> foo_rule = "foo";  
```  
  
It sould be pretty cool if we could not have to write a semantic action here.  This is really only worth it if we don't break all the other magic that we already have in place....
