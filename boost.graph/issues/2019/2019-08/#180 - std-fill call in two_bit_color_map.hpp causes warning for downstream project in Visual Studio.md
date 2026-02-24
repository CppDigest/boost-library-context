# #180 - std:fill call in two_bit_color_map.hpp causes warning for downstream project in Visual Studio [Closed]

> Username: jasjuang  
> Created at: 2019-08-20 17:04:00 UTC  
> Updated at: 2019-08-22 18:16:03 UTC  
> Closed at: 2019-08-22 18:16:02 UTC  
> Url: https://github.com/boostorg/graph/issues/180  

This [line](https://github.com/boostorg/graph/blob/develop/include/boost/graph/two_bit_color_map.hpp#L62) in `two_bit_color_map.hpp` I believe should be changed to   
  
```  
std::fill(data.get(), data.get() + (n + elements_per_char - 1) / elements_per_char, (unsigned char)0);  
```  
  
If my downstream project happens to include `two_bit_color_map.hpp` I will have warning messages similar to the below, and it's because `data` is `unsigned char` yet it is being filled with 0, which is `int`.  
  
```  
1>mycpp.cpp  
1>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\xutility(2903): error C2220: warning treated as error - no 'object' file generated  
1>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\xutility(2917): note: see reference to function template instantiation 'void std::_Fill_unchecked1<_FwdIt,_Ty>(_FwdIt,_FwdIt,const _Ty &,std::false_type)' being compiled  
1>        with  
1>        [  
1>            _FwdIt=unsigned char *,  
1>            _Ty=int  
1>        ]  
1>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\xutility(2925): note: see reference to function template instantiation 'void std::_Fill_unchecked<_Ty*,int>(_FwdIt,_FwdIt,const int &)' being compiled  
1>        with  
1>        [  
1>            _Ty=unsigned char,  
1>            _FwdIt=unsigned char *  
1>        ]  
1>C:\dev\vcpkg\installed\x64-windows-dev\include\boost/graph/two_bit_color_map.hpp(61): note: see reference to function template instantiation 'void std::fill<T*,int>(_FwdIt,_FwdIt,const _Ty &)' being compiled  
1>        with  
1>        [  
1>            T=unsigned char,  
1>            _FwdIt=unsigned char *,  
1>            _Ty=int  
1>        ]  
1>C:\dev\vcpkg\installed\x64-windows-dev\include\boost/graph/two_bit_color_map.hpp(57): note: while compiling class template member function 'boost::two_bit_color_map<boost::vec_adj_list_vertex_id_map<Property,unsigned __int64>>::two_bit_color_map(size_t,const IndexMap &)'  
1>        with  
1>        [  
1>            Property=CGAL::internal::MST_graph_vertex_properties<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<PointVectorPair>>>>,  
1>            IndexMap=boost::vec_adj_list_vertex_id_map<CGAL::internal::MST_graph_vertex_properties<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<PointVectorPair>>>>,unsigned __int64>  
1>        ]  
1>C:\dev\vcpkg\installed\x64-windows-dev\include\boost/graph/two_bit_color_map.hpp(100): note: see reference to function template instantiation 'boost::two_bit_color_map<boost::vec_adj_list_vertex_id_map<Property,unsigned __int64>>::two_bit_color_map(size_t,const IndexMap &)' being compiled  
1>        with  
1>        [  
1>            Property=CGAL::internal::MST_graph_vertex_properties<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<PointVectorPair>>>>,  
1>            IndexMap=boost::vec_adj_list_vertex_id_map<CGAL::internal::MST_graph_vertex_properties<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<PointVectorPair>>>>,unsigned __int64>  
1>        ]  
1>C:\dev\vcpkg\installed\x64-windows-dev\include\boost/graph/breadth_first_search.hpp(317): note: see reference to class template instantiation 'boost::two_bit_color_map<boost::vec_adj_list_vertex_id_map<Property,unsigned __int64>>' being compiled  
1>        with  
1>        [  
1>            Property=CGAL::internal::MST_graph_vertex_properties<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<PointVectorPair>>>>  
1>        ]  
1>C:\dev\vcpkg\installed\x64-windows-dev\include\boost/graph/breadth_first_search.hpp(345): note: see reference to function template instantiation 'void boost::detail::bfs_dispatch<boost::param_not_found>::apply<VertexListGraph,boost::bfs_visitor<CGAL::internal::Propagate_normal_orientation<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,NormalMap,Kernel>>,boost::graph_visitor_t,boost::no_property>(VertexListGraph &,unsigned __int64,const boost::bgl_named_params<boost::bfs_visitor<CGAL::internal::Propagate_normal_orientation<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,NormalMap,Kernel>>,boost::graph_visitor_t,boost::no_property> &,boost::param_not_found)' being compiled  
1>        with  
1>        [  
1>            VertexListGraph=MST_graph,  
1>            _Ty=PointVectorPair  
1>        ]  
1>C:\dev\vcpkg\installed\x64-windows-dev\include\CGAL/mst_orient_normals.h(706): note: see reference to function template instantiation 'void boost::breadth_first_search<MST_graph,boost::bfs_visitor<CGAL::internal::Propagate_normal_orientation<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,NormalMap,Kernel>>,boost::graph_visitor_t,boost::no_property>(const VertexListGraph &,unsigned __int64,const boost::bgl_named_params<boost::bfs_visitor<CGAL::internal::Propagate_normal_orientation<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,NormalMap,Kernel>>,boost::graph_visitor_t,boost::no_property> &)' being compiled  
1>        with  
1>        [  
1>            _Ty=PointVectorPair,  
1>            VertexListGraph=MST_graph  
1>        ]  
1>C:\Users\jasju\Desktop\test\mycpp.cpp(22): note: see reference to function template instantiation 'std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>> CGAL::mst_orient_normals<std::vector<_Ty,std::allocator<_Ty>>,CGAL::cgal_bgl_named_params<CGAL::Second_of_pair_property_map<PointVectorPair>,CGAL::internal_np::normal_t,CGAL::cgal_bgl_named_params<CGAL::First_of_pair_property_map<PointVectorPair>,CGAL::internal_np::point_t,boost::no_property>>>(PointRange &,unsigned int,const NamedParameters &)' being compiled  
1>        with  
1>        [  
1>            _Ty=PointVectorPair,  
1>            PointRange=std::vector<PointVectorPair,std::allocator<PointVectorPair>>,  
1>            NamedParameters=CGAL::cgal_bgl_named_params<CGAL::Second_of_pair_property_map<PointVectorPair>,CGAL::internal_np::normal_t,CGAL::cgal_bgl_named_params<CGAL::First_of_pair_property_map<PointVectorPair>,CGAL::internal_np::point_t,boost::no_property>>  
1>        ]  
1>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\xutility(2903): warning C4244: '=': conversion from 'const _Ty' to '_Ty', possible loss of data  
1>        with  
1>        [  
1>            _Ty=int  
1>        ]  
1>        and  
1>        [  
1>            _Ty=unsigned char  
1>        ]  
1>Done building project "Example.vcxproj" -- FAILED.  
```  
For more details on how to recreate this see [here](https://github.com/CGAL/cgal/issues/4165)

---

## Comment 1

> Username: glenfe  
> Created at: 2019-08-22 18:16:02 UTC  
> Url: https://github.com/boostorg/graph/issues/180#issuecomment-524018626  

Resolved in d3b157f4656a462beb9ca767838e69b60b393b4b
