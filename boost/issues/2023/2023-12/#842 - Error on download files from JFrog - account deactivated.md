# #842 - Error on download files from JFrog - account deactivated [Closed]

> Username: rafaels-dev  
> Created at: 2023-12-31 02:04:11 UTC  
> Updated at: 2024-01-08 14:02:06 UTC  
> Closed at: 2024-01-04 14:36:41 UTC  
> Url: https://github.com/boostorg/boost/issues/842  

Today I'm trying to install the boost using the below podspec (extracted from react-native), but I was receiving the error message `[!] Error installing boost  
Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff`:.  
  
PodSpec:  
```podspec  
# Copyright (c) Meta Platforms, Inc. and affiliates.  
#  
# This source code is licensed under the MIT license found in the  
# LICENSE file in the root directory of this source tree.  
  
Pod::Spec.new do |spec|  
  spec.name = 'boost'  
  spec.version = '1.76.0'  
  spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
  spec.homepage = 'http://www.boost.org'  
  spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
  spec.authors = 'Rene Rivera'  
  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = { :ios => '11.0' }  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
  
```  
When I try to open the URL https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2 on the browser, I noticed the following page:  
<img width="1431" alt="image" src="https://github.com/boostorg/boost/assets/5066597/f7d677b4-d5f0-4b89-a0e6-5dc4ff7e8f94">  
  
I also download the same URL using the cURL and the result was a HTML page with the same SHA256 of the error message:  
```sh  
$ curl -L https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2 | sha256sum  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100   138  100   138    0     0    223      0 --:--:-- --:--:-- --:--:--   225  
100 11534  100 11534    0     0   8325      0  0:00:01  0:00:01 --:--:--  8325  
5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  -  
```

---

## Comment 1

> Username: jnak  
> Created at: 2023-12-31 03:30:33 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1872659766  

Same here!

---

## Comment 2

> Username: irisjae  
> Created at: 2023-12-31 12:47:38 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1872940713  

Does anyone know of a mirror?

---

## Comment 3

> Username: rafaels-dev  
> Created at: 2023-12-31 13:10:40 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1872945535  

> Does anyone know of a mirror?  
  
I downloaded a copy of the file hosted on Source Forge and put it directly on my project.  
https://sourceforge.net/projects/boost/files/boost/1.76.0/  
  
The SHA256 of the Source Forge file is the same as the original file.

---

## Comment 4

> Username: amadou-6e  
> Created at: 2023-12-31 18:00:20 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1873006805  

same problem!

---

## Comment 5

> Username: danidaryaweesh  
> Created at: 2023-12-31 18:14:28 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1873009923  

Same problem for us too. Is this going to be fixed by them anytime soon?

---

## Comment 6

> Username: f4exb  
> Created at: 2024-01-01 07:12:32 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1873195933  

Same problem here too: https://github.com/f4exb/sdrangel/actions/runs/7372158552

---

## Comment 7

> Username: sdarwin  
> Created at: 2024-01-01 18:22:40 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1873428030  

> account deactivated.  
  
A temporary mirror site: https://archives.boost.io/release/1.84.0/source/  
  
Hopefully the jfrog links will be restored soon.

---

## Comment 8

> Username: irisjae  
> Created at: 2024-01-04 09:56:09 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1876812015  

Seems the links are back up.

---

## Comment 9

> Username: shamas  
> Created at: 2024-01-07 04:32:19 UTC  
> Updated at: 2024-01-08 14:02:06 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1879934483  

> > Does anyone know of a mirror?  
>   
> I downloaded a copy of the file hosted on Source Forge and put it directly on my project. https://sourceforge.net/projects/boost/files/boost/1.76.0/  
>   
> The SHA256 of the Source Forge file is the same as the original file.  
  
How did you put it directly in your project?  
  
I found that using spec.source with a file:/ url format worked

---

## Comment 10

> Username: Shigbeard  
> Created at: 2024-01-07 11:23:22 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1880029733  

This issue should be re-opened, at the time of writing the jfrog urls on the boost.org website lead to a landing page to "reactivate the account".

---

## Comment 11

> Username: MrCyjaneK  
> Created at: 2024-01-07 15:03:01 UTC  
> Updated at: 2024-01-07 15:09:44 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1880083767  

I agree with @Shigbeard, all download links are broken as for now. Migrating the url to use https://archives.boost.io/ instead worked, hashes match. [commit](https://git.mrcyjanek.net/mrcyjanek/monero_c/commit/485ebe04a8a81212b7b30a6dd40087a250b670f1)

---

## Comment 12

> Username: userdocs  
> Created at: 2024-01-07 16:44:11 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1880109167  

There is no point reopening this issue when this https://github.com/boostorg/boost/issues/844 supersedes any outcome that may be achieved here. That is the real solution that needs to be implemented.  
  
This issue regarding an unreliable external cdn is meme at this point.

---

## Comment 13

> Username: MrCyjaneK  
> Created at: 2024-01-07 22:33:07 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1880199886  

@userdocs yet it is the official link from the official website

---

## Comment 14

> Username: qidaye  
> Created at: 2024-01-08 09:33:51 UTC  
> Url: https://github.com/boostorg/boost/issues/842#issuecomment-1880650438  

jfrog is still not working.
