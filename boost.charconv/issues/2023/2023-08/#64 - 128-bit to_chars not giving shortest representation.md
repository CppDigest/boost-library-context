# #64 - 128-bit to_chars not giving shortest representation [Closed]

> Username: mborland  
> Created at: 2023-08-04 14:18:27 UTC  
> Updated at: 2023-08-04 17:33:36 UTC  
> Closed at: 2023-08-04 17:33:36 UTC  
> Url: https://github.com/boostorg/charconv/issues/64  

Examples:  
  
```  
Value: 0.4851437654714521865648158005512869  
Boost: 4.851437654714521865648158005512869e-01  
  STL: 0.4851437654714521865648158005512869  
```  
  
```  
Value: 0.007103704461770353364817587316082381  
Boost: 7.103704461770353364817587316082381e-03  
  STL: 0.007103704461770353364817587316082381  
```  
  
For small negative exponents we should be using fixed formatting not scientific.
