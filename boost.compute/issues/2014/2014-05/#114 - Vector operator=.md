# #114 - Vector operator= [Closed]

> Username: jpola  
> Created at: 2014-05-17 20:33:38 UTC  
> Updated at: 2014-05-18 14:22:25 UTC  
> Closed at: 2014-05-18 14:22:25 UTC  
> Url: https://github.com/boostorg/compute/issues/114  

Hello,   
  
I've just started to using compute library and I have question about compute::vector type.  
Please take a look on this code:  
class Matrix  
{  
  Matrix(compute::command_queue& queue)  
  {  
       auto ctx = queue.get_context();  
       indices = IdxVector(ctx);  
  }  
  
private:  
      compute::vector<int> indices;  
}  
  
Could you please tell me how to correctly initialize indices field with a given queue or pass the context to indices vector? I saw in the code that vector's operator= does not allow to pass context and takes it from default_queue.  
    vector<T>& operator=(const vector<T> &other)  
    {  
        if(this != &other){  
            command_queue queue = default_queue();  
            resize(other.size(), queue);  
            ::boost::compute::copy(other.begin(), other.end(), begin(), queue);  
            queue.finish();  
        }  
        return *this;  
    }  
  
Thank you very much.  
Kuba.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-17 21:14:41 UTC  
> Updated at: 2014-05-17 21:15:00 UTC  
> Url: https://github.com/boostorg/compute/issues/114#issuecomment-43424332  

Hi Kuba,  
  
If I understand you correctly, that depends on what you want to initialize the vector with. If you just want to allocate memory for the buffer in a particular context you should do something like this:  
  
```  
Matrix(compute::command_queue &queue)  
    : indicies(100, queue.get_context())  
{  
}  
```  
  
If you want to allocate memory and copy the values from another vector (either a `std::vector` on the host or a `compute::vector` on the device) you can use the constructor which takes an iterator range:  
  
```  
Matrix(compute::command_queue &queue)  
    : indicies(some_vector.begin(), some_vector.end(), queue)  
{  
}  
```  
  
Alternatively, if indices is already initialized and you just want to resize and assign it values (only resize if neccessary):  
  
```  
indicices.resize(some_vector.size(), queue);  
compute::copy(some_vector.begin(), some_vector.end(), inidices.begin(), queue);  
```  
  
Hopefully that helps. Let me know if you're trying to do something different.

---

## Comment 2

> Username: jpola  
> Created at: 2014-05-17 21:24:52 UTC  
> Url: https://github.com/boostorg/compute/issues/114#issuecomment-43424557  

Thanks Kyle for the fast answer. I think that is what I wanted to know.  
I'm currently writing some GPU algorithms related to the sparse linear systems. I want to compare the performance in comparison to Bolt library. I will post results when I'll finish.  
  
Kuba.
