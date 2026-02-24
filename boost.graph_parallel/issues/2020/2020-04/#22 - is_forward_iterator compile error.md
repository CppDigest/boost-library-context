# #22 - is_forward_iterator compile error [Closed]

> Username: timjbaer  
> Created at: 2020-04-25 22:01:52 UTC  
> Updated at: 2021-05-31 19:35:39 UTC  
> Closed at: 2021-05-31 19:35:39 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/22  

Tried to run the provided example code for [dijkstra shortest path](https://github.com/boostorg/graph_parallel/blob/develop/example/dijkstra_shortest_paths.cpp). It does not compile on 1.72.0 with OS X. [@27576714](https://stackoverflow.com/questions/27576714/dijkstra-shortest-paths-boost-graph-lib-1-57-0-fails) lead me to think the following might fix it:  
```  
dijkstra_shortest_paths(g, start,  
                          distance_map(boost::make_iterator_property_map(  
                          d.begin(), get(boost::vertex_index, g))));  
```  
  
But that is not the case. Below is the error message:  
  
```  
In file included from /usr/local/include/boost/graph/distributed/mpi_process_group.hpp:22:  
In file included from /usr/local/include/boost/optional.hpp:15:  
In file included from /usr/local/include/boost/optional/optional.hpp:31:  
In file included from /usr/local/include/boost/core/swap.hpp:27:  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:2495:5: error: static_assert failed due to requirement  
      '__is_forward_iterator<boost::detail::readable_pmap_iter<boost::iterators::transform_iterator<boost::detail::parallel::edge_descriptor<boost::detail::edge_desc_impl<boost::directed_tag,  
      unsigned long> >::out_generator<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>,  
      boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>,  
      boost::no_property, boost::listS> >, boost::detail::out_edge_iter<std::__1::__wrap_iter<boost::detail::stored_edge_property<unsigned long,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > > > *>, unsigned long, boost::detail::edge_desc_impl<boost::directed_tag, unsigned long>, long>, boost::use_default, boost::use_default>,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> > > >::value'  
      "std::min_element requires a ForwardIterator"  
    static_assert(__is_forward_iterator<_ForwardIterator>::value,  
    ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/__config:908:29: note: expanded from macro 'static_assert'  
# define static_assert(...) _Static_assert(__VA_ARGS__)  
                            ^              ~~~~~~~~~~~  
/usr/local/include/boost/graph/distributed/crauser_et_al_shortest_paths.hpp:518:19: note: in instantiation of function template specialization  
      'std::__1::min_element<boost::detail::readable_pmap_iter<boost::iterators::transform_iterator<boost::detail::parallel::edge_descriptor<boost::detail::edge_desc_impl<boost::directed_tag,  
      unsigned long> >::out_generator<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>,  
      boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>,  
      boost::no_property, boost::listS> >, boost::detail::out_edge_iter<std::__1::__wrap_iter<boost::detail::stored_edge_property<unsigned long,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > > > *>, unsigned long, boost::detail::edge_desc_impl<boost::directed_tag, unsigned long>, long>, boost::use_default, boost::use_default>,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> > >,  
      std::__1::less<float> >' requested here  
            *std::min_element  
                  ^  
/usr/local/include/boost/graph/distributed/crauser_et_al_shortest_paths.hpp:558:11: note: in instantiation of function template specialization  
      'boost::graph::distributed::detail::initialize_min_out_weights<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group,  
      boost::vecS, boost::defaultS>, boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float,  
      boost::no_property>, boost::no_property, boost::listS>, boost::iterator_property_map<float *, boost::local_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, unsigned long> >, float, float &>, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      std::__1::less<float> >' requested here  
  detail::initialize_min_out_weights(g, min_out_weight, weight, compare);  
          ^  
/usr/local/include/boost/graph/distributed/dijkstra_shortest_paths.hpp:68:9: note: in instantiation of function template specialization  
      'boost::graph::distributed::crauser_et_al_shortest_paths<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group,  
      boost::vecS, boost::defaultS>, boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float,  
      boost::no_property>, boost::no_property, boost::listS>, boost::dijkstra_visitor<boost::null_visitor>, boost::dummy_property_map,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short,  
      boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>, boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS> *,  
      float, float &, boost::vertex_distance_t> >, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >,  
      boost::iterator_property_map<std::__1::__wrap_iter<boost::default_color_type *>, boost::local_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, unsigned long> >, boost::default_color_type, boost::default_color_type &>, std::__1::less<float>, std::__1::plus<float>, float, float>' requested  
      here  
        crauser_et_al_shortest_paths(g, s, predecessor, distance, weight,  
        ^  
/usr/local/include/boost/graph/distributed/dijkstra_shortest_paths.hpp:119:13: note: in instantiation of function template specialization  
      'boost::graph::detail::parallel_dijkstra_impl2<boost::param_not_found>::run<boost::adjacency_list<boost::vecS,  
      boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>, boost::undirectedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>, boost::no_property, boost::listS>,  
      boost::dijkstra_visitor<boost::null_visitor>, boost::dummy_property_map, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >,  
      boost::iterator_property_map<std::__1::__wrap_iter<boost::default_color_type *>, boost::local_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, unsigned long> >, boost::default_color_type, boost::default_color_type &>, std::__1::less<float>, std::__1::plus<float>, float, float>' requested  
      here  
          ::run(g, s, predecessor, distance, lookahead, weight, index_map,  
            ^  
/usr/local/include/boost/graph/distributed/dijkstra_shortest_paths.hpp:144:9: note: in instantiation of function template specialization  
      'boost::graph::detail::parallel_dijkstra_impl<boost::param_not_found>::run_impl<boost::adjacency_list<boost::vecS,  
      boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>, boost::undirectedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>, boost::no_property, boost::listS>,  
      boost::dijkstra_visitor<boost::null_visitor>, boost::dummy_property_map, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >, boost::param_not_found,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >,  
      boost::iterator_property_map<std::__1::__wrap_iter<boost::default_color_type *>, boost::local_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, unsigned long> >, boost::default_color_type, boost::default_color_type &>, std::__1::less<float>, std::__1::plus<float>, float, float>' requested  
      here  
        run_impl(g, s, predecessor, distance, lookahead, weight, index_map,  
        ^  
/usr/local/include/boost/graph/distributed/dijkstra_shortest_paths.hpp:196:9: note: in instantiation of function template specialization  
      'boost::graph::detail::parallel_dijkstra_impl<boost::param_not_found>::run<boost::adjacency_list<boost::vecS,  
      boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>, boost::undirectedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>, boost::no_property, boost::listS>,  
      boost::dijkstra_visitor<boost::null_visitor>, boost::dummy_property_map, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >, boost::param_not_found,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >, std::__1::less<float>,  
      std::__1::plus<float>, float, float>' requested here  
      ::run(g, s, predecessor, distance,   
        ^  
/usr/local/include/boost/graph/dijkstra_shortest_paths.hpp:536:7: note: in instantiation of function template specialization  
      'boost::dijkstra_shortest_paths<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>,  
      boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>,  
      boost::no_property, boost::listS>, boost::dijkstra_visitor<boost::null_visitor>, boost::dummy_property_map,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short,  
      boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>, boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS> *,  
      float, float &, boost::vertex_distance_t> >, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >, std::__1::less<float>,  
      std::__1::plus<float>, float, float, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >, boost::vertex_distance_t, boost::no_property>' requested  
      here  
      dijkstra_shortest_paths  
      ^  
/usr/local/include/boost/graph/dijkstra_shortest_paths.hpp:567:15: note: in instantiation of function template specialization  
      'boost::detail::dijkstra_dispatch2<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>,  
      boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>,  
      boost::no_property, boost::listS>, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >,  
      boost::bgl_named_params<boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >, boost::vertex_distance_t, boost::no_property> >' requested  
      here  
      detail::dijkstra_dispatch2  
              ^  
/usr/local/include/boost/graph/dijkstra_shortest_paths.hpp:585:13: note: in instantiation of function template specialization  
      'boost::detail::dijkstra_dispatch1<boost::adjacency_list<boost::vecS, boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>,  
      boost::undirectedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>,  
      boost::no_property, boost::listS>, boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::edge_global_property_map<boost::detail::edge_desc_impl<boost::directed_tag, unsigned long> >,  
      boost::adj_list_edge_property_map<boost::directed_tag, float, const float &, unsigned long, const boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::edge_weight_t> >,  
      boost::local_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_id_map<boost::property<boost::vertex_distance_t, float, boost::no_property>, unsigned long> >,  
      boost::bgl_named_params<boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group,  
      boost::detail::parallel::global_descriptor_property_map<unsigned long>, boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>,  
      boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>,  
      boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float,  
      boost::no_property> > >, boost::no_property, boost::listS> *, float, float &, boost::vertex_distance_t> >, boost::vertex_distance_t, boost::no_property> >' requested  
      here  
    detail::dijkstra_dispatch1  
            ^  
boost_mst.cxx:75:3: note: in instantiation of function template specialization 'boost::dijkstra_shortest_paths<boost::adjacency_list<boost::vecS,  
      boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS, boost::defaultS>, boost::undirectedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_weight_t, float, boost::no_property>, boost::no_property, boost::listS>,  
      boost::parallel::distributed_property_map<boost::graph::distributed::mpi_process_group, boost::detail::parallel::global_descriptor_property_map<unsigned long>,  
      boost::vec_adj_list_vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_distance_t, float,  
      boost::no_property>, boost::property<boost::edge_locally_owned_t, bool, boost::property<boost::edge_target_processor_id_t, short,  
      boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS>, boost::adjacency_list<boost::vecS, boost::vecS,  
      boost::directedS, boost::property<boost::vertex_distance_t, float, boost::no_property>, boost::property<boost::edge_locally_owned_t, bool,  
      boost::property<boost::edge_target_processor_id_t, short, boost::property<boost::edge_weight_t, float, boost::no_property> > >, boost::no_property, boost::listS> *,  
      float, float &, boost::vertex_distance_t> >, boost::vertex_distance_t, boost::no_property>' requested here  
  dijkstra_shortest_paths(g, start,  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-05-31 19:35:39 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/22#issuecomment-851648717  

Yep.  Fixed in https://github.com/boostorg/graph_parallel/pull/28
