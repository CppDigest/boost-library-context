# #88 fix another unused local typedef [Merged]

> Username: tabe  
> Created at: 2015-05-01 05:04:11 UTC  
> Updated at: 2015-05-01 05:28:57 UTC  
> Merged at: 2015-05-01 05:28:57 UTC  
> Closed at: 2015-05-01 05:28:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/88  

Found another -Wunused-local-typedefs:  
  
```  
/home/tabe/isopt/boost_1_58_0/include/boost/spirit/home/lex/argument.hpp: In member function ‘void boost::spirit::lex::state_setter< <template-parameter-1-1> >::eval(const Env&) const’:  
/home/tabe/isopt/boost_1_58_0/include/boost/spirit/home/lex/argument.hpp:104:60: warning: typedef ‘string’ locally defined but not used [-Wunused-local-typedefs]  
             typedef typename context_type::state_name_type string;  
                                                            ^  
```

---
