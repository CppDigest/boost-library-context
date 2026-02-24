# #514 - Build fails attempting to download boost [Closed]

> Username: JamesDConley  
> Created at: 2021-05-11 03:03:31 UTC  
> Updated at: 2021-05-11 03:07:11 UTC  
> Closed at: 2021-05-11 03:07:11 UTC  
> Url: https://github.com/boostorg/boost/issues/514  

When running the build instructions...  
  
/Build/Boost/Build  
Scanning dependencies of target Boost  
[ 12%] Creating directories for 'Boost'  
[ 25%] Performing download step (download, verify and extract) for 'Boost'  
-- Downloading...  
   dst='/root/.hunter/_Base/Download/Boost/1.66.0/075d0b4/boost_1_66_0.7z'  
   timeout='none'  
-- Using src='https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.7z'  
-- Retrying...  
-- Using src='https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.7z'  
-- Retry after 5 seconds (attempt #2) ...  
-- Using src='https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.7z'  
-- Retry after 5 seconds (attempt #3) ...  
-- Using src='https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.7z'  
-- Retry after 15 seconds (attempt #4) ...  
-- Using src='https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.7z'  
  
And so on and so forth until the build fails

---

## Comment 1

> Username: JamesDConley  
> Created at: 2021-05-11 03:04:31 UTC  
> Url: https://github.com/boostorg/boost/issues/514#issuecomment-837701172  

When I try to access that URL I get 'forbidden' which I figure may be what's causing this, any workarounds?

---

## Comment 2

> Username: mclow  
> Created at: 2021-05-11 03:07:11 UTC  
> Url: https://github.com/boostorg/boost/issues/514#issuecomment-837703783  

Yes. See: https://www.boost.org/users/news/boost_has_moved_downloads_to_jfr.html
