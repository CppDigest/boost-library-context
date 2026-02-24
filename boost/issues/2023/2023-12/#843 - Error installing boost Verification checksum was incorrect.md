# #843 - Error installing boost Verification checksum was incorrect [Open]

> Username: wswebcreation  
> Created at: 2023-12-31 10:17:02 UTC  
> Updated at: 2025-09-29 13:24:14 UTC  
> Url: https://github.com/boostorg/boost/issues/843  

I'm developing a Mobile app with React Native (0.73.1) and for building the app I need to install the pods. The pods contains installing boos, see [this file](https://github.com/facebook/react-native/blob/main/packages/react-native/third-party-podspecs/boost.podspec#L14).  
  
I get the following error  
  
```logs  
Installing boost (1.83.0)  
  
[!] Error installing boost  
Verification checksum was incorrect, expected 6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  
```  
  
When I check [this site](https://www.boost.org/users/history/version_1_83_0.html) I see the checksum should be `6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e`  
  
When I do this on my Mac  
  
```sh  
curl -sL https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2 | shasum -a 256  
  
5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  -  
```  
  
I get a different checksum  
  
  
Please let me know what I need to provide more to debug this

---

## Comment 1

> Username: RahulSingh225  
> Created at: 2023-12-31 10:30:47 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872915036  

> I'm developing a Mobile app with React Native (0.73.1) and for building the app I need to install the pods. The pods contains installing boos, see [this file](https://github.com/facebook/react-native/blob/main/packages/react-native/third-party-podspecs/boost.podspec#L14).  
>   
> I get the following error  
>   
> ```  
> Installing boost (1.83.0)  
>   
> [!] Error installing boost  
> Verification checksum was incorrect, expected 6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  
> ```  
>   
> When I check [this site](https://www.boost.org/users/history/version_1_83_0.html) I see the checksum should be `6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e`  
>   
> When I do this on my Mac  
>   
> ```shell  
> curl -sL https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2 | shasum -a 256  
>   
> 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  -  
> ```  
>   
> I get a different checksum  
>   
> Please let me know what I need to provide more to debug this  
  
Facing the same issue on M1 machine

---

## Comment 2

> Username: vinpro24  
> Created at: 2023-12-31 10:50:58 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872918846  

move to node_modules/react-native/third-party-podspecs.  
Only change line  
spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2',  
:sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
to  
spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
:sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
  
Maybe jfrog close their server

---

## Comment 3

> Username: codeion  
> Created at: 2023-12-31 10:51:09 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872918877  

I have the same issue with React Native 0.72.7  
  
Error installing boost  
Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  
  
[boost version 1.76.0](https://www.boost.org/users/history/version_1_76_0.html)

---

## Comment 4

> Username: tecmusti  
> Created at: 2023-12-31 11:11:35 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872922805  

Just replace 83 with 76 and 6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e with f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41  
  
https://github.com/boostorg/boost/issues/843#issuecomment-1872918846  
  
> I have the same issue with React Native 0.72.7  
>   
> Error installing boost Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  
>   
> [boost version 1.76.0](https://www.boost.org/users/history/version_1_76_0.html)

---

## Comment 5

> Username: wswebcreation  
> Created at: 2023-12-31 12:27:42 UTC  
> Updated at: 2023-12-31 12:36:01 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872936749  

Changing the checksum without knowing what happened isn’t a good solution. The checksum ensures the integrity and authenticity of your downloaded file, protecting against corrupted, incomplete, or maliciously altered files.  
If you bypass the checksum verification, you risk introducing security vulnerabilities, stability issues, or subtle bugs in your application. These can be challenging to diagnose and may compromise your application or user data.   
  
So there’s a reason why they use checksums and it's up to you all if you want to skip it 😅

---

## Comment 6

> Username: tecmusti  
> Created at: 2023-12-31 12:34:37 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872938165  

> Changing the checksum without knowing what happened doesn’t feel like a good solution. There’s a reason why they use checksums 😅  
  
We are not changing the checksum. Original link is broken, just replacing it with sourceforge link.

---

## Comment 7

> Username: wswebcreation  
> Created at: 2023-12-31 12:59:54 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872943124  

Ok, created a simple patch for this for RN 0.73.1, but everyone could do this  
  
  
1. **Install `patch-package`:**  
   ```bash  
   npm install --save-dev patch-package postinstall-postinstall  
   ```  
  
2. **Modify `boost.podspec`:**  
  
change  
  
```podspec  
# Copyright (c) Meta Platforms, Inc. and affiliates.  
#  
# This source code is licensed under the MIT license found in the  
# LICENSE file in the root directory of this source tree.  
  
Pod::Spec.new do |spec|  
  spec.name = 'boost'  
  spec.version = '1.83.0'  
  spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
  spec.homepage = 'http://www.boost.org'  
  spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
  spec.authors = 'Rene Rivera'  
  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2',  
                  :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = min_supported_versions  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
  
```  
  
to  
  
```podspec  
# Copyright (c) Meta Platforms, Inc. and affiliates.  
#  
# This source code is licensed under the MIT license found in the  
# LICENSE file in the root directory of this source tree.  
  
Pod::Spec.new do |spec|  
  spec.name = 'boost'  
  spec.version = '1.83.0'  
  spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
  spec.homepage = 'http://www.boost.org'  
  spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
  spec.authors = 'Rene Rivera'  
  # Patched due to issue https://github.com/boostorg/boost/issues/843  
  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
                  :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = min_supported_versions  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
  
```  
  
3. **Create a Patch:**  
   - Run `npx patch-package react-native` to create a patch file based on your changes. This command generates a patch file in a directory called `patches/`.  
  
4. **Apply the Patch Automatically:**  
   - Modify your `package.json` to apply the patch after installation. Add the following to your scripts section:  
     ```json  
     "scripts": {  
       "postinstall": "patch-package"  
     }  
     ```  
   - Now, whenever you run `npm install`, `patch-package` will automatically apply the patch to the `boost.podspec` file.

---

## Comment 8

> Username: ronenempathy  
> Created at: 2023-12-31 13:41:47 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872951752  

facing the same issue with react-native 0.68.1.   
any workaround for this?

---

## Comment 9

> Username: wswebcreation  
> Created at: 2023-12-31 13:53:07 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872954097  

> facing the same issue with react-native 0.68.1. any workaround for this?  
  
Yes:  
  
1. Go to `node_modules/react-native/third-party-podspecs` from the root of your project  
2. check the `boost.podspec` for the version you need to use  
3. check https://sourceforge.net/projects/boost/files/boost/ for the link of the file you need to have  
4. get the checksum  
5. Follow the steps from https://github.com/boostorg/boost/issues/843#issuecomment-1872943124

---

## Comment 10

> Username: ronenempathy  
> Created at: 2023-12-31 16:31:42 UTC  
> Updated at: 2023-12-31 16:41:09 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872989278  

@wswebcreation since we are not pushing node_modules, is there any other ways to overcome this. this issue interrupts our CI as well.

---

## Comment 11

> Username: wswebcreation  
> Created at: 2023-12-31 16:45:56 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872991989  

@ronenempathy   
  
You are not creating a new module, you are creating a patch in your project that will be installed when you do the `npm i`  
  
The steps mentioned above create the patch, here's how it looks like in my case  
  
![image](https://github.com/boostorg/boost/assets/11979740/20b4d124-12a4-4837-be50-559ef1cadc21)

---

## Comment 12

> Username: gregavola  
> Created at: 2023-12-31 16:57:11 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872994164  

What about for build systems like AppCenter, where node_modules are created at build time?

---

## Comment 13

> Username: dafilmorais  
> Created at: 2023-12-31 16:58:17 UTC  
> Updated at: 2023-12-31 16:58:59 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872994399  

Doesn't work. It just hangs "installing boost (1.76.0)"  
Using sourceforge  
  
```javascript  
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
  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = { :ios => '11.0' }  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
```

---

## Comment 14

> Username: wswebcreation  
> Created at: 2023-12-31 17:01:04 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872995010  

> What about for build systems like AppCenter, where node_modules are created at build time?  
  
What do you mean?

---

## Comment 15

> Username: wswebcreation  
> Created at: 2023-12-31 17:01:26 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872995093  

> Doesn't work. It just hangs "installing boost (1.76.0)" Using sourceforge  
>   
> ```js  
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.76.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
>                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = { :ios => '11.0' }  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> ```  
  
Sorry, can't help with that

---

## Comment 16

> Username: sarunmrzn  
> Created at: 2023-12-31 17:03:48 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872995591  

> Doesn't work. It just hangs "installing boost (1.76.0)" Using sourceforge  
>   
> ```js  
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.76.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
>                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = { :ios => '11.0' }  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> ```  
  
Try running with `verbose`, `pod install --verbose`. Boost is 100+ MB ish depending on your connection.

---

## Comment 17

> Username: gregavola  
> Created at: 2023-12-31 17:04:18 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872995705  

> > What about for build systems like AppCenter, where node_modules are created at build time?  
>   
> What do you mean?  
  
When AppCenter does a build, it generates does an `yarn install` to pull in everything in `node_modules` so editing these files locally, won't help the build process. Is there a way to override this on `yarn install` it pulls the correct url?

---

## Comment 18

> Username: dafilmorais  
> Created at: 2023-12-31 17:07:38 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1872996584  

> > Doesn't work. It just hangs "installing boost (1.76.0)" Using sourceforge  
> > ```js  
> > # Copyright (c) Meta Platforms, Inc. and affiliates.  
> > #  
> > # This source code is licensed under the MIT license found in the  
> > # LICENSE file in the root directory of this source tree.  
> >   
> > Pod::Spec.new do |spec|  
> >   spec.name = 'boost'  
> >   spec.version = '1.76.0'  
> >   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
> >   spec.homepage = 'http://www.boost.org'  
> >   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
> >   spec.authors = 'Rene Rivera'  
> >   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
> >                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
> >   
> >   # Pinning to the same version as React.podspec.  
> >   spec.platforms = { :ios => '11.0' }  
> >   spec.requires_arc = false  
> >   
> >   spec.module_name = 'boost'  
> >   spec.header_dir = 'boost'  
> >   spec.preserve_path = 'boost'  
> > end  
> > ```  
>   
> Try running with `verbose`, `pod install --verbose`. Boost is 100+ MB ish depending on your connection.  
  
Huh, looks like it is downloading. Just really slow. Thanks for the tip.  
Jfrog links are dead, should be up again later sometime hopefully.

---

## Comment 19

> Username: twercedev  
> Created at: 2023-12-31 18:29:06 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873012721  

@wswebcreation   
  
> Ok, created a simple patch for this for RN 0.73.1, but everyone could do this  
>   
> 1. **Install `patch-package`:**  
>    ```shell  
>    npm install --save-dev patch-package postinstall-postinstall  
>    ```  
> 2. **Modify `boost.podspec`:**  
>   
> change  
>   
> ```ruby  
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.83.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = min_supported_versions  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> ```  
>   
> to  
>   
> ```ruby  
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.83.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   # Patched due to issue https://github.com/boostorg/boost/issues/843  
>   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = min_supported_versions  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> ```  
>   
> 3. **Create a Patch:**  
>      
>    * Run `npx patch-package react-native` to create a patch file based on your changes. This command generates a patch file in a directory called `patches/`.  
> 4. **Apply the Patch Automatically:**  
>      
>    * Modify your `package.json` to apply the patch after installation. Add the following to your scripts section:  
>      ```json  
>      "scripts": {  
>        "postinstall": "patch-package"  
>      }  
>      ```  
>    * Now, whenever you run `npm install`, `patch-package` will automatically apply the patch to the `boost.podspec` file.  
  
@wswebcreation  Thanks this works for me.

---

## Comment 20

> Username: tecmusti  
> Created at: 2023-12-31 18:53:31 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873016498  

> > > What about for build systems like AppCenter, where node_modules are created at build time?  
> >   
> >   
> > What do you mean?  
>   
> When AppCenter does a build, it generates does an `yarn install` to pull in everything in `node_modules` so editing these files locally, won't help the build process. Is there a way to override this on `yarn install` it pulls the correct url?  
  
Try creating patch on your local computer and upload generated patch then modify package.json file to apply patches. This should work.

---

## Comment 21

> Username: ali-sao  
> Created at: 2023-12-31 22:35:10 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873048223  

Who else is facing incredible slowness in jFrog? it took almost 45 minutes to install boost

---

## Comment 22

> Username: Harisene  
> Created at: 2024-01-01 06:17:01 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873179370  

> Who else is facing incredible slowness in jFrog? it took almost 45 minutes to install boost  
  
same here

---

## Comment 23

> Username: Harisene  
> Created at: 2024-01-01 06:19:43 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873180185  

> > > What about for build systems like AppCenter, where node_modules are created at build time?  
> >   
> >   
> > What do you mean?  
>   
> When AppCenter does a build, it generates does an `yarn install` to pull in everything in `node_modules` so editing these files locally, won't help the build process. Is there a way to override this on `yarn install` it pulls the correct url?  
  
Follow these steps to create a patch. https://github.com/boostorg/boost/issues/843#issuecomment-1872943124  
  
Now when running yarn install the patch will be applied in the App Center.

---

## Comment 24

> Username: mina4gerges  
> Created at: 2024-01-01 08:01:19 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873212359  

is it safe to change from https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2 to https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2 ?

---

## Comment 25

> Username: agrittiwari  
> Created at: 2024-01-01 08:22:49 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873220736  

Implemented the changes suggested as Suggested above, but it really didn't solve the problem. Made a patch of it, it doesn't solve the upstream problem, but crashes the pod install, somehow.  
  
```  
⚠️  Something went wrong running `pod install` in the `ios` directory.  
Command `pod install` failed.  
└─ Cause: Failed to load 'boost' podspec:   
[!] Invalid `boost.podspec` file: undefined local variable or method `min_supported_versions' for Pod:Module.  
  
 #  from /Users/agrittiwari/wishup/client-app/node_modules/react-native/third-party-podspecs/boost.podspec:17  
 #  -------------------------------------------  
 #    # Pinning to the same version as React.podspec.  
 >    spec.platforms = min_supported_versions  
 #    spec.requires_arc = false  
 #  -------------------------------------------  
  
  
pod install --repo-update --ansi exited with non-zero code: 1  
```

---

## Comment 26

> Username: Mitdd9707  
> Created at: 2024-01-01 11:29:10 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873286564  

Same issue face? Does it have a solution?  
  
> Implemented the changes suggested as Suggested above, but it really didn't solve the problem. Made a patch of it, it doesn't solve the upstream problem, but crashes the pod install, somehow.  
>   
> ```  
> ⚠️  Something went wrong running `pod install` in the `ios` directory.  
> Command `pod install` failed.  
> └─ Cause: Failed to load 'boost' podspec:   
> [!] Invalid `boost.podspec` file: undefined local variable or method `min_supported_versions' for Pod:Module.  
>   
>  #  from /Users/agrittiwari/wishup/client-app/node_modules/react-native/third-party-podspecs/boost.podspec:17  
>  #  -------------------------------------------  
>  #    # Pinning to the same version as React.podspec.  
>  >    spec.platforms = min_supported_versions  
>  #    spec.requires_arc = false  
>  #  -------------------------------------------  
>   
>   
> pod install --repo-update --ansi exited with non-zero code: 1  
> ```

---

## Comment 27

> Username: Seas0nSu  
> Created at: 2024-01-01 11:36:41 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873289028  

How about android,  still getting from origin website

---

## Comment 28

> Username: iMiodrag  
> Created at: 2024-01-01 12:43:22 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873312715  

@agrittiwari  @Mitdd9707 try replacing spec.platforms with:` spec.platforms = { :ios => '11.0' }` and create a patch again using `npx patch-package react-native`  
  
Not sure if this is the best solution, it works for me.

---

## Comment 29

> Username: TianMeh  
> Created at: 2024-01-01 13:05:31 UTC  
> Updated at: 2024-01-01 13:10:19 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873320393  

What happens when this is fixed? Is it smart to remove the patch and changes or just leave as is? Or when i update the react native version for example, do i have to update the boost.podspec patch?

---

## Comment 30

> Username: sameerac-swivel  
> Created at: 2024-01-01 13:09:03 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873321872  

When this will get fix ?? This caused lot of problems in our current pipeline. I hope this will get fix ASAP.

---

## Comment 31

> Username: anishsundarjee  
> Created at: 2024-01-01 13:42:38 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873333708  

I applied the patch for boost v1.76.0  
and changed the podfile `node_modules/react-native/third-party-podspecs/boost.podspec`  
  
`  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                  :sha256 => '5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff' }`  
                    
  but when I attempt `pod install`  
  I'm getting   
    
![Screenshot 2024-01-02 at 00 40 20](https://github.com/boostorg/boost/assets/45860597/56dbbe00-df64-4429-979b-a9472c2dda7c)  
  
have tried  
  
`rm -rf ~/Library/Caches/CocoaPods  
rm -rf ios/Pods  
rm -rf ~/Library/Developer/Xcode/DerivedData/*  
cd ios  
pod deintegrate  
pod setup  
pod install`  
  
still getting the same result

---

## Comment 32

> Username: kdn0325  
> Created at: 2024-01-01 14:13:31 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873344767  

I temporarily fixed the bug, but when will this be resolved?

---

## Comment 33

> Username: asafkorem  
> Created at: 2024-01-01 14:48:37 UTC  
> Updated at: 2024-01-01 15:44:28 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873356960  

I call this patch method in my post-install script, it works fine:  
  
```js  
const patchBoostPodspec = () => {  
  const boostPodspecPath = `${process.cwd()}/node_modules/react-native/third-party-podspecs/boost.podspec`;  
  const originalUrl = 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2';  
  const patchedUrl = 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2';  
  
  if (!fs.existsSync(boostPodspecPath)) {  
    // boost.podspec does not exist, skipping patch  
    return;  
  }  
  
  let boostPodspec = fs.readFileSync(boostPodspecPath, 'utf8');  
  
  if (!boostPodspec.includes(originalUrl)) {  
    // boost.podspec is already patched or the URL is different, skipping patch  
    return;  
  }  
  
  boostPodspec = boostPodspec.replace(originalUrl, patchedUrl);  
  fs.writeFileSync(boostPodspecPath, boostPodspec, 'utf8');  
};  
  
...  
  
patchBoostPodspec();  
```  
  
though installing from sourceforge is extremely slow

---

## Comment 34

> Username: sdarwin  
> Created at: 2024-01-01 18:21:36 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873427686  

This is a temporary mirror site: https://archives.boost.io/release/1.84.0/source/  
  
Hopefully the jfrog links will be restored soon.

---

## Comment 35

> Username: mununki  
> Created at: 2024-01-02 04:57:49 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873620744  

@sdarwin Thanks. I confirm that the checksum is same.  
```  
$ curl -sL https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2 | shasum -a 256  
f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41  -  
```

---

## Comment 36

> Username: Mitdd9707  
> Created at: 2024-01-02 05:40:21 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873638626  

> @agrittiwari @Mitdd9707 try replacing spec.platforms with:` spec.platforms = { :ios => '11.0' }` and create a patch again using `npx patch-package react-native`  
>   
> not sure if this is the best solution, but it works for me.  
  
Thank you, @iMiodrag it's worked for me

---

## Comment 37

> Username: ngocdevv  
> Created at: 2024-01-02 07:12:17 UTC  
> Updated at: 2024-01-02 09:43:23 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873679922  

update cocoapod(brew install cocoapods), worked for me.

---

## Comment 38

> Username: sajeezy  
> Created at: 2024-01-02 09:42:18 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873797858  

> update cocoapod(brew install cocoapods), work for me.  
  
This worked for me. I also run `brew reinstall cocoapods`. Thanks @ngoclee-dev

---

## Comment 39

> Username: ehtisham-ali-emumba  
> Created at: 2024-01-02 09:50:14 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873806169  

yes boost jfrog url is now working actually.

---

## Comment 40

> Username: hossam-k  
> Created at: 2024-01-02 10:17:52 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1873836556  

Using Boost jfrog URL is working for me correctly

---

## Comment 41

> Username: arron-kukadia  
> Created at: 2024-01-02 16:42:47 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874269616  

This looks to be working for me now as well

---

## Comment 42

> Username: Roshdy  
> Created at: 2024-01-02 18:28:40 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874391243  

The only thing that worked for me 0.72.5 is by removing `node_modules` and installing fresh ones.

---

## Comment 43

> Username: wswebcreation  
> Created at: 2024-01-02 20:20:10 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874504277  

> Ok is it just me???? I DON'T WANT AND SHOULD NOT HAVE TO DO ANY OF THIS. It should just work are you kidding me REACT NATIVE????  
  
@tyler-canton   
  
😳, what do you mean? I believe this is just you:  
  
1. it’s already solved  
2. it’s a 3rd party dependency/issue  
3. it’s “open source”  
  
everyone is doing it’s best here, during the weekend/evening/spare time.   
  
I would expect a more professional and reasonable response from a, assuming, “professional” developer.  
  
  
If you believe this could have been prevented then please help and keep these demotivating replies where they belong

---

## Comment 44

> Username: tyler-canton  
> Created at: 2024-01-02 20:52:38 UTC  
> Updated at: 2024-01-02 20:55:04 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874537667  

@wswebcreation  just frustrated that I started a new app with react-native 73 and still having the boost error issue. Going back to 72.5 with @Roshdy

---

## Comment 45

> Username: sdarwin  
> Created at: 2024-01-02 21:00:39 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874556137  

>  still having the boost error issue.   
  
The original jfrog links should be re-activated and working now.   If you still have any problems, please report the specific details.

---

## Comment 46

> Username: o-alexandrov  
> Created at: 2024-01-02 21:05:00 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874567998  

I have just tried it and the issue is resolved.  
No more issues with the checksum.

---

## Comment 47

> Username: wswebcreation  
> Created at: 2024-01-02 21:08:44 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1874571319  

> @wswebcreation just frustrated that I started a new app with react-native 73 and still having the boost error issue. Going back to 72.5 with @Roshdy  
  
I saw you removed your comment, but I understand it’s frustrating.   
I wanted my GHA workflow to work before the end of the year, as one of my last projects of 2023 and was also a bit frustrated, but these things can happen.  
Nothing works flawless in this world (not talking about RN specifically 😂), we all work with open source and can’t “demand” flawless code. We can “demand” ourselves to help and come up with fixes/patches and so on to unblock ourselves and others 😅

---

## Comment 48

> Username: aphillipo  
> Created at: 2024-01-07 00:32:17 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1879893963  

This is still a problem right now it seems...

---

## Comment 49

> Username: mclow  
> Created at: 2024-01-07 00:53:08 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1879898835  

Yes. It just started again about an hour ago.  
Working to get it fixed....

---

## Comment 50

> Username: gtokman  
> Created at: 2024-01-07 03:47:18 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1879928232  

```  
diff --git a/node_modules/react-native/third-party-podspecs/boost.podspec b/node_modules/react-native/third-party-podspecs/boost.podspec  
index 3d9331c..b1e2c6a 100644  
--- a/node_modules/react-native/third-party-podspecs/boost.podspec  
+++ b/node_modules/react-native/third-party-podspecs/boost.podspec  
@@ -10,7 +10,7 @@ Pod::Spec.new do |spec|  
   spec.homepage = 'http://www.boost.org'  
   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
   spec.authors = 'Rene Rivera'  
-  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
+  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
   
   # Pinning to the same version as React.podspec.  
   ```  
     
   This patch fixed the issue for me.

---

## Comment 51

> Username: NaNomicon  
> Created at: 2024-01-07 07:30:32 UTC  
> Updated at: 2024-01-07 07:31:07 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1879981069  

>   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
  
Instead of using sourceforge, using `https://archives.boost.io/release` works for me

---

## Comment 52

> Username: danielgospodinow  
> Created at: 2024-01-07 08:22:30 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1879991083  

> > spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
> > :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
> Instead of using sourceforge, using `https://archives.boost.io/release` works for me  
  
+1, this worked for me too.

---

## Comment 53

> Username: netanelz-amdocs  
> Created at: 2024-01-07 09:23:51 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880003292  

It started to happen again today....

---

## Comment 54

> Username: ferasabufares  
> Created at: 2024-01-07 11:19:47 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880028857  

> It started to happen again today....  
  
did you solve it ?

---

## Comment 55

> Username: devshaaran  
> Created at: 2024-01-07 11:21:09 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880029196  

Facing the same issue now, changing to `https://archives.boost.io/release` worked

---

## Comment 56

> Username: netanelz-amdocs  
> Created at: 2024-01-07 12:01:21 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880038570  

It redirects to a unsecured url...  
http://brorigin1.cpp.al/release/  
  
Not sure it's really secured to do it.  
  
> Facing the same issue now, changing to `https://archives.boost.io/release` worked

---

## Comment 57

> Username: sdarwin  
> Created at: 2024-01-07 14:48:38 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880080124  

https://archives.boost.io/release is an AWS Cloudfront distribution with backend servers hosting the files.  
  
I have switched the origin to use an ALB (application load balancer) and TLS.       
  
@netanelz-amdocs interesting comment. brorigin1 is an origin for the CDN. Usually there is a difference between a 'redirect' and an 'origin' though. The CDN should not be redirecting (such as HTTP response status code 302). Rather, it proxies the request on behalf of the client, and the client sees the main URL https://archives.boost.io .   How did you observe redirects occurring?

---

## Comment 58

> Username: netanelz-amdocs  
> Created at: 2024-01-07 15:06:50 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880084872  

I just put 'https://archives.boost.io/release'  in the browser to ensure all the releases are there and see it's going to http://brorigin1.cpp.al/release/ which is an unsecure site. Are you saying it's ok to use it?  
  
> https://archives.boost.io/release is an AWS Cloudfront distribution with backend servers hosting the files.  
>   
> I have switched the origin to use an ALB (application load balancer) and TLS.  
>   
> @netanelz-amdocs interesting comment. brorigin1 is an origin for the CDN. Usually there is a difference between a 'redirect' and an 'origin' though. The CDN should not be redirecting (such as HTTP response status code 302). Rather, it proxies the request on behalf of the client, and the client sees the main URL https://archives.boost.io . How did you observe redirects occurring?

---

## Comment 59

> Username: sdarwin  
> Created at: 2024-01-07 15:49:21 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880095249  

@netanelz-amdocs great catch!     Nginx usually provides directory listings when there is a trailing slash "/".   In this case "/release" was missing a slash, so nginx redirects the client to "/release/"  .  When the redirect happens it sends the full url that it thinks is being used: brorigin1.    
  
I added a config parameter:  
  
```  
absolute_redirect off;  
```  
  
that should fix the problem.  Clear local browser cache ...

---

## Comment 60

> Username: blackliner  
> Created at: 2024-01-07 20:14:05 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880159539  

> @sdarwin Thanks. I confirm that the checksum is same.  
>   
> ```  
> $ curl -sL https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2 | shasum -a 256  
> f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41  -  
> ```  
  
Thanks, how temporary is this? Can we rely on this for the coming future, or do you have a TTL on this?

---

## Comment 61

> Username: alexma01  
> Created at: 2024-01-07 21:41:23 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880184495  

> > spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
> > :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
> Instead of using sourceforge, using `https://archives.boost.io/release` works for me  
  
Thanks! Also for me!

---

## Comment 62

> Username: bachngocnhat  
> Created at: 2024-01-08 06:32:36 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880449125  

> > spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
> > :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
> Instead of using sourceforge, using `https://archives.boost.io/release` works for me  
  
https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2  Thanks! Also for me!

---

## Comment 63

> Username: petervn  
> Created at: 2024-01-08 09:17:22 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880627104  

diff --git a/node_modules/react-native/third-party-podspecs/boost.podspec b/node_modules/react-native/third-party-podspecs/boost.podspec  
index 3d9331c..b1e2c6a 100644  
--- a/node_modules/react-native/third-party-podspecs/boost.podspec  
+++ b/node_modules/react-native/third-party-podspecs/boost.podspec  
@@ -10,7 +10,7 @@ Pod::Spec.new do |spec|  
   spec.homepage = 'http://www.boost.org'  
   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
   spec.authors = 'Rene Rivera'  
-  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
+  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
   
   # Pinning to the same version as React.podspec.  
   ----------  
   This patch works

---

## Comment 64

> Username: ts-ign0re  
> Created at: 2024-01-08 09:40:09 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880660453  

Guys!   
  
DO NOT REPLACE HASHSUM  
  
Replace this url  
`https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2`  
  
with   
`https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2`

---

## Comment 65

> Username: olegderecha  
> Created at: 2024-01-08 10:21:18 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880725652  

Tried some minutes ago:  
In `node_modules/react-native/third-party-podspecs/boost.podspec` file:  
```  
  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
```  
  
`Podfile.lock` was changed to:  
```  
-  boost: 57d2868c099736d80fcd648bf211b4431e51a558  
+  boost: 64032b9e9b938fda23325e68a3771f0fabf414dc  
```  
  
Patch file: e.g. `patches/react-native+0.72.5.patch` (depends on installed version)  
```  
diff --git a/node_modules/react-native/third-party-podspecs/boost.podspec b/node_modules/react-native/third-party-podspecs/boost.podspec  
index 3d9331c..b1e2c6a 100644  
--- a/node_modules/react-native/third-party-podspecs/boost.podspec  
+++ b/node_modules/react-native/third-party-podspecs/boost.podspec  
@@ -10,7 +10,7 @@ Pod::Spec.new do |spec|  
   spec.homepage = 'http://www.boost.org'  
   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
   spec.authors = 'Rene Rivera'  
-  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
+  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
   # Pinning to the same version as React.podspec.  
```

---

## Comment 66

> Username: sindhusingh  
> Created at: 2024-01-08 10:23:34 UTC  
> Updated at: 2024-01-08 11:00:50 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880728907  

> Guys!  
>   
> DO NOT REPLACE HASHSUM  
>   
> Replace this url `https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2`  
>   
> with `https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2`  
  
Great! The temporary solution, as highlighted by others earlier, is indeed effective. Nevertheless, the challenge remains in having to apply this solution with every fresh package installation.   
  
It seems that the patch method mentioned at https://github.com/facebook/react-native/issues/42180 is currently the most viable solution.

---

## Comment 67

> Username: Ohmniox  
> Created at: 2024-01-08 10:35:34 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880747192  

> > spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
> > :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
> Instead of using sourceforge, using `https://archives.boost.io/release` works for me  
  
Replacing it to https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2 works   
Thanks a lot!!!

---

## Comment 68

> Username: matteolucinip  
> Created at: 2024-01-08 11:27:00 UTC  
> Updated at: 2024-01-08 15:13:58 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880822649  

After patching `node_modules/react-native/third-party-podspecs/boost.podspec` (with `yarn patch`) I still get an error because of mismatching checksums.  
> STDOUT Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  
  
  
I'm on `react-native@0.71.14` so I've replaced `https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2`  
with   
`https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2`  
  
Edit:  
My problems were due to a bug in yarn which wouldn't apply the patch correctly after installing because we are using resolutions. In case anyone else has this problem, I was able to fix it by editing the resolutions generated by yarn following this comment: https://github.com/yarnpkg/berry/issues/4231#issuecomment-1470124509

---

## Comment 69

> Username: ArindamRayMukherjee  
> Created at: 2024-01-08 11:37:36 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880836836  

> Ok, created a simple patch for this for RN 0.73.1, but everyone could do this  
>   
> 1. **Install `patch-package`:**  
>    ```shell  
>    npm install --save-dev patch-package postinstall-postinstall  
>    ```  
> 2. **Modify `boost.podspec`:**  
>   
> change  
>   
> ```ruby  
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.83.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = min_supported_versions  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> ```  
>   
> to  
>   
> ```ruby  
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.83.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   # Patched due to issue https://github.com/boostorg/boost/issues/843  
>   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = min_supported_versions  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> ```  
>   
> 3. **Create a Patch:**  
>      
>    * Run `npx patch-package react-native` to create a patch file based on your changes. This command generates a patch file in a directory called `patches/`.  
> 4. **Apply the Patch Automatically:**  
>      
>    * Modify your `package.json` to apply the patch after installation. Add the following to your scripts section:  
>      ```json  
>      "scripts": {  
>        "postinstall": "patch-package"  
>      }  
>      ```  
>    * Now, whenever you run `npm install`, `patch-package` will automatically apply the patch to the `boost.podspec` file.  
  
Ignorant question perhaps, but how and why does this also work for the Android side of a RN app? (If it does)

---

## Comment 70

> Username: cortinico  
> Created at: 2024-01-08 11:43:34 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880844807  

Hey all,  
Nicola here from the React Native team.  
Here is the official recommendation on how to overcome this failure:  
- https://github.com/facebook/react-native/issues/42180

---

## Comment 71

> Username: OmkarK45  
> Created at: 2024-01-08 12:34:41 UTC  
> Updated at: 2024-01-08 12:41:19 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880923947  

I am facing this error when trying to install patch 0.72, can someone help?   
  
### Error  
```  
NoMethodError - undefined method `=~' for []:Array  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `block (2 levels) in merged_xcconfigs'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `all?'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `block in merged_xcconfigs'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `each'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `each_with_object'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `merged_xcconfigs'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:1057:in `block in <class:PodTargetSettings>'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:120:in `block in define_build_settings_method'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:177:in `block in remove_pod_target_xcconfig_overrides_from_target'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:174:in `each'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:174:in `remove_pod_target_xcconfig_overrides_from_target'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:157:in `block in add_target'  
<internal:kernel>:90:in `tap'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:156:in `add_target'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:47:in `block in install!'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:149:in `message'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:40:in `install!'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:115:in `block in install_pod_targets'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:112:in `map'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:112:in `install_pod_targets'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:32:in `block in install_all_pod_targets'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:149:in `message'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:31:in `install_all_pod_targets'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:19:in `generate!'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:314:in `block in create_and_save_projects'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:64:in `section'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:309:in `create_and_save_projects'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:301:in `generate_pods_project'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:180:in `integrate'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:167:in `install!'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/command/install.rb:52:in `run'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/claide-1.1.0/lib/claide/command.rb:334:in `run'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/command.rb:52:in `run'  
/Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/bin/pod:55:in `<top (required)>'  
/Users/vagrant/.asdf/installs/ruby/3.2/bin/pod:25:in `load'  
/Users/vagrant/.asdf/installs/ruby/3.2/bin/pod:25:in `<main>'  
```  
  
 CocoaPods : 1.11.3  
   
   
```  
cocoapods-deintegrate : 1.0.5  
cocoapods-plugins     : 1.0.0  
cocoapods-search      : 1.0.1  
cocoapods-trunk       : 1.6.0  
cocoapods-try         : 1.2.0  
slather               : 2.7.4  
```

---

## Comment 72

> Username: olegderecha  
> Created at: 2024-01-08 13:09:34 UTC  
> Updated at: 2024-01-08 13:10:45 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880978004  

> I am facing this error when trying to install patch 0.72, can someone help?  
>   
> ### Error  
> ```  
> NoMethodError - undefined method `=~' for []:Array  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `block (2 levels) in merged_xcconfigs'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `all?'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `block in merged_xcconfigs'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `each'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `each_with_object'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `merged_xcconfigs'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:1057:in `block in <class:PodTargetSettings>'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:120:in `block in define_build_settings_method'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:177:in `block in remove_pod_target_xcconfig_overrides_from_target'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:174:in `each'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:174:in `remove_pod_target_xcconfig_overrides_from_target'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:157:in `block in add_target'  
> <internal:kernel>:90:in `tap'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:156:in `add_target'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:47:in `block in install!'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:149:in `message'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:40:in `install!'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:115:in `block in install_pod_targets'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:112:in `map'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:112:in `install_pod_targets'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:32:in `block in install_all_pod_targets'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:149:in `message'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:31:in `install_all_pod_targets'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:19:in `generate!'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:314:in `block in create_and_save_projects'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:64:in `section'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:309:in `create_and_save_projects'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:301:in `generate_pods_project'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:180:in `integrate'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:167:in `install!'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/command/install.rb:52:in `run'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/claide-1.1.0/lib/claide/command.rb:334:in `run'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/command.rb:52:in `run'  
> /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/bin/pod:55:in `<top (required)>'  
> /Users/vagrant/.asdf/installs/ruby/3.2/bin/pod:25:in `load'  
> /Users/vagrant/.asdf/installs/ruby/3.2/bin/pod:25:in `<main>'  
> ```  
>   
> CocoaPods : 1.11.3  
>   
> ```  
> cocoapods-deintegrate : 1.0.5  
> cocoapods-plugins     : 1.0.0  
> cocoapods-search      : 1.0.1  
> cocoapods-trunk       : 1.6.0  
> cocoapods-try         : 1.2.0  
> slather               : 2.7.4  
> ```  
  
@OmkarK45 , what is your `react-native` version and what boost version is defined in `node_modules/react-native/third-party-podspecs/boost.podspec`?

---

## Comment 73

> Username: OmkarK45  
> Created at: 2024-01-08 13:14:19 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880985448  

> > I am facing this error when trying to install patch 0.72, can someone help?  
> > ### Error  
> > ```  
> > NoMethodError - undefined method `=~' for []:Array  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `block (2 levels) in merged_xcconfigs'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `all?'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:446:in `block in merged_xcconfigs'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `each'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `each_with_object'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:444:in `merged_xcconfigs'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:1057:in `block in <class:PodTargetSettings>'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/target/build_settings.rb:120:in `block in define_build_settings_method'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:177:in `block in remove_pod_target_xcconfig_overrides_from_target'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:174:in `each'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:174:in `remove_pod_target_xcconfig_overrides_from_target'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:157:in `block in add_target'  
> > <internal:kernel>:90:in `tap'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:156:in `add_target'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:47:in `block in install!'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:149:in `message'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator/pod_target_installer.rb:40:in `install!'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:115:in `block in install_pod_targets'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:112:in `map'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/pods_project_generator.rb:112:in `install_pod_targets'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:32:in `block in install_all_pod_targets'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:149:in `message'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:31:in `install_all_pod_targets'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer/xcode/single_pods_project_generator.rb:19:in `generate!'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:314:in `block in create_and_save_projects'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/user_interface.rb:64:in `section'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:309:in `create_and_save_projects'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:301:in `generate_pods_project'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:180:in `integrate'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/installer.rb:167:in `install!'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/command/install.rb:52:in `run'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/claide-1.1.0/lib/claide/command.rb:334:in `run'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/lib/cocoapods/command.rb:52:in `run'  
> > /Users/vagrant/.asdf/installs/ruby/3.2.2/lib/ruby/gems/3.2.0/gems/cocoapods-1.11.3/bin/pod:55:in `<top (required)>'  
> > /Users/vagrant/.asdf/installs/ruby/3.2/bin/pod:25:in `load'  
> > /Users/vagrant/.asdf/installs/ruby/3.2/bin/pod:25:in `<main>'  
> > ```  
> >   
> >   
> >       
> >         
> >       
> >   
> >         
> >       
> >   
> >       
> >     
> > CocoaPods : 1.11.3  
> > ```  
> > cocoapods-deintegrate : 1.0.5  
> > cocoapods-plugins     : 1.0.0  
> > cocoapods-search      : 1.0.1  
> > cocoapods-trunk       : 1.6.0  
> > cocoapods-try         : 1.2.0  
> > slather               : 2.7.4  
> > ```  
>   
> @OmkarK45 , what is your `react-native` version and what boost version is defined in `node_modules/react-native/third-party-podspecs/boost.podspec`?  
  
React Native Version : 0.72.7  
  
 spec.version = '1.76.0'  
 @olegderecha

---

## Comment 74

> Username: olegderecha  
> Created at: 2024-01-08 13:20:25 UTC  
> Updated at: 2024-01-08 13:21:33 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1880994616  

@OmkarK45 ,  
  
>  React Native Version : 0.72.7  
>  spec.version = '1.76.0'  
  
I suppose, you need:  
- create `patches/react-native+0.72.7.patch` file  
- place there:  
```  
diff --git a/node_modules/react-native/third-party-podspecs/boost.podspec b/node_modules/react-native/third-party-podspecs/boost.podspec  
index 3d9331c..bbbb738 100644  
--- a/node_modules/react-native/third-party-podspecs/boost.podspec  
+++ b/node_modules/react-native/third-party-podspecs/boost.podspec  
@@ -10,7 +10,7 @@ Pod::Spec.new do |spec|  
   spec.homepage = 'http://www.boost.org'  
   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
   spec.authors = 'Rene Rivera'  
-  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
+  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
   # Pinning to the same version as React.podspec.  
```  
- run `npx patch-package`.  
  
Or, if you need to run it just locally, you can open `node_modules/react-native/third-party-podspecs/boost.podspec` and place there:  
```  
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
  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = { :ios => '11.0' }  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
```

---

## Comment 75

> Username: OmkarK45  
> Created at: 2024-01-08 13:28:28 UTC  
> Updated at: 2024-01-08 13:30:31 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881007864  

@olegderecha Thanks for the details solution. Here's the patch file that I've right now   
  
```  
diff --git a/node_modules/react-native/third-party-podspecs/boost.podspec b/node_modules/react-native/third-party-podspecs/boost.podspec  
index 3d9331c..bbbb738 100644  
--- a/node_modules/react-native/third-party-podspecs/boost.podspec  
+++ b/node_modules/react-native/third-party-podspecs/boost.podspec  
@@ -10,7 +10,7 @@ Pod::Spec.new do |spec|  
   spec.homepage = 'http://www.boost.org'  
   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
   spec.authors = 'Rene Rivera'  
-  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
+  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
   # Pinning to the same version as React.podspec.  
```  
  
the only difference between the one you provided and the one I have is this line `index 3d9331c..bbbb738 100644`  
  
I can see boost being in installed in CI   
<img width="270" alt="image" src="https://github.com/boostorg/boost/assets/45557594/771f3c3a-fef0-42f9-b1d2-87060c70ecfa">  
  
  
but the error appears after this

---

## Comment 76

> Username: olegderecha  
> Created at: 2024-01-08 13:51:07 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881051554  

@OmkarK45 , if you update this file `node_modules/react-native/third-party-podspecs/boost.podspec` and the issue still occurs, the issues is not with a patch.  
Try:  
 - rename `Pods` dir  
- try to install new Pods  
- try to remove `Podfile.lock` and `pod install` again.

---

## Comment 77

> Username: AndrySantos01  
> Created at: 2024-01-08 15:07:01 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881190419  

If anyone hasn't been able to solve it yet, I suggest this link from a member of the react native team, I managed to solve my problem but it's a temporary solution, I'm on version 0.68.  
  
link -> https://github.com/facebook/react-native/issues/42180

---

## Comment 78

> Username: OmkarK45  
> Created at: 2024-01-08 15:40:04 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881257737  

> @OmkarK45 , if you update this file `node_modules/react-native/third-party-podspecs/boost.podspec` and the issue still occurs, the issues is not with a patch. Try:  
>   
> * rename `Pods` dir  
> * try to install new Pods  
> * try to remove `Podfile.lock` and `pod install` again.  
  
You were right, the issue was with cocoapods version, I installed 1.13 and the error went away

---

## Comment 79

> Username: keShraa  
> Created at: 2024-01-08 15:48:08 UTC  
> Updated at: 2024-01-08 18:06:51 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881299651  

If you don't want to introduce a patch with `patch-package`, you can simply upgrade your RN version:  
[[Workaround Available] Error installing boost Verification checksum was incorrect.](https://github.com/facebook/react-native/issues/42180#top) #42180  
  
It has been fixed for React Native ([0.70](https://github.com/facebook/react-native/pull/42178)), [0.71](https://github.com/facebook/react-native/pull/42177), [0.72](https://github.com/facebook/react-native/pull/42175) and [0.73](https://github.com/facebook/react-native/pull/42176).

---

## Comment 80

> Username: wrldh  
> Created at: 2024-01-08 17:32:26 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881532103  

Unfortunately it wasn't fixed for 0.71

---

## Comment 81

> Username: keShraa  
> Created at: 2024-01-08 18:08:34 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1881585181  

> Unfortunately it wasn't fixed for 0.71  
  
Hope [this can help you](https://github.com/facebook/react-native/pull/42177#issuecomment-1881271147):  
> They've released the fix for RN version 0.72 (0.72.9) ~40 mins ago at the time of writing this. I can only assume the fixes for 0.73, 0.71 will follow shortly

---

## Comment 82

> Username: SHINGPH  
> Created at: 2024-01-09 05:23:20 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1882431674  

Hi i have fixed the issue of react native versionsum on boost now i have another problem on android which is  
  
1: Task failed with an exception.  
-----------  
* What went wrong:  
Execution failed for task ':react-native-reanimated:prepareBoost'.  
> Could not read appname/node_modules/react-native-reanimated/android/build/downloads/boost_1_76_0.tar.gz.  
   > Not in GZIP format  
  
  **and now i have downloaded the boost 1_76_0 on sourceforge website and added it to appname/node_modules/react-native-reanimated/android/build/downloads/boost_1_76_0.tar.gz. and now here is the error.**   
  
/node_modules/react-native/ReactCommon/cxxreact/CxxModule.h:15:10: fatal error: 'folly/dynamic.h' file not found  
  #include <folly/dynamic.h>  
           ^~~~~~~~~~~~~~~~~  
  1 error generated.

---

## Comment 83

> Username: mclow  
> Created at: 2024-01-09 05:25:25 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1882433186  

Folly is not part of Boost; it comes from Facebook.  See https://github.com/facebook/folly

---

## Comment 84

> Username: arnoldc  
> Created at: 2024-01-09 10:09:37 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1882774634  

how it was fixed? do we have official comment from the team?  
im wondering if this issue would happen again

---

## Comment 85

> Username: userdocs  
> Created at: 2024-01-09 10:32:00 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-1882812947  

> how it was fixed? do we have official comment from the team? I'm wondering if this issue would happen again  
  
At this point you wonder if the maintainers only knew it was working again from comments in issue. They have really done an absurdly impressive job of ignoring this issue and all requests for guidance or clarification on how to proceed. Full head in the sand mode.   
  
I mean, how many time does a dev with a significant project have to professionally and politely ask this question, almost daily, without being ignored?   
 https://github.com/boostorg/boost/issues/845#issuecomment-1880596544  
  
This will happen again as it's been an ongoing problem for this project for too long while they insist of using these vanity projects to host the source code people need.  
  
Your only option at this point is to factor in your own redundancy because the most realisable takeaway from this repeat issue is that  
  
- they will ignore you when it does happen letting you figure out your own emergency solution  
- they will refuse to implement any sane solutions prevent the issue being this wide. Just as it was 2-3 years ago.  
- it will keep happening and they never seem to know why  
- they will blame end users for downloading as the reason it's unavailable.  
  
Another defining moment in opensource software development of how to not do something.

---

## Comment 86

> Username: gregoripolak  
> Created at: 2025-01-01 11:45:11 UTC  
> Updated at: 2025-01-01 12:14:16 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2566973441  

Hi this issue is currently appearing again for us, looks like jfrog is having a hard time with the new years.  
  
`React-Native 0.68.1`  
  
```  
Installing boost 1.76.0  
  
[!] Error installing boost  
Verification checksum was incorrect, expected  
Couldn't install Pods. Updating the Pods project and trying again...  
Command pod install failed.  
└─ Cause: Error installing boost  
Verification checksum was incorrect, expected  
```

---

## Comment 87

> Username: sdarwin  
> Created at: 2025-01-01 14:38:59 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2567034014  

Hi @gregoripolak ,  
  
The url is now:    
  
https://archives.boost.io/  
https://archives.boost.io/release/1.87.0/source/boost_1_87_0.tar.bz2  
  
JFrog was donating bandwidth. After years, they have requested we switch to another service, to mitigate costs.

---

## Comment 88

> Username: userdocs  
> Created at: 2025-01-01 14:53:33 UTC  
> Updated at: 2025-01-01 15:03:24 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2567038817  

The official URL is still jfrog here: https://www.boost.org/users/download/  
  
The URL that was changed to https://archives.boost.io/ the last time this happened, now changed back to jfrog  
  
edited for clarity:

---

## Comment 89

> Username: sdarwin  
> Created at: 2025-01-01 14:59:25 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2567040705  

@userdocs very interesting! Previous versions (1.86.0 etc) switched to the new download link.  This needs to be fixed.

---

## Comment 90

> Username: userdocs  
> Created at: 2025-01-01 15:05:36 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2567043177  

@sdarwin yeah, I rephrased my comment The download page itself does not link to https://archives.boost.io/ nor the newer github latest assets.  
  
That info could avoid non packaged boost related issues when jfrog shutdown last night.

---

## Comment 91

> Username: Akifcan  
> Created at: 2025-01-01 19:47:59 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2567125641  

> [@sdarwin](https://github.com/sdarwin) yeah, I rephrased my comment The download page itself does not link to https://archives.boost.io/ nor the newer github latest assets.  
>   
> That info could avoid non packaged boost related issues when jfrog shutdown last night.  
  
Thank you this is work for me:  
  
[root]/node_modules/react-native/third-party-podspecs/boost.podspec  
  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
  
line: 13  
react native version: 0.71.11

---

## Comment 92

> Username: jpieper  
> Created at: 2025-01-04 22:49:57 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2571431662  

> Hi [@gregoripolak](https://github.com/gregoripolak) ,  
>   
> The url is now:  
>   
> https://archives.boost.io/ https://archives.boost.io/release/1.87.0/source/boost_1_87_0.tar.bz2  
>   
> JFrog was donating bandwidth. After years, they have requested we switch to another service, to mitigate costs.  
  
Any chance you could get JFrog to do a DNS forward to the new location?  That should be pretty cheap and would keep many things working.

---

## Comment 93

> Username: jtvargas  
> Created at: 2025-01-06 16:56:43 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2573516469  

ok, so why on local `pod install` works without issues and then in my CI is not working ? I don't understand that 🤔

---

## Comment 94

> Username: genesiscz  
> Created at: 2025-01-06 17:09:30 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2573540640  

> ok, so why on local `pod install` works without issues and then in my CI is not working ? I don't understand that 🤔  
  
Because you have the package cached and is not required to re-download. CI is probably uncached

---

## Comment 95

> Username: genesiscz  
> Created at: 2025-01-06 17:33:41 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2573584459  

My patch file loosk like:  
  
```  
diff --git a/third-party-podspecs/boost.podspec b/third-party-podspecs/boost.podspec  
index 3d9331c95d1217682a0b820a0d9440fdff074ae0..bbbb7380e51a12d1b2994c8ab8e8f682c5e6d83d 100644  
--- a/third-party-podspecs/boost.podspec  
+++ b/third-party-podspecs/boost.podspec  
@@ -10,7 +10,7 @@ Pod::Spec.new do |spec|  
   spec.homepage = 'http://www.boost.org'  
   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
   spec.authors = 'Rene Rivera'  
-  spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2',  
+  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                   :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
   
   # Pinning to the same version as React.podspec.  
```  
  
into patches/react-native@0.72.7.patch (or your own version)

---

## Comment 96

> Username: jtvargas  
> Created at: 2025-01-06 17:36:19 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2573588828  

is there another solution instead of the `patch-package` ? 👀  I would not like to do the `patch-package`

---

## Comment 97

> Username: EnginKoyuncu  
> Created at: 2025-01-07 09:10:52 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2574760623  

> Ok, created a simple patch for this for RN 0.73.1, but everyone could do this  
>   
> 1. **Install `patch-package`:**  
>    npm install --save-dev patch-package postinstall-postinstall  
> 2. **Modify `boost.podspec`:**  
>   
> change  
>   
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.83.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = min_supported_versions  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> to  
>   
> # Copyright (c) Meta Platforms, Inc. and affiliates.  
> #  
> # This source code is licensed under the MIT license found in the  
> # LICENSE file in the root directory of this source tree.  
>   
> Pod::Spec.new do |spec|  
>   spec.name = 'boost'  
>   spec.version = '1.83.0'  
>   spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
>   spec.homepage = 'http://www.boost.org'  
>   spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
>   spec.authors = 'Rene Rivera'  
>   # Patched due to issue https://github.com/boostorg/boost/issues/843  
>   spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2',  
>                   :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
>   # Pinning to the same version as React.podspec.  
>   spec.platforms = min_supported_versions  
>   spec.requires_arc = false  
>   
>   spec.module_name = 'boost'  
>   spec.header_dir = 'boost'  
>   spec.preserve_path = 'boost'  
> end  
> 3. **Create a Patch:**  
>      
>    * Run `npx patch-package react-native` to create a patch file based on your changes. This command generates a patch file in a directory called `patches/`.  
> 4. **Apply the Patch Automatically:**  
>      
>    * Modify your `package.json` to apply the patch after installation. Add the following to your scripts section:  
>      "scripts": {  
>        "postinstall": "patch-package"  
>      }  
>    * Now, whenever you run `npm install`, `patch-package` will automatically apply the patch to the `boost.podspec` file.  
  
Thanks for the fix! Anyone using 'App Center', follow these steps: Commit the file 'patches/react-native+0.72.6.patch' to your branch and let 'App Center' do the work. Just to be sure, after running 'yarn install' or 'npm i' locally, verify that you’ve got this applied.  
![Image](https://github.com/user-attachments/assets/5f5459cc-86da-4ecf-9772-71d40d0eb577)

---

## Comment 98

> Username: Lazar4o  
> Created at: 2025-01-07 10:27:50 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2574930648  

Could someone explain to me how this error appears out of nowhere? It seems that no one on my team is experiencing this issue.

---

## Comment 99

> Username: sdarwin  
> Created at: 2025-01-07 11:46:40 UTC  
> Updated at: 2025-01-07 11:48:11 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2575086022  

@Lazar4o   
  
> It seems that no one on my team is experiencing this issue.  
  
Great.  
  
> Could someone explain to me how this error appears out of nowhere?  
  
The boost download was hosted on JFrog until December 31, 2024 when the service ended. Therefore the problem "appeared out of nowhere" on Jan 1, 2025.    
  
Although not completely out of nowhere. the switch was announced in the mailing list, in github issues, and boost.org has pointed to the new download links for 6 months.

---

## Comment 100

> Username: meramsey  
> Created at: 2025-01-15 15:01:15 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2593096058  

for anyone else having issues still i stumbled onto easier fix for Podfile  vs patch-package approach might be worth trying if not able to upgrade yet  
  
https://github.com/boostorg/boost/issues/996#issuecomment-2574671532

---

## Comment 101

> Username: edouard-claude  
> Created at: 2025-01-20 12:19:50 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2602280364  

Fixed   
https://www.boost.org/users/history/version_1_76_0.html  
  
```  
Pod::Spec.new do |spec|  
  spec.name = 'boost'  
  spec.version = '1.76.0'  
  spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
  spec.homepage = 'http://www.boost.org'  
  spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
  spec.authors = 'Rene Rivera'  
  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = { :ios => '11.0' }  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
```

---

## Comment 102

> Username: misaelriojasftf  
> Created at: 2025-01-22 10:13:20 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2606820291  

## Errors  
> move to node_modules/react-native/third-party-podspecs. Only change line spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2', :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' } to spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2', :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
> Maybe jfrog close their server  
  
In my case updating the version  didn't work. it throw an error  
  
```  
[Hermes] Using the release tarball from Maven Central  
[!] CocoaPods could not find compatible versions for pod "boost":  
  In Podfile:  
    RCT-Folly (from `../node_modules/react-native/third-party-podspecs/RCT-Folly.podspec`) was resolved to 2021.07.22.00, which depends on  
      boost  
  
    React-jsi (from `../node_modules/react-native/ReactCommon/jsi`) was resolved to 0.72.7, which depends on  
      boost (= 1.76.0)  
  
    boost (from `../node_modules/react-native/third-party-podspecs/boost.podspec`)  
```  
  
By trying to understand the error it quite obviously the checksum doesn't match and even if I force it to match it throws another error  
  
## Solution  
  
use a patch and change the url of the boost file. It looks someone updated the frog link somehow not sure but even forcing to the actual checksum it still throws error like file is not correct format  
  
  
so go here https://www.boost.org/users/history/ where you can find all the version. find the one you need and check the checksum   
  
in my case this was the file link and this was the checksum and interesting is the checksum did match with the legacy one I got initially from react native  
  
<img width="632" alt="Image" src="https://github.com/user-attachments/assets/35de180d-f3d2-46c2-a0d2-a99d5c50b396" />  
  
  
just paste the link instead of the frog one and it works!   
  
<img width="694" alt="Image" src="https://github.com/user-attachments/assets/c3a6b652-5e2f-4eda-bf88-2ccf596317f3" />  
  
<img width="999" alt="Image" src="https://github.com/user-attachments/assets/c9b7634b-8c19-43b9-b85f-9f1c7880a573" />

---

## Comment 103

> Username: kabeerx9  
> Created at: 2025-02-23 08:30:34 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2676706208  

changing in manually works but what if we do a fresh install , it then tries using the old url that was there using which checksum do not match

---

## Comment 104

> Username: dgreasi  
> Created at: 2025-02-27 10:19:48 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2687509313  

Any updates on that?

---

## Comment 105

> Username: kabeerx9  
> Created at: 2025-02-27 10:30:09 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2687535256  

patch the package.

---

## Comment 106

> Username: jonp-iversoft  
> Created at: 2025-02-27 18:58:22 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2688840591  

as an update, for boost 1.76.0 the boost.podspec file should be   
  
```  
# Copyright (c) Meta Platforms, Inc. and affiliates.  
#  
# This source code is licensed under the MIT license found in the  
# LICENSE file in the root directory of this source tree.  
  
Pod::Spec.new do |spec|  
  spec.name = 'boost'  
  spec.version = '1.76.0'  
  spec.license = { :type => 'Boost Software License', :file => "LICENSE_1_0.txt" }  
  spec.homepage = 'http://www.boost.org/'  
  spec.summary = 'Boost provides free peer-reviewed portable C++ source libraries.'  
  spec.authors = 'Rene Rivera'  
  spec.source = { :http => 'https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
  # Pinning to the same version as React.podspec.  
  spec.platforms = { :ios => '11.0', :tvos => "12.4" }  
  spec.requires_arc = false  
  
  spec.module_name = 'boost'  
  spec.header_dir = 'boost'  
  spec.preserve_path = 'boost'  
end  
```

---

## Comment 107

> Username: LeonardoFreitag  
> Created at: 2025-02-27 22:36:51 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2689260720  

> 2\. boost.podspec  
  
Onde localizo este arquivo?

---

## Comment 108

> Username: AliHaidar110  
> Created at: 2025-03-11 18:32:46 UTC  
> Updated at: 2025-03-11 19:35:05 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2715353891  

yep try using a vpn solved it for me after that I had to turn it of for another package :)

---

## Comment 109

> Username: gauravsbagul  
> Created at: 2025-04-08 06:19:58 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2785349660  

> move to node_modules/react-native/third-party-podspecs. Only change line spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2', :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' } to spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2', :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }  
>   
> Maybe jfrog close their server  
  
also after doing this change if you get checksum mismatch issue, replace `6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e` this with expected checksum

---

## Comment 110

> Username: michtntbrighte  
> Created at: 2025-04-09 01:34:41 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2788059182  

How to fix it for now is:  
1. check what version your boost.podspec is using  
2. compare the SHA and link from the Boost official website  
  
In my case I'm using 1.76, the url I use is https://archives.boost.io/release/1.76.0/source/boost_1_76_0.tar.bz2  
  
Reference: https://www.boost.org/users/history/version_1_76_0.html

---

## Comment 111

> Username: MadelineCollier  
> Created at: 2025-04-18 08:39:52 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2814934138  

This issue is fixed in React Native `0.75`, (see fix here: https://github.com/facebook/react-native/commit/d274826fecfbb67b1b534f4ce9b511a1393625f4). I have opened a cherry-pick request to hopefully back-merge the fix to older versions of React Native (`0.71-0.74`) here: https://github.com/reactwg/react-native-releases/issues/902  
  
If this pick-request gets rejected (fairly likely as releases of RN `<=0.76.x` are now unsupported) then [this fix](https://github.com/boostorg/boost/issues/843#issuecomment-1872943124) should still work for older versions such as RN `0.71`.

---

## Comment 112

> Username: tibic  
> Created at: 2025-04-19 05:25:31 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2816546377  

我遇到同样的问题，真令人抓狂  
  
<img width="1710" alt="Image" src="https://github.com/user-attachments/assets/a889aa0f-3b51-4a66-99d0-6fe83e818747" />

---

## Comment 113

> Username: sahilkashyap64  
> Created at: 2025-04-19 09:39:20 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2816632135  

if you are still getting the error   
```  
Installing boost (1.76.0)  
  
[!] Error installing boost  
Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 79e6d3f986444e5a80afbeccdaf2d1c1cf964baa8d766d20859d653a16c39848  
```  
  
try this solution , adding a function on top of the podfile  
  
https://github.com/boostorg/boost/issues/996

---

## Comment 114

> Username: guruatdigni  
> Created at: 2025-05-21 05:37:09 UTC  
> Updated at: 2025-05-21 05:37:48 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2896661583  

don't go for pod install or cocopod update. i have spend 4 to 5 hours fixed by myself.  
  
https://www.boost.org  
  
use this website and select the version you have used currently pick the checksum (SHA256) and copy the link.

---

## Comment 115

> Username: wenyiweb  
> Created at: 2025-05-23 06:41:21 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2903426361  

1. npm install --save-dev patch-package postinstall-postinstall  
  
2. package.json  scripts add "postinstall": "patch-package"  
  
3. modify node_modules/react-native/third-party-podspecs/boost.podspec  
  
spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
:sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
  
4. npx patch-package react-native  
  
5. cd ios && pod install

---

## Comment 116

> Username: GourabSanyal  
> Created at: 2025-05-23 21:38:53 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2905872152  

was fixed when I changed the react native version from `71.14.0` to `71.15.0`.  
  
Did regular cleaning and reinstall, and everything worked fine.  
  
also you can check this out : https://github.com/facebook/react-native/issues/42180

---

## Comment 117

> Username: rodrigotoledo  
> Created at: 2025-06-03 11:54:33 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-2934889789  

> 1. npm install --save-dev patch-package postinstall-postinstall  
> 2. package.json  scripts add "postinstall": "patch-package"  
> 3. modify node_modules/react-native/third-party-podspecs/boost.podspec  
>   
> spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2', :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
>   
> 4. npx patch-package react-native  
> 5. cd ios && pod install  
  
This is a really good code to solve the problem.

---

## Comment 118

> Username: iZooto-App-Push  
> Created at: 2025-08-08 09:47:33 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-3167261515  

https://stackoverflow.com/questions/77738691/error-installing-boost-verification-checksum-was-incorrect-expected  
follow this link, fixed now bugs

---

## Comment 119

> Username: mustafazeb381-123  
> Created at: 2025-08-10 13:47:05 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-3172644877  

@wswebcreation thankyou so much sir you save my day

---

## Comment 120

> Username: Samama-Zargham  
> Created at: 2025-09-29 13:24:14 UTC  
> Url: https://github.com/boostorg/boost/issues/843#issuecomment-3346934162  

**- Go to node_modules/react-native/third-party-podspecs from the root of your project  
- check the boost.podspec file**   
  
See this line  **spec.source = { :http => 'https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2',  
                  :sha256 => '6478edfe2f3305127cffe8caf73ea0176c53769f4bf1585be237eb30798c3b8e' }**  
  
only change this url **https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.bz2**  
 to this   
**https://sourceforge.net/projects/boost/files/boost/1.83.0/boost_1_83_0.tar.bz2**  
  
**and than make patch by installing   
patch-package postinstall-postinstall   
Run this Comand to create patch   
npx patch-package react-native  
  
and now BOOM😃 install pods by running  
cd ios pod install**
