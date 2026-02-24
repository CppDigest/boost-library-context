# #26 - Feature request: constructor and resize from values [Open]

> Username: Svalorzen  
> Created at: 2020-11-20 19:32:53 UTC  
> Updated at: 2020-11-20 19:32:53 UTC  
> Url: https://github.com/boostorg/multi_array/issues/26  

Hello, this is a copy of my email I sent to the boost-users mailing list, as it might improve visibility.  
  
I'd love to be able to use multi_array with classes that do not have a default constructor. At the moment, this is impossible, as both the constructor and resize functions only take extents as arguments (and possibly some other options), but no value_type, as for example std::vector does. Thus, trying to store objects without a default constructor in a multi_array results in hard compiler errors, with no workaround that I know of.  
  
This happens to me fairly often, as I use multi_array to organize sets of complex objects. If there is a technical reason why this is not possible, I would also be happy to learn it.
