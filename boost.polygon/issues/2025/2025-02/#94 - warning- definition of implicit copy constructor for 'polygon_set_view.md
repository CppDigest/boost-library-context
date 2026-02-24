# #94 - warning: definition of implicit copy constructor for 'polygon_set_view [Closed]

> Username: stefanottili  
> Created at: 2025-02-08 21:58:17 UTC  
> Updated at: 2025-07-04 02:46:12 UTC  
> Closed at: 2025-07-01 02:46:57 UTC  
> Url: https://github.com/boostorg/polygon/issues/94  

Apple clang 16, using either c++11 or c++14 I get the following warning.  
What to do about it ?  
```  
In file included from /opt/homebrew/opt/boost/include/boost/polygon/polygon.hpp:81:  
In file included from /opt/homebrew/opt/boost/include/boost/polygon/polygon_set_data.hpp:1002:  
/opt/homebrew/opt/boost/include/boost/polygon/detail/polygon_set_view.hpp:137:23: warning: definition of implicit copy constructor for 'polygon_set_view<std::vector<boost::polygon::polygon_data<int>>, std::vector<boost::polygon::polygon_data<int>>, 2>' is deprecated because it has a user-provided copy assignment operator [-Wdeprecated-copy-with-user-provided-copy]  
  137 |     polygon_set_view& operator=(const polygon_set_view&);  
      |                       ^  
/opt/homebrew/opt/boost/include/boost/polygon/polygon_set_concept.hpp:364:12: note: in implicit copy constructor for 'boost::polygon::polygon_set_view<std::vector<boost::polygon::polygon_data<int>>, std::vector<boost::polygon::polygon_data<int>>, 2>' first required here  
  364 |     return polygon_set_view<geometry_type_1, geometry_type_2, 2>  
      |            ^  
```

---

## Comment 1

> Username: stefanottili  
> Created at: 2025-07-01 02:46:57 UTC  
> Updated at: 2025-07-04 02:46:12 UTC  
> Url: https://github.com/boostorg/polygon/issues/94#issuecomment-3021545641  

commenting out the lines that cause the "warning: definition ..." will silence the compiler
