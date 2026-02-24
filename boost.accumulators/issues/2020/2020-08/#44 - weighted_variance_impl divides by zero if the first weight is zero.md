# #44 - weighted_variance_impl divides by zero if the first weight is zero [Open]

> Username: jukofyork  
> Created at: 2020-08-04 23:10:14 UTC  
> Updated at: 2020-08-04 23:16:13 UTC  
> Url: https://github.com/boostorg/accumulators/issues/44  

Hi,  
  
lazy_weighted_variance_impl works fine if the first weight is zero, but weighted_variance_impl divides by zero:  
  
            if(cnt > 1)  
            {  
                extractor<MeanFeature> const some_mean = {};  
  
                result_type tmp = args[parameter::keyword<Tag>::get()] - some_mean(args);  
  
                this->weighted_variance =  
                    numeric::fdiv(this->weighted_variance * (sum_of_weights(args) - args[weight]), sum_of_weights(args))  
                  + numeric::fdiv(tmp * tmp * args[weight], sum_of_weights(args) - args[weight] );  
            }  
  
It looks like "cnt > 1" should instead check "sum_of_weights(args)>0", but I'm not sure as there is some mention of this in cosurgi's answer here:  
  
https://stackoverflow.com/questions/16761965/weighted-variance-and-weighted-standard-deviation-in-c  
  
and this change may end up breaking existing code that takes this into account...  
  
If this is left as is, perhaps the documentation needs updating as currently it just says:  
  
"Note that the sample variance is not defined for n<=1".  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost/accumulators/impl/weighted_variance_impl.html  
  
Juk
