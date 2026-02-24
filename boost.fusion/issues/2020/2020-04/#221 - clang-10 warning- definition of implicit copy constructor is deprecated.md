# #221 - clang-10 warning: definition of implicit copy constructor is deprecated [Closed]

> Username: k15tfu  
> Created at: 2020-04-20 12:37:14 UTC  
> Updated at: 2020-10-06 01:20:18 UTC  
> Closed at: 2020-10-06 01:20:18 UTC  
> Url: https://github.com/boostorg/fusion/issues/221  

In boost/fusion/view/filter_view/filter_view.hpp:  
```  
boost/fusion/view/filter_view/filter_view.hpp:62:22: error: definition of implicit copy constructor for 'filter_view<const boost::fusion::tuple<boost::filesystem::path &, boost::process::detail::arg_setter_<char, false> &, boost::process::basic_environment<char> &>, boost::process::detail::is_initializer<mpl_::arg<-1> > >' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        filter_view& operator= (filter_view const&);  
```  
  
In boost/fusion/container/vector/vector_iterator.hpp:  
```  
boost/fusion/container/vector/vector_iterator.hpp:48:26: error: definition of implicit copy constructor for 'vector_iterator<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char>, boost::process::detail::posix::env_init<char> >, 0>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        vector_iterator& operator= (vector_iterator const&);  
```  
  
In boost/fusion/view/filter_view/filter_view_iterator.hpp:  
```  
boost/fusion/view/filter_view/filter_view_iterator.hpp:66:26: error: definition of implicit copy constructor for 'filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path &, boost::process::detail::arg_setter_<char, false> &, boost::process::basic_environment<char> &>, 0>, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path &, boost::process::detail::arg_setter_<char, false> &, boost::process::basic_environment<char> &>, 3>, boost::process::detail::is_initializer<mpl_::arg<-1> > >' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        filter_iterator& operator= (filter_iterator const&);  
```

---

## Comment 1

> Username: djowel  
> Created at: 2020-04-20 14:25:19 UTC  
> Url: https://github.com/boostorg/fusion/issues/221#issuecomment-616588441  

PR very welcome.
