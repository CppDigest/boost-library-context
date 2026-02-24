# #26 - Tests fail with msmpi [Open]

> Username: jzmaddock  
> Created at: 2021-05-31 17:19:39 UTC  
> Updated at: 2021-05-31 17:19:39 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/26  

When building with https://docs.microsoft.com/en-us/message-passing-interface/microsoft-mpi most of the tests fail with failed assertions, and one (distributed_connected_components_test) seems to go into an infinite loop.  
  
We could also use a way of running CI for this on Windows.
