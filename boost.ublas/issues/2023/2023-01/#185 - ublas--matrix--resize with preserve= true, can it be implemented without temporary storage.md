# #185 - ublas::matrix::resize with preserve= true, can it be implemented without temporary storage? [Open]

> Username: dmenendez-gruposantander  
> Created at: 2023-01-02 14:11:09 UTC  
> Updated at: 2023-01-02 14:11:09 UTC  
> Url: https://github.com/boostorg/ublas/issues/185  

For ublas::matrix::resize(), when passing preserve=true (which is the default) the implementation is using temporary space to save the data that needs to be preserved.  
  
I was wondering if this can be done without needing the temporary storage... and I think it is!  
  
Do you think it is worth the extra code complexity?  
Would you be willing to accept a PR?
