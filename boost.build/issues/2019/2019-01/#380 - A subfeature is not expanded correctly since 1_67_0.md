# #380 - A subfeature is not expanded correctly since 1_67_0 [Closed]

> Username: atlantisjk  
> Created at: 2019-01-02 18:30:29 UTC  
> Updated at: 2019-01-03 17:18:02 UTC  
> Closed at: 2019-01-03 17:18:02 UTC  
> Url: https://github.com/boostorg/build/issues/380  

When a subfeature is specified for a source/library target (e.g. /lib1//lib1/<feature_name>feature_value-subfeature_value), such target (lib1) will not be build with features (such as \<cxxflags\>) that are associated with the specified subfeature.  
  
This happened after commit 15c8760 that remove the following line from main-target.generate in targets.jam.  
property-set = [ $(property-set).expand ] ;
