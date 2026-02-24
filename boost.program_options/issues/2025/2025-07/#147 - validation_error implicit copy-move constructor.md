# #147 - validation_error implicit copy/move constructor [Open]

> Username: bmagistro  
> Created at: 2025-07-29 16:54:22 UTC  
> Updated at: 2025-07-29 16:54:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/147  

We had a number of program options , most `uint*_t` and one additional that is a `vector<string>` using `mutlitoken` ) that began erroring.  This was working, but post updates to boost 1.88.0 (prior 1.75.0) + clang 19.1.7, it now produces an error. We have opted to throw some push/pops around this in our code right now to suppress the issue but would like to address it with a more appropriate fix.  Included below is the warning for one of the numeric options.  We did not investigate these as much as we did the arguments_map ( https://github.com/boostorg/program_options/issues/146 ), but would believe this one is better handled by adding default constructors (this might even be a case where `BOOST_DEFAULTED_FUNCTION` should be updated) as nothing jumps out as being a pointer where special handling may be required but would like those with more knowledge of the library to weigh in.  
  
```  
In file included from /home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options.hpp:15:  
In file included from /home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/options_description.hpp:12:  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/errors.hpp:383:34: error: definition of implicit copy constructor for 'validation_error' is deprecated because it has a user-declared destructor [-Werror,-Wdeprecated-copy-with-dtor]  
  383 |         BOOST_DEFAULTED_FUNCTION(~validation_error() BOOST_NOEXCEPT_OR_NOTHROW, {})  
      |                                  ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/throw_exception/include/boost/throw_exception.hpp:97:41: note: in implicit copy constructor for 'boost::program_options::validation_error' first required here  
   97 |     explicit wrapexcept( E const & e ): E( e )  
      |                                         ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/throw_exception/include/boost/throw_exception.hpp:165:11: note: in instantiation of member function 'boost::wrapexcept<boost::program_options::validation_error>::wrapexcept' requested here  
  165 |     throw wrapexcept<E>( e );  
      |           ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/detail/value_semantic.hpp:62:24: note: in instantiation of function template specialization 'boost::throw_exception<boost::program_options::validation_error>' requested here  
   62 |                 boost::throw_exception(validation_error(validation_error::multiple_values_not_allowed));  
      |                        ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/detail/value_semantic.hpp:90:48: note: in instantiation of function template specialization 'boost::program_options::validators::get_single_string<char>' requested here  
   90 |         std::basic_string<charT> s(validators::get_single_string(xs));  
      |                                                ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/detail/value_semantic.hpp:184:13: note: in instantiation of function template specialization 'boost::program_options::validate<unsigned short, char>' requested here  
  184 |             validate(value_store, new_tokens, static_cast<T*>(nullptr), 0);  
      |             ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/value_semantic.hpp:189:9: note: in instantiation of member function 'boost::program_options::typed_value<unsigned short>::xparse' requested here  
  189 |         typed_value(T* store_to)   
      |         ^  
/home/user/bitbucket/app/build/_deps/boost-src/libs/program_options/include/boost/program_options/detail/value_semantic.hpp:199:33: note: in instantiation of member function 'boost::program_options::typed_value<unsigned short>::typed_value' requested here  
  199 |         typed_value<T>* r = new typed_value<T>(v);  
      |                                 ^  
/home/user/bitbucket/app/apps/app_name/src/main.cpp:111:49: note: in instantiation of function template specialization 'boost::program_options::value<unsigned short>' requested here  
  111 |         ("listen_port", boost::program_options::value<uint16_t>(&opt_listen_port)->value_name("LISTEN_PORT")->required(),"Port to listen on")  
      |                                                 ^  
```  
  
https://quuxplusone.github.io/blog/2023/05/05/deprecated-copy-with-dtor/  
https://stackoverflow.com/a/51864979
