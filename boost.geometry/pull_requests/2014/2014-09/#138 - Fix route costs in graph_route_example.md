# #138 Fix route costs in graph_route_example [Merged]

> Username: mlang  
> Created at: 2014-09-17 07:43:44 UTC  
> Updated at: 2014-09-17 09:44:06 UTC  
> Merged at: 2014-09-17 08:36:57 UTC  
> Closed at: 2014-09-17 08:36:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/138  

For some reason, the graph_route examples have evolved into a broken state.  
  
Basically, road length is taken without considering the earth radius.  
Which leads to the following output:  
  
distances, all in KM  
Boston          - Chicago         -> through the air: 1370 , over the road: 0    
Boston          - Houston         -> through the air: 2568 , over the road: 0    
Boston          - Los Angeles     -> through the air: 4192 , over the road: 0    
Boston          - Miami           -> through the air: 2025 , over the road: 0    
Boston          - New York        -> through the air: 303  , over the road: 0    
Boston          - Phoenix         -> through the air: 3694 , over the road: 0    
Boston          - Washington DC   -> through the air: 641  , over the road: 0    
Chicago         - Boston          -> through the air: 1370 , over the road: 0    
Chicago         - Houston         -> through the air: 1496 , over the road: 0    
Chicago         - Los Angeles     -> through the air: 2822 , over the road: 0    
Chicago         - Miami           -> through the air: 1912 , over the road: 0    
Chicago         - New York        -> through the air: 1147 , over the road: 0    
Chicago         - Phoenix         -> through the air: 2333 , over the road: 0    
Chicago         - Washington DC   -> through the air: 960  , over the road: 0     
  
cost is mostly between 0 and 1, and later gets divided by 1000.  
  
The bug is rather obvious: You need to use the haversine strategy  
for linestring length as well, so that the distance though air  
and over the road have same units.  
  
With this commit applied, the output looks like this now:  
  
distances, all in KM  
Boston          - Chicago         -> through the air: 1370 , over the road: 1572  
Boston          - Houston         -> through the air: 2568 , over the road: 2931  
Boston          - Los Angeles     -> through the air: 4192 , over the road: 4811  
Boston          - Miami           -> through the air: 2025 , over the road: 2875  
Boston          - New York        -> through the air: 303  , over the road: 330   
Boston          - Phoenix         -> through the air: 3694 , over the road: 4327  
Boston          - Washington DC   -> through the air: 641  , over the road: 704   
Chicago         - Boston          -> through the air: 1370 , over the road: 1572  
Chicago         - Houston         -> through the air: 1496 , over the road: 2049  
Chicago         - Los Angeles     -> through the air: 2822 , over the road: 3263  
Chicago         - Miami           -> through the air: 1912 , over the road: 2253  
Chicago         - New York        -> through the air: 1147 , over the road: 1252  
Chicago         - Phoenix         -> through the air: 2333 , over the road: 2971  
Chicago         - Washington DC   -> through the air: 960  , over the road: 1101

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-09-17 08:36:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/138#issuecomment-55864555  

Thanks for these fixes!  
Indeed it was broken in the last version, by some changes to distance and strategies.  
Or probably it was broken earlier while using haversine instead of andoyer, because haversine needs the earth radius which is not picked automatically.

---
