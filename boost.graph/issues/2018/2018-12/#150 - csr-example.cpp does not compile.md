# #150 - csr-example.cpp does not compile [Closed]

> Username: jzmaddock  
> Created at: 2018-12-16 19:13:14 UTC  
> Updated at: 2026-02-20 21:38:02 UTC  
> Closed at: 2026-02-20 21:38:02 UTC  
> Url: https://github.com/boostorg/graph/issues/150  

I see:  
  
```  
1>csr-example.cpp  
1>d:\data\boost\boost\boost\graph\graphviz.hpp(265): error C2064: term does not evaluate to a function taking 1 arguments  
1>d:\data\boost\boost\boost\graph\graphviz.hpp(291): note: see reference to function template instantiation 'void boost::write_graphviz<Graph,VertexPropertiesWriter,EdgePropertiesWriter,GraphPropertiesWriter,boost::typed_identity_property_map<size_t>>(std::ostream &,const Graph &,VertexPropertiesWriter,EdgePropertiesWriter,GraphPropertiesWriter,VertexID,boost::graph::detail::no_parameter)' being compiled  
1>        with  
1>        [  
1>            Graph=WebGraph,  
1>            VertexPropertiesWriter=boost::dynamic_properties,  
1>            EdgePropertiesWriter=std::string,  
1>            GraphPropertiesWriter=boost::typed_identity_property_map<size_t>,  
1>            VertexID=boost::typed_identity_property_map<size_t>  
1>        ]  
1>d:\data\boost\boost\libs\graph\example\csr-example.cpp(58): note: see reference to function template instantiation 'void boost::write_graphviz<WebGraph,boost::dynamic_properties,std::string,boost::typed_identity_property_map<size_t>>(std::ostream &,const Graph &,VertexPropertiesWriter,EdgePropertiesWriter,GraphPropertiesWriter,boost::graph::detail::no_parameter)' being compiled  
1>        with  
1>        [  
1>            Graph=WebGraph,  
1>            VertexPropertiesWriter=boost::dynamic_properties,  
1>            EdgePropertiesWriter=std::string,  
1>            GraphPropertiesWriter=boost::typed_identity_property_map<size_t>  
1>        ]  
1>d:\data\boost\boost\boost\graph\graphviz.hpp(271): error C2064: term does not evaluate to a function taking 2 arguments  
1>d:\data\boost\boost\boost\graph\graphviz.hpp(277): error C2064: term does not evaluate to a function taking 2 arguments  
```

---

## Comment 1

> Username: deinst  
> Created at: 2019-01-20 22:59:18 UTC  
> Url: https://github.com/boostorg/graph/issues/150#issuecomment-455910826  

Change line 58 from a call to `write_graphviz` to `write_graphviz_dp` to handle the dynamic properties correctly.

---

## Comment 2

> Username: hdu-sdlzx  
> Created at: 2022-12-10 02:39:47 UTC  
> Url: https://github.com/boostorg/graph/issues/150#issuecomment-1344987810  

Why not close this issue? @deinst @jzmaddock

---

## Comment 3

> Username: andreacassioli  
> Created at: 2026-02-20 20:04:03 UTC  
> Url: https://github.com/boostorg/graph/issues/150#issuecomment-3936863187  

This issue is stale and does not apply anymore (the example do compile). It can be closed @jeremy-murphy

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2026-02-20 21:37:06 UTC  
> Url: https://github.com/boostorg/graph/issues/150#issuecomment-3937272651  

Thanks!
