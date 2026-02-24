# #229 - Error C2672: 'equal_range_dispatch': no matching overloaded function found [Open]

> Username: Levi-Armstrong  
> Created at: 2020-10-16 02:46:26 UTC  
> Updated at: 2020-10-16 16:19:38 UTC  
> Url: https://github.com/boostorg/graph/issues/229  

Hello,  
  
I am currently in the process of getting an existing code base which builds fine on linux to build windows and I have ran into an issues with a section of code which leverages boost graph. I have also attached an example file which is almost an exact copy from here [header](https://github.com/ros-industrial-consortium/tesseract/blob/feature/CommandLanguage/tesseract/tesseract_scene_graph/include/tesseract_scene_graph/graph.h) and [source](https://github.com/ros-industrial-consortium/tesseract/blob/feature/CommandLanguage/tesseract/tesseract_scene_graph/src/graph.cpp). I need help understanding why it builds fine on Ubuntu but not on Windows; does anyone have any ideas to what might be the issue?  
  
``` bash  
2020-10-15T21:07:17.5155301Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/pending/container_traits.hpp(684): error C2672: 'equal_range_dispatch': no matching overloaded function found  
2020-10-15T21:07:17.5158203Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/graph/detail/adjacency_list.hpp(1670): note: see reference to function template instantiation 'std::pair<std::_List_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,std::_List_iterator<std::_List_val<std::_List_simple_types<_Ty>>>> boost::graph_detail::equal_range<std::list<_Ty,std::allocator<_Ty>>,StoredEdge>(Container &,const Value &)' being compiled  
2020-10-15T21:07:17.5159983Z         with  
2020-10-15T21:07:17.5165372Z         [  
2020-10-15T21:07:17.5166571Z             _Ty=boost::detail::stored_edge_iter<void *,std::_List_iterator<std::_List_val<std::_List_simple_types<boost::list_edge<void *,tesseract_scene_graph::EdgeProperty>>>>,tesseract_scene_graph::EdgeProperty>,  
2020-10-15T21:07:17.5169102Z             Container=std::list<boost::detail::stored_edge_iter<void *,std::_List_iterator<std::_List_val<std::_List_simple_types<boost::list_edge<void *,tesseract_scene_graph::EdgeProperty>>>>,tesseract_scene_graph::EdgeProperty>,std::allocator<boost::detail::stored_edge_iter<void *,std::_List_iterator<std::_List_val<std::_List_simple_types<boost::list_edge<void *,tesseract_scene_graph::EdgeProperty>>>>,tesseract_scene_graph::EdgeProperty>>>,  
2020-10-15T21:07:17.5172043Z             Value=StoredEdge  
2020-10-15T21:07:17.5174927Z         ]  
2020-10-15T21:07:17.5176611Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/graph/detail/adjacency_list.hpp(1245): note: see reference to function template instantiation 'std::pair<boost::detail::out_edge_iter<std::_List_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,void *,boost::detail::edge_desc_impl<boost::bidirectional_tag,void *>,__int64>,boost::detail::out_edge_iter<std::_List_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,void *,boost::detail::edge_desc_impl<boost::bidirectional_tag,void *>,__int64>> boost::edge_range<Config,Base>(void *,void *,const boost::adj_list_helper<Config,Base> &)' being compiled  
2020-10-15T21:07:17.5178312Z         with  
2020-10-15T21:07:17.5178518Z         [  
2020-10-15T21:07:17.5179338Z             _Ty=boost::detail::stored_edge_iter<void *,std::_List_iterator<std::_List_val<std::_List_simple_types<boost::list_edge<void *,tesseract_scene_graph::EdgeProperty>>>>,tesseract_scene_graph::EdgeProperty>,  
2020-10-15T21:07:17.5182114Z             Config=boost::detail::adj_list_gen<boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>::config,  
2020-10-15T21:07:17.5186458Z             Base=boost::bidirectional_graph_helper_with_property<boost::detail::adj_list_gen<boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>::config>  
2020-10-15T21:07:17.5188989Z         ]  
2020-10-15T21:07:17.5194902Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/graph/detail/adjacency_list.hpp(1244): note: while compiling class template member function 'std::pair<boost::detail::out_edge_iter<std::_List_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,void *,boost::detail::edge_desc_impl<boost::bidirectional_tag,void *>,__int64>,boost::detail::out_edge_iter<std::_List_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,void *,boost::detail::edge_desc_impl<boost::bidirectional_tag,void *>,__int64>> boost::bidirectional_graph_helper_with_property<boost::detail::adj_list_gen<boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,VertexListS,OutEdgeListS,DirectedS,VertexProperty,EdgeProperty,GraphProperty,EdgeListS>::config>::get_parallel_edge_sublist(boost::detail::edge_desc_impl<boost::bidirectional_tag,void *>,const boost::adjacency_list<OutEdgeListS,VertexListS,DirectedS,VertexProperty,EdgeProperty,GraphProperty,EdgeListS> &,boost::hash_setS *)'  
2020-10-15T21:07:17.5200790Z         with  
2020-10-15T21:07:17.5200998Z         [  
2020-10-15T21:07:17.5201709Z             _Ty=boost::detail::stored_edge_iter<void *,std::_List_iterator<std::_List_val<std::_List_simple_types<boost::list_edge<void *,tesseract_scene_graph::EdgeProperty>>>>,tesseract_scene_graph::EdgeProperty>,  
2020-10-15T21:07:17.5202476Z             VertexListS=boost::listS,  
2020-10-15T21:07:17.5202797Z             OutEdgeListS=boost::listS,  
2020-10-15T21:07:17.5203193Z             DirectedS=boost::bidirectionalS,  
2020-10-15T21:07:17.5203702Z             VertexProperty=tesseract_scene_graph::VertexProperty,  
2020-10-15T21:07:17.5204265Z             EdgeProperty=tesseract_scene_graph::EdgeProperty,  
2020-10-15T21:07:17.5204810Z             GraphProperty=tesseract_scene_graph::GraphProperty,  
2020-10-15T21:07:17.5205249Z             EdgeListS=boost::listS  
2020-10-15T21:07:17.5205510Z         ]  
2020-10-15T21:07:17.5208872Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/graph/detail/adjacency_list.hpp(1538): note: see reference to class template instantiation 'boost::bidirectional_graph_helper_with_property<boost::detail::adj_list_gen<boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,VertexListS,OutEdgeListS,DirectedS,VertexProperty,EdgeProperty,GraphProperty,EdgeListS>::config>' being compiled  
2020-10-15T21:07:17.5212234Z         with  
2020-10-15T21:07:17.5212439Z         [  
2020-10-15T21:07:17.5212711Z             VertexListS=boost::listS,  
2020-10-15T21:07:17.5213076Z             OutEdgeListS=boost::listS,  
2020-10-15T21:07:17.5213475Z             DirectedS=boost::bidirectionalS,  
2020-10-15T21:07:17.5214093Z             VertexProperty=tesseract_scene_graph::VertexProperty,  
2020-10-15T21:07:17.5214645Z             EdgeProperty=tesseract_scene_graph::EdgeProperty,  
2020-10-15T21:07:17.5215174Z             GraphProperty=tesseract_scene_graph::GraphProperty,  
2020-10-15T21:07:17.5215600Z             EdgeListS=boost::listS  
2020-10-15T21:07:17.5215851Z         ]  
2020-10-15T21:07:17.5216578Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/graph/detail/adjacency_list.hpp(1788): note: see reference to class template instantiation 'boost::adj_list_helper<Config,Base>' being compiled  
2020-10-15T21:07:17.5217469Z         with  
2020-10-15T21:07:17.5217675Z         [  
2020-10-15T21:07:17.5219811Z             Config=boost::detail::adj_list_gen<boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>::config,  
2020-10-15T21:07:17.5224241Z             Base=boost::bidirectional_graph_helper_with_property<boost::detail::adj_list_gen<boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>::config>  
2020-10-15T21:07:17.5226174Z         ]  
2020-10-15T21:07:17.5231445Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/graph/adjacency_list.hpp(270): note: see reference to class template instantiation 'boost::adj_list_impl<Graph,boost::detail::adj_list_gen<Graph,VertexListS,OutEdgeListS,DirectedS,VertexProperty,EdgeProperty,GraphProperty,EdgeListS>::config,boost::bidirectional_graph_helper_with_property<boost::detail::adj_list_gen<Graph,VertexListS,OutEdgeListS,DirectedS,VertexProperty,EdgeProperty,GraphProperty,EdgeListS>::config>>' being compiled  
2020-10-15T21:07:17.5236545Z         with  
2020-10-15T21:07:17.5236767Z         [  
2020-10-15T21:07:17.5237879Z             Graph=boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>,  
2020-10-15T21:07:17.5239035Z             VertexListS=boost::listS,  
2020-10-15T21:07:17.5239400Z             OutEdgeListS=boost::listS,  
2020-10-15T21:07:17.5239755Z             DirectedS=boost::bidirectionalS,  
2020-10-15T21:07:17.5240498Z             VertexProperty=tesseract_scene_graph::VertexProperty,  
2020-10-15T21:07:17.5241083Z             EdgeProperty=tesseract_scene_graph::EdgeProperty,  
2020-10-15T21:07:17.5241802Z             GraphProperty=tesseract_scene_graph::GraphProperty,  
2020-10-15T21:07:17.5242245Z             EdgeListS=boost::listS  
2020-10-15T21:07:17.5242499Z         ]  
2020-10-15T21:07:17.5245169Z D:\a\tesseract\tesseract\src\tesseract\tesseract\tesseract_scene_graph\include\tesseract_scene_graph/graph.h(99): note: see reference to class template instantiation 'boost::adjacency_list<boost::listS,boost::listS,boost::bidirectionalS,tesseract_scene_graph::VertexProperty,tesseract_scene_graph::EdgeProperty,tesseract_scene_graph::GraphProperty,boost::listS>' being compiled  
2020-10-15T21:07:17.5249707Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/pending/container_traits.hpp(685): error C2440: 'initializing': cannot convert from 'boost::graph_detail::list_tag' to 'boost::graph_detail::associative_container_tag'  
2020-10-15T21:07:17.5251982Z C:\opt\ros\noetic\x64\share\orocos_kdl\..\..\include\boost/pending/container_traits.hpp(685): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
2020-10-15T21:07:18.1313235Z NMAKE : fatal error U1077: 'C:\PROGRA~2\MICROS~1\2019\ENTERP~1\VC\Tools\MSVC\1427~1.291\bin\Hostx64\x64\cl.exe' : return code '0x2'  
2020-10-15T21:07:18.1313715Z Stop.  
2020-10-15T21:07:18.1314823Z NMAKE : fatal error U1077: '"C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.27.29110\bin\HostX64\x64\nmake.exe"' : return code '0x2'  
2020-10-15T21:07:18.1315608Z Stop.  
2020-10-15T21:07:18.1316230Z NMAKE : fatal error U1077: '"C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.27.29110\bin\HostX64\x64\nmake.exe"' : return code   
```  
  
  
[Compiler Explorer C++ Editor #1 Code (3).txt](https://github.com/boostorg/graph/files/5388861/Compiler.Explorer.C%2B%2B.Editor.1.Code.3.txt)

---

## Comment 1

> Username: Levi-Armstrong  
> Created at: 2020-10-16 04:35:26 UTC  
> Url: https://github.com/boostorg/graph/issues/229#issuecomment-709756525  

After trying multiple different things I found a solution to allow it to build on windows. Switching the `OutEdgeListS` from `boost::listS` to `boost::hash_mapS` solved the issues. The reason I tried this is because the last error stated it that `list_tag` could not be converted to `associative_container_tag` and after looking through the boost code I noticed that it `listS` tag did not derive from `associative_container_tag` but `hash_mapS` did. So the question I have is should `listS` work or is it truly not a valid type for the bidirectiona graph?  
  
before:  
  
```  
using Graph = boost::adjacency_list<boost::listS, boost::listS, boost::bidirectionalS, VertexProperty, EdgeProperty, GraphProperty>;  
```  
  
After:  
  
```  
using Graph = boost:: adjacency_list<boost::hash_mapS, boost::listS, boost::bidirectionalS, VertexProperty, EdgeProperty, GraphProperty>;  
```

---

## Comment 2

> Username: jakobandersen  
> Created at: 2020-10-16 08:10:44 UTC  
> Url: https://github.com/boostorg/graph/issues/229#issuecomment-709896608  

I'm not familiar with building on Windows, though if it works with GCC and Clang it could be a bug in the MS compiler. For those that may be able to answer more concretely, it may be helpful with the specific Boost version and compiler version you are trying.  
  
I don't think switching the out-edge list container should not be done lightly. They offer different guarantees that the rest of the code may rely on. E.g., if I remember correctly, then ``listS`` means you have iterator stability during additions/deletions, while ``hash_mapS`` does not. On the other hand, ``hash_mapS`` gives access to the ``edge_range`` function.

---

## Comment 3

> Username: Levi-Armstrong  
> Created at: 2020-10-16 15:34:16 UTC  
> Url: https://github.com/boostorg/graph/issues/229#issuecomment-710118973  

> I'm not familiar with building on Windows, though if it works with GCC and Clang it could be a bug in the MS compiler. For those that may be able to answer more concretely, it may be helpful with the specific Boost version and compiler version you are trying.  
  
I will try out clang compiler on linux and see if there are any issues. The other requested information is below.  
  
- Boost Version tested  
  - 1.66  
  - 1.73  
- Windows Compiler  
  - The C compiler identification is MSVC 19.27.29112.0  
    - C:/Program Files (x86)/Microsoft Visual Studio/2019/Enterprise/VC/Tools/MSVC/14.27.29110/bin/Hostx64/x64/cl.exe  
  - The CXX compiler identification is MSVC 19.27.29112.0  
    - C:/Program Files (x86)/Microsoft Visual Studio/2019/Enterprise/VC/Tools/MSVC/14.27.29110/bin/Hostx64/x64/cl.exe

---

## Comment 4

> Username: Levi-Armstrong  
> Created at: 2020-10-16 15:36:21 UTC  
> Url: https://github.com/boostorg/graph/issues/229#issuecomment-710120176  

> I don't think switching the out-edge list container should not be done lightly. They offer different guarantees that the rest of the code may rely on. E.g., if I remember correctly, then `listS` means you have iterator stability during additions/deletions, while `hash_mapS` does not. On the other hand, `hash_mapS` gives access to the `edge_range` function.  
  
Thanks for the information. I agree and currently the code is not functional because of the issue you stated related to the iterator stability when using hash_mapS.

---

## Comment 5

> Username: Levi-Armstrong  
> Created at: 2020-10-16 16:19:37 UTC  
> Url: https://github.com/boostorg/graph/issues/229#issuecomment-710166333  

I have also created this [repository](https://github.com/Levi-Armstrong/boost_graph_issue) to isolate the code for those to test.
