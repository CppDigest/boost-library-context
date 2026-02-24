# #996 - Verification checksum was incorrect [Open]

> Username: yard2010  
> Created at: 2025-01-01 14:16:50 UTC  
> Updated at: 2025-02-09 11:07:32 UTC  
> Url: https://github.com/boostorg/boost/issues/996  

I'm using React Native which depends on Boost. When trying to run `pod install` I get the following message:  
  
```  
[!] Error installing boost  
Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 79e6d3f986444e5a80afbeccdaf2d1c1cf964baa8d766d20859d653a16c39848  
```  
  
I went down the rabbit hole: React Native tries to get Boost from `https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2` (in `node_modules/react-native/third-party-podspecs/boost.podspec`). This URL is broken - it leads to a [JFrog's "Your 14-day trial is over" page](https://landing.jfrog.com/reactivate-server/boostorg). Its checksum is indeed `79e6d3f986444e5a80afbeccdaf2d1c1cf964baa8d766d20859d653a16c39848`:  
  
```  
curl -sL https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2 | sha1sum   
7694f0b22c5217c4a12cd0fc1b4f74758b9d3fa3  -  
```  
  
Is it an upstream problem with JFrog? Or maybe someone forgot to pay the bills? 🙃

---

## Comment 1

> Username: sdarwin  
> Created at: 2025-01-01 14:31:39 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2567031602  

Hi @yard2010 the url is now:    
  
https://archives.boost.io/  
https://archives.boost.io/release/1.87.0/source/boost_1_87_0.tar.bz2  
  
JFrog was donating bandwidth. After years, they have requested we switch to another service, to mitigate costs.

---

## Comment 2

> Username: trim21  
> Created at: 2025-01-01 15:27:01 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2567050495  

there are some previous discussion about using GitHub releases https://github.com/boostorg/boost/issues/844

---

## Comment 3

> Username: yard2010  
> Created at: 2025-01-01 16:54:00 UTC  
> Updated at: 2025-01-08 05:54:16 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2567077083  

@sdarwin - thank you for the fast response! I guess all good things come to an end.  
  
Since I'm using a relatively old version of React Native (0.66.5) that I cannot upgrade, I ended up using [patch-package](https://www.npmjs.com/package/patch-package) to change the broken JFrog URL in `node_modules/react-native/third-party-podspecs/boost.podspec` to the one from [the archive](https://www.boost.org/users/history/version_1_76_0.html) or from the [releases page](https://github.com/userdocs/boost/releases/). The checksum is the same, so it's working nicely.  
  
For anyone having troubles with this, this is how I fixed it:  
1) `npm i patch-package`  
2) Update the URL in `node_modules/react-native/third-party-podspecs/boost.podspec`  
3) `npx patch-package react-native` (don't forget to commit the patch so it works for everybody else and in CI as well)  
4) Add `"postinstall": "patch-package"` under `scripts` in your `package.json`  
  
After running `npm i`, running `pod install` will install Boost successfully.  
Do note that using `patch-package` is a bit hacky in my opinion, and should be used as last resort.  
  
