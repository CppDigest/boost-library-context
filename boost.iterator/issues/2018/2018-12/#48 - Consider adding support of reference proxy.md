# #48 - [Feature request] Consider adding support of reference proxy [Open]

> Username: tower120  
> Created at: 2018-12-01 14:45:49 UTC  
> Updated at: 2018-12-01 14:45:49 UTC  
> Url: https://github.com/boostorg/iterator/issues/48  

Continuing https://github.com/boostorg/iterator/issues/47.   
Currently, `iterator_facade` does not allow to have RandomAcess iterator with reference proxy (aka `std::vector<bool>::iterator::reference`) .   
  
It looks like that the _only_ thing that prevent this - is iterator-category deduction/override rules https://www.boost.org/doc/libs/1_68_0/libs/iterator/doc/iterator_facade.html :   
  
```  
iterator-category(C,R,V) :=  
   if (C is convertible to std::input_iterator_tag  
       || C is convertible to std::output_iterator_tag  
   )  
       return C  
  
   else if (C is not convertible to incrementable_traversal_tag)  
       the program is ill-formed  
  
   else return a type X satisfying the following two constraints:  
  
      1. X is convertible to X1, and not to any more-derived  
         type, where X1 is defined by:  
  
           if (R is a reference type  
               && C is convertible to forward_traversal_tag)  
           {  
               if (C is convertible to random_access_traversal_tag)  
                   X1 = random_access_iterator_tag  
               else if (C is convertible to bidirectional_traversal_tag)  
                   X1 = bidirectional_iterator_tag  
               else  
                   X1 = forward_iterator_tag  
           }  
           else  
           {  
               if (C is convertible to single_pass_traversal_tag  
                   && R is convertible to V)  
                   X1 = input_iterator_tag  
               else  
                   X1 = C  
           }  
  
      2. category-to-traversal(X) is convertible to the most  
         derived traversal tag type to which X is also  
         convertible, and not to any more-derived traversal tag  
         type.  
```  
  
The simplest _solution_ for now, seems to override manually iterator_category in Derived class.  
  
---  
  
Maybe add some proxy tag? Like:  
```  
class node_iterator  
	: public boost::iterator_facade  
	  <  
		node_iterator  
		, std::pair<int, int>  
		, boost::bidirectional_traversal_tag  
		, boost::iterator_facade::proxy_reference<std::pair<int, int>>  
	  >  
```  
And do not downgrade iterator-category, if proxy_reference occurs.
