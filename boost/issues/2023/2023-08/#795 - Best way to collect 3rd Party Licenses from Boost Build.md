# #795 - Best way to collect 3rd Party Licenses from Boost Build? [Closed]

> Username: ncook-hxgn  
> Created at: 2023-08-14 10:29:50 UTC  
> Updated at: 2026-01-27 13:50:32 UTC  
> Closed at: 2026-01-27 13:50:32 UTC  
> Url: https://github.com/boostorg/boost/issues/795  

Hi,  
  
I build boost and pack it up as nuget packages for our business unit to use. I would like to include third party licenses where appropriate, if applicable (is _everything_ in boost licensed under BSL 1.0?).  
  
I am currently using `--stage some/path` during my build, as this makes it easier to package up. It would be wonderful if `--stage` collated all the licenses under `some/path` for me too.
