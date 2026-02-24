# #445 - Custom features specified on command line aren't propagated to subdirectories [Closed]

> Username: grisumbras  
> Created at: 2019-05-28 11:57:27 UTC  
> Updated at: 2019-05-28 12:31:41 UTC  
> Closed at: 2019-05-28 12:31:41 UTC  
> Url: https://github.com/boostorg/build/issues/445  

Consider this project:  
  
```  
# Jamroot  
import feature ;  
feature.feature custom : value1 : optional ;  
  
import notfile ;  
  
notfile foo : @foo ;  
  
rule foo ( target : : props * ) { echo $(props) ; echo ; }  
  
alias bar : sub//bar ;  
  
explicit foo bar ;  
```  
```  
# sub/Jamfile  
import notfile ;  
  
notfile bar : @bar ;  
  
rule bar ( target : : props * ) { echo $(props) ; }  
```  
  
When invoking from the project root `b2  foo bar custom=value1` I get  
```  
<action>@Jamfile</home/arhipov_d/dev/b21>%Jamfile</home/arhipov_d/dev/b21>.foo <asynch-exceptions>off <custom>value1 <debug-symbols>on ...  
  
<action>@Jamfile</home/arhipov_d/dev/b21/sub>%Jamfile</home/arhipov_d/dev/b21/sub>.bar <asynch-exceptions>off <debug-symbols>on ...  
```  
Note, that only target `foo` (which is in the root project) has property `custom=value1`.  
  
When I invoke `b2` from the `sub` subdirectory, target `bar` also has this property.

---

## Comment 1

> Username: grisumbras  
> Created at: 2019-05-28 12:31:41 UTC  
> Url: https://github.com/boostorg/build/issues/445#issuecomment-496496501  

Never mind, I dodn't realize the feature has to have `propagated` attribute.
