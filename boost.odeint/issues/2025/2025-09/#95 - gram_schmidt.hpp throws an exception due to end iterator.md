# #95 - gram_schmidt.hpp throws an exception due to end iterator [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 19:01:17 UTC  
> Updated at: 2025-12-02 12:07:46 UTC  
> Closed at: 2025-12-02 12:07:46 UTC  
> Url: https://github.com/boostorg/odeint/issues/95  

[This code](https://github.com/boostorg/odeint/blob/91a4a916fa193b02bda2ffc3920953387acae962/examples/gram_schmidt.hpp#L58):  
```  
    beg1 += n;  
    end1 += n;  
  
    for( size_t j=1 ; j<num_of_lyap ; ++j , beg1+=n , end1+=n )  
    {  
        for( size_t k=0 ; k<j ; ++k )  
        {  
            tmp[k] = std::inner_product( beg1 , end1 , first + k*n , 0.0 );  
            //  clog << j << " " << k << " " << tmp[k] << "\n";  
        }  
  
  
  
        for( size_t k=0 ; k<j ; ++k )  
            substract_vector( beg1 , end1 , first + k*n , tmp[k] );  
  
        // normalize j-th vector  
        norm[j] = sqrt( std::inner_product( beg1 , end1 , beg1 , 0.0 ) );  
        // clog << j << " " << norm[j] << "\n";  
        normalize( beg1 , end1 , norm[j] );  
    }  
```  
advances the `end1` iterator past the end of the state vector `x`, which causes a debug exception (contract violation) on MSVC.  
  
Since the iterators are advanced before the loop starts and then again at the end of the each loop iteration, I propose the fix:  
  
```  
    for( size_t j=1 ; j<num_of_lyap ; ++j )  
    {  
        beg1 += n;  
        end1 += n;  
  
        for( size_t k=0 ; k<j ; ++k )  
        {  
            tmp[k] = std::inner_product( beg1 , end1 , first + k*n , 0.0 );  
            //  clog << j << " " << k << " " << tmp[k] << "\n";  
        }  
  
  
  
        for( size_t k=0 ; k<j ; ++k )  
            substract_vector( beg1 , end1 , first + k*n , tmp[k] );  
  
        // normalize j-th vector  
        norm[j] = sqrt( std::inner_product( beg1 , end1 , beg1 , 0.0 ) );  
        // clog << j << " " << norm[j] << "\n";  
        normalize( beg1 , end1 , norm[j] );  
    }  
```  
  
....which I will submit in a pull request shortly.
