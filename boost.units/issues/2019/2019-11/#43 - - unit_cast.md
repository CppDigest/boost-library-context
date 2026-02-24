# #43 - [Feature Request]: unit_cast [Open]

> Username: Krzmbrzl  
> Created at: 2019-11-29 13:09:07 UTC  
> Updated at: 2019-11-29 13:09:41 UTC  
> Url: https://github.com/boostorg/units/issues/43  

When I try to cast a quantity to it's numerical representation I can use ` quantity_cast`. That's great. What I think could also be very helpful is a shorthand for casting a quantity to a different unit. Now I know that this can be done like so:  
```   
boost::units::quantity<boost::units::si::length, double> siLength;  
auto cgsLength = static_cast<boost::units::quantity<boost::units::cgs::length, double>>(siLength);  
```   
  
However this feels kinda more bulky than it'd need to be. It would be very neat if something like this would be possible:  
```   
boost::units::quantity<boost::units::si::length, double> siLength;  
auto cgsLength = unit_cast<boost::units::cgs::length>(siLength);  
```  
  
So essentially I am asking for a way to cast to a different (compatible) unit without having to explicitly write that I want to cast it to a quantity again. That should be deduced. Thus when starting from a quantity of double, I'd expect this cast to automatically cast to a quantity of double as well. Same for int and all other types.  
  
Would something like this be possible? :thinking:
