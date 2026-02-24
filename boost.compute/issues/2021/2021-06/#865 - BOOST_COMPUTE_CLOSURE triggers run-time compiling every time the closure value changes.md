# #865 - BOOST_COMPUTE_CLOSURE triggers run-time compiling every time the closure value changes [Open]

> Username: ozpavel  
> Created at: 2021-06-24 02:21:19 UTC  
> Updated at: 2021-06-24 02:21:19 UTC  
> Url: https://github.com/boostorg/compute/issues/865  

Just wondering if this is a bug or a user error. The below is a snippet showing the issue. Trying to pass different run-time value to closure, that triggers the run-time compiling every time the transform() is called.  
  
If this is user error or not supported, is there a workaround for supplying run-time values to algorithms?  
  
  boost::compute::device gpu =  
      boost::compute::system::default_device();  
  
  boost::compute::context context(gpu);  
  
  boost::compute::command_queue queue(context, gpu);  
  
  size_t size = 10000;  
  boost::compute::vector<int> va(size, 1, queue);  
  boost::compute::vector<int> vb(size, 2, queue);  
  boost::compute::vector<int> vc(size, context);  
  
  size_t N = 100;  
  for (size_t i = 0; i < N-1; ++i)  
  {  
    int e = static_cast<int>(i);  // changing the 'e' here causing the transform() to compile every time is invoked  
  
    BOOST_COMPUTE_CLOSURE(int, calc, (int a, int b), (e),  
    {  
        return a + b + e;  
    });  
  
    boost::compute::transform(  
          va.begin(),  
          va.end(),  
          vb.begin(),  
          vc.begin(),  
          calc,  
          queue);  
  }  
  
Cheers
