# #846 - Why I cannot get result from boost::compute::function  make_function_from_source [Open]

> Username: Baiyun-u-smartAI  
> Created at: 2020-03-14 04:36:45 UTC  
> Updated at: 2020-03-14 04:36:45 UTC  
> Url: https://github.com/boostorg/compute/issues/846  

`using namespace boost;  
  
    using compute::int_;  
    // get default device and setup context  
    compute::device device = compute::system::default_device();  
    compute::context context(device);  
    compute::command_queue queue(context, device);  
  
    // point coordinates  
    int a[]={2};  
  
    // create vector for five points  
    compute::vector<int_> vector(1, context);  
  
    // copy point data to the device  
    compute::copy(  
            reinterpret_cast<int *>(a),  
            reinterpret_cast<int *>(a) + 1,  
            vector.begin(),  
            queue  
    );  
  
    compute::vector<int_> out(1, context);  
    boost::compute::function<int (int)> add_four =  
            boost::compute::make_function_from_source<int (int)>(  
                    "add_four",  
                    "int add_four(int x) { return x + 4; }"  
            );  
  
    boost::compute::transform(vector.begin(), vector.end(), out.begin(), add_four, queue);  
    cout<<out[0];`