Reference: [this stackoverflow thread](https://stackoverflow.com/questions/77738691).  
  
Edit: There's a more detailed guide here: https://github.com/boostorg/boost/issues/843#issuecomment-1872943124

---

## Comment 4

> Username: dheeyi  
> Created at: 2025-01-06 19:46:22 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2573798501  

I have the same issue in CircleCI

---

## Comment 5

> Username: HTHou  
> Created at: 2025-01-07 03:17:01 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2574312659  

Is there any other address we can download the windows binaries? The exe file cannot be downloaded from https://archives.boost.io/.  
  
For example: https://archives.boost.io/release/1.87.0/binaries/boost_1_87_0-msvc-14.3-64.exe

---

## Comment 6

> Username: trim21  
> Created at: 2025-01-07 03:18:13 UTC  
> Updated at: 2025-01-07 03:20:46 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2574314369  

> Is there any other address we can download the windows binaries? The exe file cannot be downloaded from https://archives.boost.io/.  
>   
> For example: https://archives.boost.io/release/1.87.0/binaries/boost_1_87_0-msvc-14.3-64.exe  
  
https://github.com/ScoopInstaller/Main/blob/master/bucket/boost.json  
  
https://github.com/userdocs/boost/releases/

---

## Comment 7

> Username: mclow  
> Created at: 2025-01-07 04:16:27 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2574359569  

Specifically, the error for that file is:  
  
Error 503 Response object too large  
Response object too large  
  
@sdarwin  - any ideas?

---

## Comment 8

> Username: HTHou  
> Created at: 2025-01-07 04:25:18 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2574366601  

> > Is there any other address we can download the windows binaries? The exe file cannot be downloaded from https://archives.boost.io/.  
> > For example: https://archives.boost.io/release/1.87.0/binaries/boost_1_87_0-msvc-14.3-64.exe  
>   
> https://github.com/ScoopInstaller/Main/blob/master/bucket/boost.json  
>   
> https://github.com/userdocs/boost/releases/  
  
thx so much~

---

## Comment 9

> Username: Raja0sama  
> Created at: 2025-01-07 08:28:39 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2574671532  

🎉 Found a Fix for the Pesky Boost Installation Error! 🛠️  
  
Hey fellow React Native warriors! If you're battling with the infamous Boost installation error (`Error installing boost - Verification checksum was incorrect`), I've got a solution that might save your day!   
  
The issue occurs because the default Boost download URL is having a mid-life crisis 😅. Here's a quick fix that'll make your iOS builds happy again:  
  
1. Add this magical snippet to the TOP of your Podfile:  
```ruby  
def find_and_replace_boost_url  
  pod_spec = "../node_modules/react-native/third-party-podspecs/boost.podspec"  
  puts "Debug: Starting boost URL replacement"  
  if File.exist?(pod_spec)  
    puts "Debug: Found boost.podspec"  
    spec_content = File.read(pod_spec)  
    spec_content.gsub!(  
      'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
      'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2'  
    )  
    File.write(pod_spec, spec_content)  
    puts "Debug: Updated boost.podspec"  
  end  
end  
  
# Let the magic happen!  
find_and_replace_boost_url  
```  
  
💡 **What's happening here?**  
- The script swaps out the problematic JFrog URL with a more reliable mirror  
- It runs before pod installation begins, ensuring smooth sailing ⛵  
- No SHA verification modifications needed!  
  
🔧 **Pro Tips:**  
- Make sure to add this at the very top of your Podfile  
- You might need to adjust the URL based on what's in your boost.podspec  
- After adding this, run `pod install` as usual  
  
Hope this helps someone avoid the hours of head-scratching I went through! 🧩

---

## Comment 10

> Username: sdarwin  
> Created at: 2025-01-07 11:39:06 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2575072896  

> windows binaries  
> Error 503 Response object too large  
  
Try now. Above a certain size the Fastly CDN won't cache/serve files unless a custom configuration is set up.

---

## Comment 11

> Username: Jacobharv00  
> Created at: 2025-01-08 21:23:05 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2578695412  

🔥  -> https://github.com/boostorg/boost/issues/996#issuecomment-2574671532   
  
  
@Raja0sama thank you for this solution. Did builds before the 31st and install worked fine but today fail. Making it use the archives url worked to resolve my install issue with Boost.

---

## Comment 12

> Username: ensarduman  
> Created at: 2025-02-09 11:07:31 UTC  
> Url: https://github.com/boostorg/boost/issues/996#issuecomment-2646179276  

> 🎉 Found a Fix for the Pesky Boost Installation Error! 🛠️  
>   
> Hey fellow React Native warriors! If you're battling with the infamous Boost installation error (`Error installing boost - Verification checksum was incorrect`), I've got a solution that might save your day!  
>   
> The issue occurs because the default Boost download URL is having a mid-life crisis 😅. Here's a quick fix that'll make your iOS builds happy again:  
>   
> 1. Add this magical snippet to the TOP of your Podfile:  
>   
> def find_and_replace_boost_url  
>   pod_spec = "../node_modules/react-native/third-party-podspecs/boost.podspec"  
>   puts "Debug: Starting boost URL replacement"  
>   if File.exist?(pod_spec)  
>     puts "Debug: Found boost.podspec"  
>     spec_content = File.read(pod_spec)  
>     spec_content.gsub!(  
>       'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
>       'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2'  
>     )  
>     File.write(pod_spec, spec_content)  
>     puts "Debug: Updated boost.podspec"  
>   end  
> end  
>   
> # Let the magic happen!  
> find_and_replace_boost_url  
> 💡 **What's happening here?**  
>   
> * The script swaps out the problematic JFrog URL with a more reliable mirror  
> * It runs before pod installation begins, ensuring smooth sailing ⛵  
> * No SHA verification modifications needed!  
>   
> 🔧 **Pro Tips:**  
>   
> * Make sure to add this at the very top of your Podfile  
> * You might need to adjust the URL based on what's in your boost.podspec  
> * After adding this, run `pod install` as usual  
>   
> Hope this helps someone avoid the hours of head-scratching I went through! 🧩  
  
Awesome!!!
