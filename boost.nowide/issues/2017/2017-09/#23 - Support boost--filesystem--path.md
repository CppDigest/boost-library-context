# #23 - Support boost::filesystem::path [Closed]

> Username: Flamefire  
> Created at: 2017-09-27 02:39:50 UTC  
> Updated at: 2020-02-05 16:24:03 UTC  
> Closed at: 2020-02-05 16:24:02 UTC  
> Url: https://github.com/boostorg/nowide/issues/23  

Could you add support for `boost::filesystem::path` as valid arguments to the fstreams? Similar to the `boost::filesystem:iofstream` as e.g. our pathhandling is almost completely done with bfs::path and calling `path.string().c_str()` would be cumbersome.

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-02-05 16:24:02 UTC  
> Url: https://github.com/boostorg/nowide/issues/23#issuecomment-582489489  

Fixed by #26
