# #37 - End of line comments in BOOST_BIMAP_GENERATE_INDEX_BINDER_1CP macro call in set_of.hpp [Open]

> Username: pauljansen42  
> Created at: 2022-11-19 18:26:30 UTC  
> Updated at: 2022-11-19 21:53:25 UTC  
> Url: https://github.com/boostorg/bimap/issues/37  

There is a macro call  
  
BOOST_BIMAP_GENERATE_INDEX_BINDER_1CP(  
  
        // binds to  
        multi_index::ordered_unique,  
  
        // with  
        key_compare  
    )  
  
in the file boost/bimap/set_of.hpp. There is nothing wrong with that. However, if you run this in preprocess only mode for Visual Studio (which we need as input for our code checkers), it will end up in incorrect code:  
  
// binds to// withtemplate< class KeyExtractor, class Tag > struct index_bind { typedef multi_index::ordered_unique < multi_index::tag< Tag >, KeyExtractor, key_compare > type; };  
  
I know that this is a bug in Visual Studio, but it help if you can change it into  
  
BOOST_BIMAP_GENERATE_INDEX_BINDER_1CP(  
  
        /* binds to */  
        multi_index::ordered_unique,  
  
        /* with */  
        key_compare  
    )  
   
i.e. changing // comments into /* .. */ comments twice. Many thanks for your help.  
  
For my own reference this is TiCS ticket 21934.
