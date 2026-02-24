# #272 - UNC path issues with boost_1_80_0 and newer [Closed]

> Username: greenkalx  
> Created at: 2023-01-13 04:21:44 UTC  
> Updated at: 2023-01-13 15:13:34 UTC  
> Closed at: 2023-01-13 10:24:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/272  

The unit tests below passed with boost_1_65_1 but now fail with boost_1_80_0 and 1_81_0.  
Seems a separate issue from https://github.com/boostorg/filesystem/issues/247  
  
```  
boost::filesystem::path::iterator::difference_type const k_UncLongRootLen = 5;// min # of Path components  
boost::filesystem::path::iterator::difference_type const k_UncLongMarkerPos = 2;  
  
{  
	boost::filesystem::path zpath(R"(\\?\UNC\x\y)");  
	BOOST_CHECK(std::distance(zpath.begin(), zpath.end()) >= k_UncLongRootLen);  
	BOOST_CHECK(*zpath.begin() == LR"(\\?)");  
	BOOST_CHECK(*std::next(zpath.begin(), k_UncLongMarkerPos) == LR"(UNC)");  
}  
  
{  
	boost::filesystem::path zpath(R"(\\?\UNC\server\sharename\)");  
	BOOST_CHECK(*zpath.begin() == LR"(\\?)");  
	BOOST_CHECK(*std::next(zpath.begin(), k_UncLongMarkerPos) == LR"(UNC)");  
}  
  
{  
	boost::filesystem::path zpath(R"(\\?\UNC\x\y\s\z)");  
	BOOST_CHECK(*zpath.begin() == LR"(\\?)");  
	BOOST_CHECK(*std::next(zpath.begin(), k_UncLongMarkerPos) == LR"(UNC)");  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-01-13 10:24:26 UTC  
> Updated at: 2023-01-13 15:13:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/272#issuecomment-1381620882  

I believe, this was a change in [Boost 1.77.0](https://www.boost.org/users/history/version_1_77_0.html), which added better support for Windows path prefixes such as "\\\\?\\". Prior to that the prefixes were not really supported and could be parsed as root names, as your test shows. There still are surprising effects with prefixes on Boost.Filesystem v3, so it is not recommended to use them with v3.  
  
The change was intended, the prefix should not be the entirety of the root name of the path (which is the first element when iterating over the path elements). In your case, the root name should be "\\\\?\\UNC". Similarly, `path("\\\\?\\C:\\foo\\bar").root_name()` will be "\\\\?\\C:" and `path("C:\\foo\\bar").root_name()` will be "C:".
