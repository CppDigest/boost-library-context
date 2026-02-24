# #226 - Jamfile contains syntax errors [Closed]

> Username: xobs  
> Created at: 2018-08-02 04:28:50 UTC  
> Updated at: 2018-08-02 10:58:16 UTC  
> Closed at: 2018-08-02 10:58:16 UTC  
> Url: https://github.com/boostorg/python/issues/226  

In the root of the project there is a `Jamfile`.  When trying to build it using Jam 2.6 (available in Ubuntu 16.04), `jam` is unable to do anything and complains about syntax errors:  
  
```sh  
xobs@Cuboid:boostpython$ jam  
Jamfile: line 21: syntax error at keyword (  
Jamfile: line 21: syntax error at keyword )  
...found 8 target(s)...  
xobs@Cuboid:boostpython$ jam -a  
Jamfile: line 21: syntax error at keyword (  
Jamfile: line 21: syntax error at keyword )  
...found 8 target(s)...  
xobs@Cuboid:boostpython$ git rev-parse HEAD  
ed4776b59caec6dfbea548a96701a810653e6f24  
xobs@Cuboid:boostpython$  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-08-02 10:58:16 UTC  
> Url: https://github.com/boostorg/python/issues/226#issuecomment-409888420  

There are no syntax errors; you are merely using the wrong tool. Please see http://boostorg.github.io/python/doc/html/building.html for instructions on how to build `Boost.Python` using the official build system (note: it's *BJam*, not *Jam*).  
  
This is admittedly complex, which is why I have started to add support for `Faber`...
