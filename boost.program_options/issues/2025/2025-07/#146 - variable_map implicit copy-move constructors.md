# #146 - variable_map implicit copy/move constructors [Open]

> Username: bmagistro  
> Created at: 2025-07-29 16:01:01 UTC  
> Updated at: 2025-07-29 16:01:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/146  

We had abstracted our options and parsing (add_options(), store(), and command_line_parser()) calls to a function returning variables_map.  This was working, but post updates to boost 1.88.0 (prior 1.75.0) + clang 19.1.7, this now produces an error.  At this time the function was small (~15-20 lines) so moving into main was not as big of a deal but we have others where this abstraction could be beneficial from a readability perspective.  Should the copy/move constructors be explicitly deleted due to the presence of the destructor, should explicit constructors be added to continue supporting this behavior, should the destructor be removed to allow default copy/move/destruct to be utilized?  I am inclined to to vote for option 2 if this behavior should be persisted as the base class `abstract_variables_map` has a pointer member variable https://github.com/boostorg/program_options/blob/develop/include/boost/program_options/variables_map.hpp#L138 but do not have enough knowledge to know what risks might exist or what those should look like for this library.  
  
```  
In file included from /home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options.hpp:18:  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/variables_map.hpp:111:17: error: definition of implicit copy constructor for 'abstract_variables_map' is deprecated because it has a user-provided destructor [-Werror,-Wdeprecated-copy-with-user-provided-dtor]  
  111 |         virtual ~abstract_variables_map() {}  
      |                 ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/variables_map.hpp:146:38: note: in implicit copy constructor for 'boost::program_options::abstract_variables_map' first required here  
  146 |     class BOOST_PROGRAM_OPTIONS_DECL variables_map : public abstract_variables_map,  
      |                                      ^  
/home/user/bitbucket/app/apps/app_name/src/main.cpp:59:12: note: in implicit move constructor for 'boost::program_options::variables_map' first required here  
   59 |     return in_args;  
      |            ^  
```  
  
https://quuxplusone.github.io/blog/2023/05/05/deprecated-copy-with-dtor/  
https://stackoverflow.com/a/51864979
