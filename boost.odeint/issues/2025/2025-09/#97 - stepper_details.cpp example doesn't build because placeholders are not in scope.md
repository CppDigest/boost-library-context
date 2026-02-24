# #97 - stepper_details.cpp example doesn't build because placeholders are not in scope [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 21:58:34 UTC  
> Updated at: 2025-12-01 18:04:49 UTC  
> Closed at: 2025-12-01 18:04:49 UTC  
> Url: https://github.com/boostorg/odeint/issues/97  

The following code fails to compile:  
  
```  
    // Symplectic harmonic oscillator example  
    {  
        double t( 0.0 ) , dt( 0.1 );  
        //[ symplectic_stepper_detail_example  
        pair< vector_type , vector_type > x;  
        x.first[0] = 1.0; x.second[0] = 0.0;  
        symplectic_rkn_sb3a_mclachlan< vector_type > rkn;  
        rkn.do_step( make_pair( harm_osc_f1() , harm_osc_f2() ) , x , t , dt );  
        //]  
  
        //[ symplectic_stepper_detail_system_class_example  
        harm_osc h;  
        rkn.do_step( make_pair( detail::bind( &harm_osc::f1 , h , _1 , _2 ) , detail::bind( &harm_osc::f2 , h , _1 , _2 ) ) ,  
                x , t , dt );  
        //]  
    }  
```  
  
Because `std::placeholders::_1` and `_2` are not in scope.  The fix is to include `<functional>` and add using declarations for the placeholders:  
  
```  
    // Symplectic harmonic oscillator example  
    {  
        using std::placeholders::_1;  
        using std::placeholders::_2;  
  
        double t( 0.0 ) , dt( 0.1 );  
        //[ symplectic_stepper_detail_example  
        pair< vector_type , vector_type > x;  
        x.first[0] = 1.0; x.second[0] = 0.0;  
        symplectic_rkn_sb3a_mclachlan< vector_type > rkn;  
        rkn.do_step( make_pair( harm_osc_f1() , harm_osc_f2() ) , x , t , dt );  
        //]  
  
        //[ symplectic_stepper_detail_system_class_example  
        harm_osc h;  
        rkn.do_step( make_pair( detail::bind( &harm_osc::f1 , h , _1 , _2 ) , detail::bind( &harm_osc::f2 , h , _1 , _2 ) ) ,  
                x , t , dt );  
        //]  
    }  
```  
  
Pull request to follow...
