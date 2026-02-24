# #667 - Prepare for release with Boost 1.80 [Closed]

> Username: mloskot  
> Created at: 2022-05-12 07:12:51 UTC  
> Updated at: 2022-08-11 21:44:46 UTC  
> Closed at: 2022-08-11 19:59:04 UTC  
> Url: https://github.com/boostorg/gil/issues/667  

Following the https://github.com/boostorg/gil/discussions/663 discussion, we are going to perform the grand true merge of `develop` into `master` in order to prepare for release all of the fixes and features (GSoC too) that have been developed over the [Boost 1.76.0](https://www.boost.org/users/history/version_1_76_0.html), that is the last Boost release with anything GIL.  
  
Steps:  
1. [x] Merge  
2. [x] Build locally before `git push`  
3. [x] Push  
4. [x] Review CI status  
5. [x] Collect release notes to `RELEASES.md`  
6. [x] Copy release notes to https://github.com/boostorg/website/  (_special_ kudos go to @striezel for his huge helps with the docs and release notes!)  
  
### Changes  
  
Below is initial, as on 12 May 2022, log of commits in form convenient to review that will merged from current develop into master for release with Boost 1.80:  
  
```console  
git log origin/master..develop --reverse --pretty=format:" | %cs | [%h](http://github.com/boostorg/gil/commit/%H) | %s |" | nl | sed -E 's/^\s/\|/g'  
```  
  
