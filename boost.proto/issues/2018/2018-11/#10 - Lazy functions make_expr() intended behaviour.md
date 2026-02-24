# #10 - Lazy functions make_expr() intended behaviour ? [Open]

> Username: praveen-velliengiri  
> Created at: 2018-11-06 16:34:36 UTC  
> Updated at: 2018-11-06 16:34:36 UTC  
> Url: https://github.com/boostorg/proto/issues/10  

Hi   
  
```cpp  
using namespace boost;  
template<int Exp>  
struct pow_fuc  
{  
    using result_type = double;  
    result_type operator()()  
    {  
        return std::pow(2,Exp);  
    }  
};  
template<int Exp,typename Arg>  
typename proto::result_of::make_expr <proto::tag::function,  
                                      pow_fuc<Exp>,  
                                      const Arg&>::type  
pow(const Arg& c)  
{  
  return proto::make_expr<proto::tag::function>(pow_fuc<Exp>(),boost::ref(c));  
}  
int main()  
{  
    auto pi = proto::lit(3.17);  
    pow<3>(pi);  
    return 0;  
}  
```  
  
The function call operator of pow_fuc doesn't receive any arguments, but while constructing the expression node for pow function, I'm passing the argument to the function call operator   
```cpp  
  return proto::make_expr<proto::tag::function>(pow_fuc<Exp>(),boost::ref(c));    
```  
My compiler doesn't throws any error messages and compiles fine. I don't know exactly whether this is the intended behaviour of proto. If it is a expected behaviour why it is so ?   
I'm believing that child expr nodes are captured by reference and used as the argument for pow_fuc call operator. Whether my assumption is wrong ? If wrong, Can you please explain me   
Thank you very much :)
