# #99 - molecular_dynamics_cells.cpp initializes reference from temporary [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 22:35:27 UTC  
> Updated at: 2025-12-02 07:31:52 UTC  
> Closed at: 2025-12-02 07:31:52 UTC  
> Url: https://github.com/boostorg/odeint/issues/99  

In the [following code](https://github.com/boostorg/odeint/blob/91a4a916fa193b02bda2ffc3920953387acae962/examples/molecular_dynamics_cells.cpp#L165):  
```  
        interaction_functor( hash_vector const& cells_begin , hash_vector const& cells_end , hash_vector pos_order ,  
                            point_vector const&x , point_vector const& v , params const &p )  
        : m_cells_begin( cells_begin ) , m_cells_end( cells_end ) , m_order( pos_order ) , m_x( x ) , m_v( v ) ,  
        m_p( p ) { }  
```  
  
The member variable `m_order`, which is declared as:  
```  
        hash_vector const &m_order;  
```  
is initialized from the constructor function call argument `hash_vector pos_order`, which is a temporary, so that when the constructor exits, the member variable is holding a reference to junk.  This results in the [following line](https://github.com/boostorg/odeint/blob/91a4a916fa193b02bda2ffc3920953387acae962/examples/molecular_dynamics_cells.cpp#L187) of code indexing an empty `hash_vector` and asserting under MSVC:  
```  
                        if( m_order[ ii ] == pos_hash ) continue;  
```  
  
I believe the correct fix is to change the type of the parameter to `const hash_vector &`.  If I do this locally, then the assert doesn't fire.  Pull request to follow.
