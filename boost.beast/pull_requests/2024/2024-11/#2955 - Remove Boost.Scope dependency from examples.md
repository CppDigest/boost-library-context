# #2955 Remove Boost.Scope dependency from examples [Merged]

> Username: ashtum  
> Created at: 2024-11-26 06:54:12 UTC  
> Updated at: 2024-11-26 13:21:00 UTC  
> Merged at: 2024-11-26 13:21:00 UTC  
> Closed at: 2024-11-26 13:21:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2955  

Boost.Scope triggers a strange internal compiler error in GCC 14:  
```  
/workspace/boost/libs/asio/include/boost/asio/impl/co_spawn.hpp: In function ‘boost::asio::awaitable<boost::asio::detail::awaitable_thread_entry_point, Executor> boost::asio::detail::co_spawn_entry_point(boost::asio::awaitable<void, AwaitableExecutor>*, co_spawn_state<Handler, Executor, Function>) [with Handler = boost::asio::cancellation_slot_binder<consign_handler<listen(listen(task_group&, boost::asio::ssl::context&, boost::asio::ip::tcp::endpoint, boost::beast::string_view)::_Z6listenR10task_groupRN5boost4asio3ssl7contextENS2_2ip14basic_endpointINS6_3tcpEEENS1_4core17basic_string_viewIcEE.Frame*)::<lambda(std::__exception_ptr::exception_ptr)>, boost::scope::scope_exit<task_group::adapt<listen(listen(task_group&, boost::asio::ssl::context&, boost::asio::ip::tcp::endpoint, boost::beast::string_view)::_Z6listenR10task_groupRN5boost4asio3ssl7contextENS2_2ip14basic_endpointINS6_3tcpEEENS1_4core17basic_string_viewIcEE.Frame*)::<lambda(std::__exception_ptr::exception_ptr)> >(listen(listen(task_group&, boost::asio::ssl::context&, boost::asio::ip::tcp::endpoint, boost::beast::string_view)::_Z6listenR10task_groupRN5boost4asio3ssl7contextENS2_2ip14basic_endpointINS6_3tcpEEENS1_4core17basic_string_viewIcEE.Frame*)::<lambda(std::__exception_ptr::exception_ptr)>&&)::<lambda()>, boost::scope::always_true> >, boost::asio::cancellation_slot>; Executor = boost::asio::strand<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >; Function = awaitable_as_function<void, boost::asio::strand<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> > >]’:  
/workspace/boost/libs/asio/include/boost/asio/impl/co_spawn.hpp:226:1: internal compiler error: in gimplify_var_or_parm_decl, at gimplify.cc:3308  
  226 | }  
      | ^  
0x1431e4c internal_error(char const*, ...)  
        ???:0  
0x1431f22 fancy_abort(char const*, int, char const*)  
        ???:0  
0x159c676 gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159c6ca gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159c08e gimplify_stmt(tree_node**, gimple**)  
        ???:0  
0x159dd72 gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d910 gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d2da gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d31b gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d3e5 gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d31b gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159c08e gimplify_stmt(tree_node**, gimple**)  
        ???:0  
0x159ce0d gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d31b gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x159d3e5 gimplify_expr(tree_node**, gimple**, gimple**, bool (*)(tree_node*), int)  
        ???:0  
0x1593e1b gimplify_body(tree_node*, bool)  
        ???:0  
0x15922bc gimplify_function_tree(tree_node*)  
        ???:0  
0x15918af cgraph_node::analyze()  
        ???:0  
0x1b06681 symbol_table::finalize_compilation_unit()  
        ???:0  
Please submit a full bug report, with preprocessed source (by using -freport-bug).  
```

---