| #   | Date | Hash | Subject |  
| --- | ---  | ---  | ---     |  
|    1	 | 2020-10-05 | [f6a35532f](http://github.com/boostorg/gil/commit/f6a35532f05963804c83f6519955c6fd283a3978) | Fix typo in copyright headers (#521) |  
|    2	 | 2020-10-10 | [f7e83e0ba](http://github.com/boostorg/gil/commit/f7e83e0bafac9413b8e7bd7c29af7a5bcbaa16bd) | Fix conversion of rgb to signed cmyk(boostorg#479) (#522) |  
|    3	 | 2020-10-11 | [f374a672a](http://github.com/boostorg/gil/commit/f374a672a0a6b9be7a6ea7eaa0e8b7136bae9ae5) | Add image constructor from compatible view (#520) |  
|    4	 | 2020-11-10 | [8b1c2d3ea](http://github.com/boostorg/gil/commit/8b1c2d3ea4eb5bf0a1e7e093862962313dd6c2bb) | Boost 1.75 release notes: plan to phase out GCC 5 and introduce C++14 |  
|    5	 | 2020-11-22 | [6fc7d7541](http://github.com/boostorg/gil/commit/6fc7d7541d1bb16b7cca1835129c13fe8ed8142c) | Removing unnecessary cast in hsv.cpp (#530) |  
|    6	 | 2020-11-22 | [09911cd1c](http://github.com/boostorg/gil/commit/09911cd1c44020e82cd8f14280f589512668bbaa) | ci: Boost.Numeric is no longer a dependency [ci skip] |  
|    7	 | 2020-11-22 | [8d0490630](http://github.com/boostorg/gil/commit/8d049063035e86ad614215a7cbb67b2b99816266) | docs: Update CONTRIBUTING.md [ci skip] |  
|    8	 | 2020-12-16 | [2b1e4665d](http://github.com/boostorg/gil/commit/2b1e4665d15bc5c58d657879ac52890611c7d77e) | Add "cxxstd" json field (#531) |  
|    9	 | 2021-01-04 | [e1c69d171](http://github.com/boostorg/gil/commit/e1c69d1718b902d3ecb00d83ec4683494919b751) | Add codecov coverage (#532) |  
|   10	 | 2021-01-11 | [0c0fe1ae7](http://github.com/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c) | build: Switch conan packages from bincrafters to conan.io (#537) |  
|   11	 | 2021-01-20 | [68cdbdd2e](http://github.com/boostorg/gil/commit/68cdbdd2ec4e21f667a49ae1dbbc2cebd833051c) | Add inverse function for matrix3x2 (#527) |  
|   12	 | 2021-01-20 | [853bc1266](http://github.com/boostorg/gil/commit/853bc1266b3d287bafbb571e12d00e01df6a8734) | Fix typo in copyright headers (#524) |  
|   13	 | 2021-01-21 | [eef475e27](http://github.com/boostorg/gil/commit/eef475e27a91df8c912fbf82f330d1af955743ce) | ci: Temporarily set B2_VERBOSE=0 for CircleCI |  
|   14	 | 2021-01-22 | [470923be3](http://github.com/boostorg/gil/commit/470923be362881f5ea9bd72414a95690f8cceb85) | Fix default ctor of homogeneous_color_base for reference pixel elements (#542) |  
|   15	 | 2021-01-22 | [cb5bc9d8c](http://github.com/boostorg/gil/commit/cb5bc9d8c233374736118290f45ac08172fa835c) | Add Perona–Malik anisotropic diffusion algorithm (#500) |  
|   16	 | 2021-01-23 | [b23c5e75b](http://github.com/boostorg/gil/commit/b23c5e75b2999cf363a7f6af6b806c771de221b7) | doc: Display GIL is header-only in README |  
|   17	 | 2021-01-23 | [0e372a10b](http://github.com/boostorg/gil/commit/0e372a10bebd72cccbd76bb6ee2fb636c83210fe) | docs: Display GIL is header-only in README |  
|   18	 | 2021-01-24 | [3e729e5da](http://github.com/boostorg/gil/commit/3e729e5dae4c0ba6f407f6885bf6a18d525e8489) | Add histogram class and related functionality (#499) |  
|   19	 | 2021-01-24 | [fb7512c29](http://github.com/boostorg/gil/commit/fb7512c29f5c3ac3f8b44ce5925757855242d489) | Add histogram equalization feature (#514) |  
|   20	 | 2021-01-24 | [77255e9e6](http://github.com/boostorg/gil/commit/77255e9e61b517ebfceb4f9439a074e61b4ba1c5) | Add histogram matching algorithm (#515) |  
|   21	 | 2021-01-24 | [b82aed8f8](http://github.com/boostorg/gil/commit/b82aed8f8e12602e1a3537ebbdbeb6a0b357d481) | Implement hstack and vstack (#506) |  
|   22	 | 2021-01-24 | [a68a95d5f](http://github.com/boostorg/gil/commit/a68a95d5f457038d4851c900e57d430dfb09eca8) | Add code for ahe algorithm (#516) |  
|   23	 | 2021-01-25 | [81b4dc08b](http://github.com/boostorg/gil/commit/81b4dc08bd9177bf9361194a66b0e70cfd6438e8) | ci: Add configuration for GitHub Actions (#544) |  
|   24	 | 2021-01-25 | [a37f12b3e](http://github.com/boostorg/gil/commit/a37f12b3e9e90c968e2e416d33c339fa26e77226) | Add implementation of Hough transforms (#512) |  
|   25	 | 2021-01-26 | [434e78f76](http://github.com/boostorg/gil/commit/434e78f76b5179369738f25a553b74cbaf886218) | ci: R.I.P. Travis CI - Long live GitHub Actions |  
|   26	 | 2021-01-27 | [4e0f815f5](http://github.com/boostorg/gil/commit/4e0f815f5e27edb315093a3f632f611bb7ffad16) | build: Remove most of uses of cxxflags from test/Jamfile (#550) |  
|   27	 | 2021-01-28 | [6f0a06136](http://github.com/boostorg/gil/commit/6f0a061363ffa1f1ed0b2bcd509d3c0ce2a058a0) | docs: Clarify PR update procedure in CONTRIBUTING.md [ci skip] |  
|   28	 | 2021-01-28 | [5d63cf679](http://github.com/boostorg/gil/commit/5d63cf6798c3985cd02aa703274b2c9a85ff24f2) | Fix more warnings in examples |  
|   29	 | 2021-01-28 | [063385398](http://github.com/boostorg/gil/commit/063385398fe7690e8b6e6611d0d6e1b851e636d4) | Fix warnings about abs called without std qualification |  
|   30	 | 2021-01-28 | [6007d7466](http://github.com/boostorg/gil/commit/6007d74667f9079ebed4475de48906f3eef573ab) | Fix some clang -Wunused-variable warnings |  
|   31	 | 2021-01-28 | [422ca82fe](http://github.com/boostorg/gil/commit/422ca82fe58d5aed90f86a2b043a402a2dc48a53) | Removed two instaces of boost.mpl (#551) |  
|   32	 | 2021-01-30 | [2e2764225](http://github.com/boostorg/gil/commit/2e2764225f81fff3eb90e56b55635880d954f3b0) | Support constructing a planar image from interleaved image (#552) |  
|   33	 | 2021-02-02 | [90f7952b0](http://github.com/boostorg/gil/commit/90f7952b054de873b14196466c35a646b83b2575) | Fixed most of warnings in examples and some in core library (#545) |  
|   34	 | 2021-02-02 | [d923a8f37](http://github.com/boostorg/gil/commit/d923a8f37e9b2fa05b57f476076be451acb6ec9c) | Declare laplacian_function::get_directed_offsets as inline |  
|   35	 | 2021-02-02 | [c7847f4d6](http://github.com/boostorg/gil/commit/c7847f4d640e0a3ab4bea4dad53af48dd9ad57de) | Fix warning: returning reference to local temporary object (#556) |  
|   36	 | 2021-02-03 | [04daae716](http://github.com/boostorg/gil/commit/04daae7160474e9d1dbd795e2e10f761d9cfec2b) | Fix warning: comparison of integer expressions of different signedness |  
|   37	 | 2021-02-03 | [58804ecb2](http://github.com/boostorg/gil/commit/58804ecb241063947a0ec34aed972a018f2f425a) | Remove <cxxflags> and other cruft from Jamfile (#557) |  
|   38	 | 2021-02-03 | [f58f3a23a](http://github.com/boostorg/gil/commit/f58f3a23a1c009535fb8f88c36d71dee59e7d13e) | Disable warning: overlapping comparisons always evaluate to false [-Wtautological-overlap-compare] |  
|   39	 | 2021-02-04 | [8337f47ae](http://github.com/boostorg/gil/commit/8337f47aeb72e81141f3ea63834bdb1672965bb1) | Review and update includes in boost/gil.hpp (#559) |  
|   40	 | 2021-02-05 | [c3b3d35aa](http://github.com/boostorg/gil/commit/c3b3d35aaabee47df448e95cdeedc8b08f272c87) | Fix sphinx installation link in docs readme (#560) |  
|   41	 | 2021-02-09 | [2676d3180](http://github.com/boostorg/gil/commit/2676d31801c1232c2824c93e617f5c49a638e594) | ci: Disable AppVeyor job using CMake to avoid timeouts |  
|   42	 | 2021-02-13 | [1e8526797](http://github.com/boostorg/gil/commit/1e8526797e3327fd1bed7c2ace092fad5fd5ee74) | Added all standard morphological transformations (#541) |  
|   43	 | 2021-02-19 | [a8cb36449](http://github.com/boostorg/gil/commit/a8cb364492667b9a6441a6538b458aace64513c6) | doc: Add Compiling section with C++ version and compilers support |  
|   44	 | 2021-02-28 | [a82af6d25](http://github.com/boostorg/gil/commit/a82af6d25de304bd7482fdfece3b6f775f2832b9) | ci: Add Codecov to GitHub Actions (#564) |  
|   45	 | 2021-03-01 | [33ab3cfc1](http://github.com/boostorg/gil/commit/33ab3cfc1034b0a67526966b3c126faa912624d2) | added missing header guards (#568) |  
|   46	 | 2021-03-01 | [ec25950af](http://github.com/boostorg/gil/commit/ec25950aff8b953ce359cba95f711a90e3708112) | added missing copyright info (#569) |  
|   47	 | 2021-03-01 | [ad007ab92](http://github.com/boostorg/gil/commit/ad007ab92127daab26596524402e89ffbf386ccd) | ci: Add Codecov badge and codecov.yml file (#567) |  
|   48	 | 2021-03-01 | [093117d56](http://github.com/boostorg/gil/commit/093117d5650cb4f8f277dd73a00888c488447f57) | Repoint B2 refs to new non-boostorg home. (#570) |  
|   49	 | 2021-03-01 | [f18445f90](http://github.com/boostorg/gil/commit/f18445f90ba0ae05877d659c9fb369ffb55afa95) | Repoint B2 refs to new non-boostorg home. (#571) |  
|   50	 | 2021-03-02 | [0778069b8](http://github.com/boostorg/gil/commit/0778069b8e97fed6138fbd5dab6e5bb5e9a2ef37) | Switch docs deployment from Travis CI to GitHub Actions (#563) |  
|   51	 | 2021-03-02 | [8bd241312](http://github.com/boostorg/gil/commit/8bd2413127237e6fdb3a903ea662bd5e0dfc1b12) | ci: Remove GCC 5 on GitHub Actions and Azure Pipelines (#572) |  
|   52	 | 2021-03-05 | [745d033ef](http://github.com/boostorg/gil/commit/745d033ef2f382dea338851cfa6b2668f1e52905) | ci: Use GitHub Actions automatic GITHUB_TOKEN secret in docs workflow |  
|   53	 | 2021-03-09 | [392ac4996](http://github.com/boostorg/gil/commit/392ac4996d49d7ea958426438775d291b689af07) | Rotation of image by any arbitrary angle from its center (#565) |  
|   54	 | 2021-03-10 | [6da59cc33](http://github.com/boostorg/gil/commit/6da59cc3351e5657275d3a536e0b6e7a1b6ac738) | docs: Updated GA workflow to publish with peaceiris/action-gh-pages (#574) |  
|   55	 | 2021-03-18 | [b8564e256](http://github.com/boostorg/gil/commit/b8564e256c067275287792a1678a74cf81d4ae08) | Fix any_image_view::const_t (#526) |  
|   56	 | 2021-03-24 | [dc9ba74cb](http://github.com/boostorg/gil/commit/dc9ba74cb3e7801f7025902796a76172d75517bf) | Add docs for histogram (#503) |  
|   57	 | 2021-03-24 | [6e91e4bf5](http://github.com/boostorg/gil/commit/6e91e4bf5c1bc9b5def5ef60e3e11bc475cbf11d) | Update image url links for docs for histogram (#586) |  
|   58	 | 2021-03-28 | [bc3a6c0db](http://github.com/boostorg/gil/commit/bc3a6c0db9dd0b66f4842bfe55b39bc0119e4639) | Add rasterizer support for ellipse (#585) |  
|   59	 | 2021-04-02 | [cea6ef275](http://github.com/boostorg/gil/commit/cea6ef2752cbfc4c8d1991d1d96b9fc3f78d79f5) | ci: Fix availability of toolsets in updated Ubuntu images of GitHub Actions (#589) |  
|   60	 | 2021-04-02 | [2ad274cc8](http://github.com/boostorg/gil/commit/2ad274cc81f1613a0dc723a6c87a4c48dea9e35f) | ci: Remove cxxstd=14 from clang 3.5 job on GitHub Actions (#590) |  
|   61	 | 2021-04-03 | [bbdce36a1](http://github.com/boostorg/gil/commit/bbdce36a1f655ebf8332b775790673b197c8f1de) | docs: Correct typos in documentation (#592) |  
|   62	 | 2021-05-06 | [0a21d741c](http://github.com/boostorg/gil/commit/0a21d741ce06ad3a880efb0971eaf7bb51effb78) | test: Verify core IO headers are self-contained |  
|   63	 | 2021-05-11 | [4738a3829](http://github.com/boostorg/gil/commit/4738a38295b6a153e979a16e0439742c496bd559) | test: Catch up IO with switch to variant2 migration (#607) |  
|   64	 | 2021-05-23 | [d65cc5822](http://github.com/boostorg/gil/commit/d65cc582254de7065b9b2e795786f3d561a46398) | Update CONTRIBUTING.md |  
|   65	 | 2021-05-30 | [7dd61209a](http://github.com/boostorg/gil/commit/7dd61209a81695600d773f481bb8b22b0f74a30a) | Add remaining pieces of documentation in convolve.hpp (#611) |  
|   66	 | 2021-06-11 | [effd078b3](http://github.com/boostorg/gil/commit/effd078b325c1c5f408a9ae87bd695b429875d8e) | Fix C++20 incompatibilities in IO extensions |  
|   67	 | 2021-07-16 | [2cc525a2d](http://github.com/boostorg/gil/commit/2cc525a2d6a48ba7bc6be44626d9d82bafa4318d) | Fix for_each_pixel for non 1d iterable views |  
|   68	 | 2021-10-04 | [327952325](http://github.com/boostorg/gil/commit/3279523252327ac7a99296806c5a3615485955a3) | Update pixel design documentation for MP11 changes (#626) |  
|   69	 | 2021-10-05 | [c9b339e5d](http://github.com/boostorg/gil/commit/c9b339e5d0b6a1d121ff9b37ae2d7544c534dc68) | Merge pull request #617 from fmorgner/fix-c++20-ctor-incompatibility |  
|   70	 | 2021-10-30 | [a328bef94](http://github.com/boostorg/gil/commit/a328bef94de26b15ec676cf53d4c58fecf0d7dab) | Add a Boost-friendly subproject case to CMakeLists (#614) |  
|   71	 | 2021-11-08 | [b6526deb7](http://github.com/boostorg/gil/commit/b6526deb7862ffb10ec0da4739402723f7604a47) | Remove remaining references to MPL in the documentation (#631) |  
|   72	 | 2021-11-10 | [0b24f4cdb](http://github.com/boostorg/gil/commit/0b24f4cdbf430430b5430507822b0698cd9d2ac7) | Ensure all examples build without error (#628) |  
|   73	 | 2021-12-16 | [4531c3d56](http://github.com/boostorg/gil/commit/4531c3d563cfade3eafb80a09f304747845bbe8b) | docs: Fix TOC link to B2 in CONTRIBUTING.md (#632) |  
|   74	 | 2022-02-05 | [2db5fe9f4](http://github.com/boostorg/gil/commit/2db5fe9f448bbf4b9da0e38414a366ec5188c817) | Fix warning: unused variable ‘r_squared’ [-Wunused-variable] |  
|   75	 | 2022-02-05 | [0accacdbe](http://github.com/boostorg/gil/commit/0accacdbe679766f69caebecbd0f40edb607fac2) | Fix warning: comparison of integer expressions of different signedness |  
|   76	 | 2022-02-06 | [5dcdf53c0](http://github.com/boostorg/gil/commit/5dcdf53c02f6b646703b03670c66596beae905ab) | Fix warning: unused variable in diffusion.hpp (#635) |  
|   77	 | 2022-02-09 | [c7aba23c7](http://github.com/boostorg/gil/commit/c7aba23c748456f2bb679be0d6011f3db2eb94fa) | Fix convolve_2d for images with float32_t channel model (#577) |  
|   78	 | 2022-02-09 | [199520179](http://github.com/boostorg/gil/commit/199520179e1b8e8324cc130145ed7879d5190c10) | Refactor CMake test config (#634) |  
|   79	 | 2022-02-18 | [2562e1124](http://github.com/boostorg/gil/commit/2562e112419226301effa78e77c55107b7a17bf4) | numeric extension move into core 1 |  
|   80	 | 2022-02-18 | [baf246a72](http://github.com/boostorg/gil/commit/baf246a72681a363fd423cdcccbd97cfc45f07b5) | numeric extension move into core part 2 |  
|   81	 | 2022-02-18 | [483915cb2](http://github.com/boostorg/gil/commit/483915cb20174b87109f1852c6072ea1f5ab4209) | numeric extension move into core part 3 |  
|   82	 | 2022-02-18 | [87a3157c4](http://github.com/boostorg/gil/commit/87a3157c4ba20b2c17d2829baa3304cf9ceb8189) | numeric extension move into core part 4 |  
|   83	 | 2022-02-18 | [9bd8642f6](http://github.com/boostorg/gil/commit/9bd8642f69bdbf5dfd01df45682efd50dd18fa53) | numeric extension move into core part 5 |  
|   84	 | 2022-02-22 | [f4c70a860](http://github.com/boostorg/gil/commit/f4c70a86065374eddc4455725c0013f22de4e70a) | Add support for <filesystem> to IO (#636) |  
|   85	 | 2022-03-01 | [da0655fb6](http://github.com/boostorg/gil/commit/da0655fb66dd161a643e1ca0ed51937548465d18) | Merge pull request #621 from sdebionne/fix-620 |  
|   86	 | 2022-03-28 | [4943e403b](http://github.com/boostorg/gil/commit/4943e403bcd3181209bd1b94e1a63fdd04e48027) | chore: Bump CMake version in CMakeLists.txt |  
|   87	 | 2022-04-06 | [63f5837bd](http://github.com/boostorg/gil/commit/63f5837bd35a3c78bc0952035f02126306f35390) | Removed deprecated.hpp (#627) |  
|   88	 | 2022-04-07 | [bb080b544](http://github.com/boostorg/gil/commit/bb080b5445ce35d6f2f34693aefc09338f814b43) | ci: Replace Ubuntu 16.04 with Ubuntu 18.04 in some GitHub Actions jobs (#640) |  
|   89	 | 2022-04-07 | [a38f890fe](http://github.com/boostorg/gil/commit/a38f890feec307d7251427db27aa442a111b6101) | ci: Fix Clang 9 build on GitHub Actions (#641) |  
|   90	 | 2022-04-07 | [c1c87ee03](http://github.com/boostorg/gil/commit/c1c87ee0301c82a0acc328c1e69c2d79280db75b) | ci: Switch Azure's macOS build to supported OS version (#643) |  
|   91	 | 2022-04-09 | [cf1e6b11b](http://github.com/boostorg/gil/commit/cf1e6b11ba73d50e60fd26b0d84ec95031ab3672) | build: Define _SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING for MSVC |  
|   92	 | 2022-04-09 | [09c3464ad](http://github.com/boostorg/gil/commit/09c3464adf86818903442df9427a268526d43ce5) | ci: Switch GitHub Action's MSVC build to supported OS version (#644) |  
|   93	 | 2022-04-12 | [8d83cdca2](http://github.com/boostorg/gil/commit/8d83cdca24b42e0c844e0ff503931abe5a9fe590) | ci: Remove /std:c++latest from MSVC build variants |  
|   94	 | 2022-04-12 | [22be33bb6](http://github.com/boostorg/gil/commit/22be33bb6198631266df214cf47dc85cae923a76) | ci: Remove cxxstd=2a from GitHub Actions ci.yml workflow |  
|   95	 | 2022-04-12 | [41167f600](http://github.com/boostorg/gil/commit/41167f6000c15d1aaf8e4d1a6f3f37d3689ff5eb) | ci: Add Boost.Align to transitive dependencies on AppVeyor |  
|   96	 | 2022-04-12 | [016ae7877](http://github.com/boostorg/gil/commit/016ae78778e24a90cf2da37fa4e81995527d38d3) | ci: Stop building on CircleCI |  
|   97	 | 2022-04-12 | [c0cb52d1f](http://github.com/boostorg/gil/commit/c0cb52d1f88fbe0138e585a3779a353563a1041c) | ci: Remove CircleCI traces from README.md |  
|   98	 | 2022-04-14 | [39089c5f8](http://github.com/boostorg/gil/commit/39089c5f8f44f2a5a64a3704b831c0ee5a2c7f85) | docs: Add Discussions to README.md |  
|   99	 | 2022-04-21 | [caf92fa94](http://github.com/boostorg/gil/commit/caf92fa94ba4b5960b7431dd9c7e391400aa293d) | Renamed adaptive_he to adaptive_histogram_equalization (#638) |  
|  100	 | 2022-04-26 | [4f83beb73](http://github.com/boostorg/gil/commit/4f83beb735463ed6ce68c67cb1607e82b23462cb) | style: Remove trailing space characters (#651) |  
|  101	 | 2022-04-26 | [9ed8fd2b4](http://github.com/boostorg/gil/commit/9ed8fd2b4ba2033cfe2a5dc5de61b90fddea00d0) | ci: remove Windows Server 2016 from builds on Azure Pipelines (#652) |  
|  102	 | 2022-04-26 | [791148660](http://github.com/boostorg/gil/commit/791148660e1c4244f7668ad376572eb8b7677d15) | fix: is_equal_to_sixteen in PNG I/O was less-than test (#650) |  
|  103	 | 2022-04-27 | [9d526ed70](http://github.com/boostorg/gil/commit/9d526ed70673bc69192b86cb4411219fcf569721) | refactor: Renamed io/dynamic_io_new.hpp to io/detail/dynamic.hpp (#653) |  
|  104	 | 2022-04-30 | [3289fe0bc](http://github.com/boostorg/gil/commit/3289fe0bc43ff1d82770ffcb3e9268b31cfaa0d7) | refactor: Unified operation names for pixel and channel operations (#655) |  
|  105	 | 2022-04-30 | [c063d1c18](http://github.com/boostorg/gil/commit/c063d1c185984c49b0c3f9f400905ef2d6ed2474) | feat: Added for_each_pixel overload for any_image (#648) |  
|  106	 | 2022-05-03 | [36a45e3af](http://github.com/boostorg/gil/commit/36a45e3af566950acc50b6ab052f3e7d7b1fc0b7) | fix documentation links in "See also" sections |  
  
  
### Merge  
  
```  
/d/boost/gil (master|u= origin/master) $ git merge --no-ff develop  
Auto-merging .github/workflows/ci.yml  
CONFLICT (add/add): Merge conflict in .github/workflows/ci.yml  
Auto-merging CMakeLists.txt  
Auto-merging CONTRIBUTING.md  
Auto-merging README.md  
CONFLICT (content): Merge conflict in README.md  
Auto-merging RELEASES.md  
CONFLICT (content): Merge conflict in RELEASES.md  
Auto-merging test/Jamfile  
CONFLICT (content): Merge conflict in test/Jamfile  
Auto-merging test/core/channel/algorithm_channel_relation.cpp  
CONFLICT (content): Merge conflict in test/core/channel/algorithm_channel_relation.cpp  
Automatic merge failed; fix conflicts and then commit the result.  
...  
Unmerged paths:  
  (use "git add <file>..." to mark resolution)  
        both added:      .github/workflows/ci.yml  
        both modified:   README.md  
        both modified:   RELEASES.md  
        both modified:   test/Jamfile  
        both modified:   test/core/channel/algorithm_channel_relation.cpp  
  
/d/boost/gil (master|MERGING|u= origin/master) $ git add -A  
/d/boost/gil (master|MERGING|u= origin/master) $ git merge --continue  
[master 6ef11459a] Merge branch 'develop'  
```  
  
### Build (local)  
  
On Windows, using VS 2022, `core` only:  
  
```console  
b2.exe cxxstd=11 variant=release address-model=64 libs/gil/test/core  
```  
  
On Linux, using gcc 10, all default tests  
  
```console  
./b2 cxxstd=11 variant=release address-model=64 libs/gil/test  
```  
  
All pass.

---

## Comment 1

> Username: mloskot  
> Created at: 2022-06-19 10:55:19 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1159688401  

I've just pushed another merge from develop to master in https://github.com/boostorg/gil/commit/b1d64abbba0f5e253fad612985c479a0a0cce21f

---

## Comment 2

> Username: mloskot  
> Created at: 2022-06-27 08:12:44 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1167028095  

I've just pushed `develop` to `master` merge in https://github.com/boostorg/gil/commit/b29f3da72cae2354a9249268899b81417474e5b0 before the [upcoming deadline on the 29th](https://lists.boost.org/Archives/boost/2022/06/253227.php).  
  
/cc @marco-langer

---

## Comment 3

> Username: marco-langer  
> Created at: 2022-06-28 10:11:54 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1168522202  

Changes on the documentation are still possible until 6th of July according to the Boost 1.80 release schedule. Does this also include PRs on the examples? (IMO: yes, examples are part of the documentation)

---

## Comment 4

> Username: mloskot  
> Created at: 2022-06-28 14:09:58 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1168777471  

Yes, of course. Documentation, examples, tests and stuff inside `namespace boost::gil::detail` can receive any changes - only major changes to public interface is not allowed.  
  
I've been rushing with merging PRs  but postponed updates to `RELEASES.md` notes.

---

## Comment 5

> Username: mloskot  
> Created at: 2022-06-29 21:06:39 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1170493458  

I've just pushed, I think, the **last major changes** `develop` to `master` merge in https://github.com/boostorg/gil/commit/bba6d62b4a50076418174469c3ad04d201873c3b before the [upcoming deadline on the 29th](https://lists.boost.org/Archives/boost/2022/06/253227.php).  
  
We are free to make changes to docs, examples, tests, and other non-breaking changes.

---

## Comment 6

> Username: mloskot  
> Created at: 2022-07-05 20:29:39 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1175469580  

Here is likely the final batch of changes to collect release notes for:  
  
```  
$ git log 36a45e3af..develop --reverse --pretty=format:" | %cs | [%h](http://github.com/boostorg/gil/commit/%H) | %s |" | nl | sed -E 's/^\s/\|/g'  
```  
| #   | Date | Hash | Subject |  
| --- | ---  | ---  | ---     |  
|    1   | 2022-05-12 | [9666a95bc](http://github.com/boostorg/gil/commit/9666a95bc5e0bf2a8780c307c945a8452473d3b3) | chore: Remove unused variable in diffusion.hpp (#666) |  
|    2   | 2022-05-13 | [f839504f2](http://github.com/boostorg/gil/commit/f839504f2081321509333c28eb673e437c5218a2) | Fixed io extension build error (#671) |  
|    3   | 2022-05-13 | [ec9f0b024](http://github.com/boostorg/gil/commit/ec9f0b0240ae0b264ac59cae112199b62948d3d3) | docs: Add partial release notes for Boost 1.80 (#670) |  
|    4   | 2022-05-14 | [fb8e389ad](http://github.com/boostorg/gil/commit/fb8e389ada1e7ef536f851fd69100a2be72625ee) | docs: Add remaining part of release notes for Boost 1.80 (#672) |  
|    5   | 2022-05-17 | [8caa148e0](http://github.com/boostorg/gil/commit/8caa148e0a1e2e6d0fdba18d7029e1b9ab6331ed) | docs: Added more release notes with GSoC work |  
|    6   | 2022-05-18 | [44d4ab8d4](http://github.com/boostorg/gil/commit/44d4ab8d4b9c35e2aa2d879492d83bc820311328) | ci: Disable g++-8 cxxstd=17 - segmentation fault for simple_all_formats test |  
|    7   | 2022-05-18 | [3090f866f](http://github.com/boostorg/gil/commit/3090f866f25ccc44772b55f4e3986ec0ab3617d5) | fix: Re-allow devicen_t with two color components (#654) |  
|    8   | 2022-05-18 | [1be8c87c1](http://github.com/boostorg/gil/commit/1be8c87c1047733aeeb3c29585ebafb057753356) | chore: do not use deprecated header in test or elsewhere (#675) |  
|    9   | 2022-05-18 | [d64e70dc8](http://github.com/boostorg/gil/commit/d64e70dc84f48c0f0db8ae815599d87f0d38d856) | docs: add release notes for issue #654 (#674) |  
|   10   | 2022-05-18 | [4ad824e8d](http://github.com/boostorg/gil/commit/4ad824e8dd379632809cf0b57478252b985fb5cf) | ci: Remove clang 3.5 through 3.8 jobs based on Ubuntu 16.04 |  
|   11   | 2022-05-24 | [dcae92f5c](http://github.com/boostorg/gil/commit/dcae92f5c2b6b9523544da5b672c7f6e40c15541) | docs!: announce switch from C++11 to C++14 with Boost 1.80 (#677) |  
|   12   | 2022-05-26 | [6d312af51](http://github.com/boostorg/gil/commit/6d312af51acfc922a61a635d3da90b9acd45e62b) | chore!: deprecate any_color_converted_view (#678) |  
|   13   | 2022-05-28 | [98f49711d](http://github.com/boostorg/gil/commit/98f49711dd8b4b5cff74d7feddbdaacb94943c54) | refactor: Make packed_pixel trivially copyable and assignable (#679) |  
|   14   | 2022-05-31 | [bab2a370f](http://github.com/boostorg/gil/commit/bab2a370ff09b4f526cd8e5084c5435208b8a799) | Fix Hough transform and move rasterization |  
|   15   | 2022-05-31 | [f87ee565c](http://github.com/boostorg/gil/commit/f87ee565c1c4157b880c729d04b1d21d1f6dcc2f) | In depth explanation for Hough line |  
|   16   | 2022-05-31 | [cc39f1a00](http://github.com/boostorg/gil/commit/cc39f1a007578c4bfda003641d53cf9c91f6e247) | Change "" includes into <> |  
|   17   | 2022-05-31 | [48d7ebffe](http://github.com/boostorg/gil/commit/48d7ebffe06bfc97466f6f09e3382cc91aa3a5ed) | Move diffusion and Hough transform |  
|   18   | 2022-05-31 | [b962a6a2d](http://github.com/boostorg/gil/commit/b962a6a2dba6a75cab9177fa7a36a66016c9b3cb) | Edit Jamfile to new layout |  
|   19   | 2022-05-31 | [76dcca89f](http://github.com/boostorg/gil/commit/76dcca89f4777f50089a1bb110f8f35cd3fd8318) | Add compile statements to Jamfile |  
|   20   | 2022-05-31 | [3965f2ab9](http://github.com/boostorg/gil/commit/3965f2ab99a27498cb94f761c186fd08cae780ae) | Remove migrated files |  
|   21   | 2022-05-31 | [45da54487](http://github.com/boostorg/gil/commit/45da544873eb32eff2ef4a2c72c641775f11fa43) | Removed redundant lines |  
|   22   | 2022-06-03 | [843ea374b](http://github.com/boostorg/gil/commit/843ea374b69dc90b60d289e308c4e35319e8be58) | Merge pull request #673 from simmplecoder/gsoc_2020_fixup |  
|   23   | 2022-06-05 | [2f7c3db5b](http://github.com/boostorg/gil/commit/2f7c3db5bd8df8fb3dc203c75f5148d7d93cdda0) | chore: update metadata for switch to C++ 14 (#682) |  
|   24   | 2022-06-07 | [9ecdb876b](http://github.com/boostorg/gil/commit/9ecdb876b33e06725b3b81e9db3ad0d9f60c58f7) | Fix deprecation warning bug for gcc 10.2 (#683) |  
|   25   | 2022-06-12 | [1c6c427f5](http://github.com/boostorg/gil/commit/1c6c427f5394b06296aec62442e361332a9b34d9) | fix: Added missing include guard to line rasterizer (#687) |  
|   26   | 2022-06-24 | [5273678bf](http://github.com/boostorg/gil/commit/5273678bfc61bf6fae0707f6c90001757bfba37d) | fix: Wrong RGB -> HSL convertion (#505) |  
|   27   | 2022-06-24 | [c436ea3e6](http://github.com/boostorg/gil/commit/c436ea3e6739964e906c3e794ffbaae48978ad38) | fix: Add missing #include <array> |  
|   28   | 2022-06-25 | [46731e6d4](http://github.com/boostorg/gil/commit/46731e6d449ff1f4acc9735384d9313203dd7f15) | chore: Correct include guard |  
|   29   | 2022-06-25 | [d50d85613](http://github.com/boostorg/gil/commit/d50d85613add1a52eed45ae51778d6649137865c) | refactor: Make with_tolerance reusable across other tests |  
|   30   | 2022-06-25 | [57c616d27](http://github.com/boostorg/gil/commit/57c616d2734b1455f9784a7b12a73412e24e2299) | refactor: Move RGB to HSL tests to color_convert_rgb.cpp |  
|   31   | 2022-06-25 | [0f435906e](http://github.com/boostorg/gil/commit/0f435906eade0e0d936bc07260da7fe8e278ff21) | test: Add tests for RGB to HSL (#691) |  
|   32   | 2022-06-25 | [526c89833](http://github.com/boostorg/gil/commit/526c8983364a68cb5222fc54c193b1cbbe541a12) | test: Add tiled TIFF test case to simple_all_formats |  
|   33   | 2022-06-25 | [b9e652dce](http://github.com/boostorg/gil/commit/b9e652dce823e5a117e8e49a884ab71e22570e77) | fix: Automatic detection of <filesystem> header (#684) |  
|   34   | 2022-06-26 | [151fd9c8a](http://github.com/boostorg/gil/commit/151fd9c8a318a4249e244eab959592143422a04d) | refactor: Replace deprecated libtiff v4.3 typedefs with C99 fixed-size integers (#685) |  
|   35   | 2022-06-26 | [bfed3de00](http://github.com/boostorg/gil/commit/bfed3de0049ed1f0de2f9045ee5b1409666330dc) | refactor: Deprecate apply_operation in favor of variant2::visit for any_image (#656) |  
|   36   | 2022-06-26 | [adddbec89](http://github.com/boostorg/gil/commit/adddbec8961a152db5751802307cc4ee977fd15d) | refactor: Ellipse rasterizer according to the comment at (#692) |  
|   37   | 2022-06-27 | [d5492e1ac](http://github.com/boostorg/gil/commit/d5492e1ace19e7716e525323f8e18d14a0d9fb9a) | feat: Added apply_rasterizer() free function (#695) |  
|   38   | 2022-06-27 | [95679b628](http://github.com/boostorg/gil/commit/95679b6280b4cc8a20c208fb0008c4579a1a8070) | docs!: Announce plan to require C++17 after Boost 1.80 (#694) |  
|   39   | 2022-06-27 | [103f4d31b](http://github.com/boostorg/gil/commit/103f4d31ba76ac1817867364e7e8532d73e949db) | build: Update CMAKE_CXX_STANDARD from 11 to 14 |  
|   40   | 2022-06-27 | [156dd29f2](http://github.com/boostorg/gil/commit/156dd29f282aad687d958f927f2015f2e1b8b0e8) | build: Bump Boost required by CMake from 1.72 to 1.80 |  
|   41   | 2022-06-27 | [fe63aa2a1](http://github.com/boostorg/gil/commit/fe63aa2a108e266d197de4e78f12535a8af6ce49) | refactor: Switch to trailing return types (#599) |  
|   42   | 2022-06-27 | [1049c0719](http://github.com/boostorg/gil/commit/1049c07192e1cbe5377f23f31cf15f28c20863dc) | build: Fix CMake source file extensions must be explicit |  
|   43   | 2022-06-27 | [27826a7d5](http://github.com/boostorg/gil/commit/27826a7d554d6b9b555e099d722c4d7981f8ebe0) | ci: Remove C++11 build jobs after C++14 switch (#698) |  
|   44   | 2022-06-27 | [2faf52f23](http://github.com/boostorg/gil/commit/2faf52f236f7a7d90ad148e1b4ffa33d7e8a5dd6) | Merge pull request #699 from mloskot/ml/cmake-bump-cxxstd-11-to-14 |  
|   45   | 2022-06-27 | [5c3cfc7f7](http://github.com/boostorg/gil/commit/5c3cfc7f7b424d715dd8998ab576d479561dd00a) | docs: Bump C++11 to C++14 as current required (#700) |  
|   46   | 2022-06-27 | [8d7034c71](http://github.com/boostorg/gil/commit/8d7034c71cdb3ea1e946f57ca3b30e81b8a8b854) | fix: Memory leak in image class for empty dimensions (#649) |  
|   47   | 2022-06-28 | [a0ac9fb4a](http://github.com/boostorg/gil/commit/a0ac9fb4aa04250698cda957ea8f34e0efb0ff73) | test: Case test_constructor_from_view was not called |  
|   48   | 2022-06-28 | [ef9b89a93](http://github.com/boostorg/gil/commit/ef9b89a93a526fc091479b6ece107c89e7557809) | test: Add test cases for image with empty dimensions (#702) |  
|   49   | 2022-06-28 | [4dbf35a51](http://github.com/boostorg/gil/commit/4dbf35a510fd2bad2ed8c59c4dd523de4b3cf77b) | Add pmr image typedefs (#529) |  
|   50   | 2022-06-29 | [8101595ce](http://github.com/boostorg/gil/commit/8101595cebd5c1c7805600dab021caef471724e9) | chore: Update CMake to use latest cmake-conan/0.18.1 [ci skip] |  
|   51   | 2022-06-29 | [00423cca0](http://github.com/boostorg/gil/commit/00423cca03e0641ba3a82f6b85eacac566ecf049) | chore: Update CMakeSettings.json sample [ci skip] |  
|   52   | 2022-06-29 | [08e3e6dad](http://github.com/boostorg/gil/commit/08e3e6dadb4e344757802b668def8f1bab7952e2) | test: Add basic is_1d_traversable cases for image_view |  
|   53   | 2022-06-29 | [7b7c786c1](http://github.com/boostorg/gil/commit/7b7c786c1b0d253dfbdd460695323311b19e50df) | test: Check more properties of indexed_image_view from extension/toolbox |  
|   54   | 2022-06-29 | [86ee473f5](http://github.com/boostorg/gil/commit/86ee473f51cfc6b2f9ee205bc88b09673fea34b0) | test: Add virtual_2d_locator fixture; is_2d_traversable test case |  
|   55   | 2022-06-29 | [2409be4bd](http://github.com/boostorg/gil/commit/2409be4bd6ff4c36ea990ebdaf5a7066eb65a038) | test: Add more basic cases for image class (#423) |  
|   56   | 2022-07-02 | [233dc53ff](http://github.com/boostorg/gil/commit/233dc53ff7888647f0ae39af70aac39e575d1e9b) | fix: Unused variable warning (#704) |  
|   57   | 2022-07-02 | [fdefeab38](http://github.com/boostorg/gil/commit/fdefeab388f43c54c911e90a01988afb459df066) | docs: fixed gradient tutorial (#703) |  
|   58   | 2022-07-04 | [573ba132c](http://github.com/boostorg/gil/commit/573ba132cd182f0bde819d1f1abcd13adcf00623) | fix: broken build for midpoint_ellipse_rasterizer::draw_curve (#705) |  
|   59   | 2022-07-05 | [eb161402e](http://github.com/boostorg/gil/commit/eb161402e7608a9d6a6266f9ba08141cc42d5570) | docs: Updated readme language badge to C++14 (#707) [ci skip] |

---

## Comment 7

> Username: mloskot  
> Created at: 2022-07-05 21:24:22 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1175512377  

I think we are good now and on time before the Boost super-project `master` [closes for all changes on the 6th of June](https://lists.boost.org/Archives/boost/2022/06/253251.php).

---

## Comment 8

> Username: striezel  
> Created at: 2022-07-19 21:55:38 UTC  
> Updated at: 2022-08-11 21:44:46 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1189589161  

>     6. [ ]  Copy release notes to https://github.com/boostorg/website/  
  
Now might be a good time to do that. The Boost 1.80.0 beta 1 has been released last week and the [release notes are up on the website](https://www.boost.org/users/history/version_1_80_0.html), but they show no changes for GIL - so far. Considering that GIL in Boost 1.80.0 brings a lot of changes - among them a breaking by switching to C++14 - those changes should be made visible on the website.  
  
As far as I understand somebody has to create a PR on the website repository to update those notes, so I took the liberty to do that: https://github.com/boostorg/website/pull/696 Somebody (@mloskot?) should still check the syntax, though, because that's the first time I have written something in QuickBook format.

---

## Comment 9

> Username: mloskot  
> Created at: 2022-07-20 08:16:10 UTC  
> Updated at: 2022-07-20 09:43:28 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1189970173  

@striezel Yes, I meant to do it sooner but I've got swamped with other stuff. So, thank you for the golden helping hand with the PR. I will check it.  
  
UPDATE: Done, https://github.com/boostorg/website/pull/696 merged.

---

## Comment 10

> Username: mloskot  
> Created at: 2022-08-11 19:59:04 UTC  
> Url: https://github.com/boostorg/gil/issues/667#issuecomment-1212429630  

We're done here. Congratulations and thanks to every one involved!
