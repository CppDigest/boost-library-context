# #538 - vcpkg  boost-build:x64-windows [Closed]

> Username: taoyeh  
> Created at: 2021-07-20 00:38:26 UTC  
> Updated at: 2021-07-20 01:07:14 UTC  
> Closed at: 2021-07-20 01:07:14 UTC  
> Url: https://github.com/boostorg/boost/issues/538  

Error: Building package boost-build:x64-windows failed with: BUILD_FAILED  
Please ensure you're using the latest portfiles with `.\vcpkg update`, then  
submit an issue at https://github.com/Microsoft/vcpkg/issues including:  
  Package: boost-build:x64-windows  
  Vcpkg version: 2021-07-16-3be6b75f6972aa72ca34afce39168094432ea300  
  
Additionally, attach any relevant sections from the log files above.  
  
  
how I sovle this problem

---

## Comment 1

> Username: taoyeh  
> Created at: 2021-07-20 00:53:29 UTC  
> Url: https://github.com/boostorg/boost/issues/538#issuecomment-882961791  

D:\vcpkg-master>vcpkg install pcl:x64-windows  
Computing installation plan...  
The following packages will be built and installed:  
  * boost-algorithm[core]:x64-windows -> 1.76.0  
  * boost-align[core]:x64-windows -> 1.76.0  
  * boost-any[core]:x64-windows -> 1.76.0  
  * boost-asio[core]:x64-windows -> 1.76.0  
  * boost-atomic[core]:x64-windows -> 1.76.0  
  * boost-bimap[core]:x64-windows -> 1.76.0  
  * boost-build[core]:x64-windows -> 1.75.0  
  * boost-chrono[core]:x64-windows -> 1.76.0  
  * boost-container[core]:x64-windows -> 1.76.0  
  * boost-context[core]:x64-windows -> 1.76.0  
  * boost-coroutine[core]:x64-windows -> 1.76.0  
  * boost-date-time[core]:x64-windows -> 1.76.0  
  * boost-dynamic-bitset[core]:x64-windows -> 1.76.0  
  * boost-endian[core]:x64-windows -> 1.76.0  
  * boost-exception[core]:x64-windows -> 1.76.0  
  * boost-filesystem[core]:x64-windows -> 1.76.0  
  * boost-foreach[core]:x64-windows -> 1.76.0  
  * boost-format[core]:x64-windows -> 1.76.0  
  * boost-function[core]:x64-windows -> 1.76.0  
  * boost-functional[core]:x64-windows -> 1.76.0  
  * boost-graph[core]:x64-windows -> 1.76.0  
  * boost-interprocess[core]:x64-windows -> 1.76.0  
  * boost-intrusive[core]:x64-windows -> 1.76.0  
  * boost-iostreams[core]:x64-windows -> 1.76.0  
  * boost-lambda[core]:x64-windows -> 1.76.0  
  * boost-lexical-cast[core]:x64-windows -> 1.76.0  
  * boost-math[core]:x64-windows -> 1.76.0  
  * boost-modular-build-helper[core]:x64-windows -> 1.76.0  
  * boost-mp11[core]:x64-windows -> 1.76.0  
  * boost-multi-array[core]:x64-windows -> 1.76.0  
  * boost-multi-index[core]:x64-windows -> 1.76.0  
  * boost-parameter[core]:x64-windows -> 1.76.0  
  * boost-phoenix[core]:x64-windows -> 1.76.0  
  * boost-pool[core]:x64-windows -> 1.76.0  
  * boost-property-map[core]:x64-windows -> 1.76.0  
  * boost-property-tree[core]:x64-windows -> 1.76.0  
  * boost-proto[core]:x64-windows -> 1.76.0  
  * boost-ptr-container[core]:x64-windows -> 1.76.0  
  * boost-random[core]:x64-windows -> 1.76.0  
  * boost-range[core]:x64-windows -> 1.76.0  
  * boost-ratio[core]:x64-windows -> 1.76.0  
  * boost-rational[core]:x64-windows -> 1.76.0  
  * boost-regex[core]:x64-windows -> 1.76.0  
  * boost-serialization[core]:x64-windows -> 1.76.0  
  * boost-signals2[core]:x64-windows -> 1.76.0  
  * boost-sort[core]:x64-windows -> 1.76.0  
  * boost-spirit[core]:x64-windows -> 1.76.0  
  * boost-system[core]:x64-windows -> 1.76.0  
  * boost-test[core]:x64-windows -> 1.76.0  
  * boost-thread[core]:x64-windows -> 1.76.0  
  * boost-tokenizer[core]:x64-windows -> 1.76.0  
  * boost-tti[core]:x64-windows -> 1.76.0  
  * boost-type-index[core]:x64-windows -> 1.76.0  
  * boost-unordered[core]:x64-windows -> 1.76.0  
  * boost-uuid[core]:x64-windows -> 1.76.0  
  * boost-variant[core]:x64-windows -> 1.76.0  
  * boost-xpressive[core]:x64-windows -> 1.76.0  
  * bzip2[core]:x64-windows -> 1.0.8#2  
  * eigen3[core]:x64-windows -> 3.3.9  
  * flann[core]:x64-windows -> 2019-04-07#2  
  * liblzma[core]:x64-windows -> 5.2.5#2  
  * libpng[core]:x64-windows -> 1.6.37#15  
  * lz4[core]:x64-windows -> 1.9.3  
  * openssl[core]:x64-windows -> 1.1.1k#7  
    pcl[core]:x64-windows -> 1.11.1#4  
  * qhull[core]:x64-windows -> 8.0.2  
  * vcpkg-cmake[core]:x64-windows -> 2021-06-25#4  
  * vcpkg-cmake-config[core]:x64-windows -> 2021-05-22#1  
  * xxhash[core]:x64-windows -> 0.8.0#1  
  * zlib[core]:x64-windows -> 1.2.11#10  
  * zstd[core]:x64-windows -> 1.4.9  
Additional packages (*) will be modified to complete this operation.  
Detecting compiler hash for triplet x64-windows...  
-- Automatically setting HTTP(S)_PROXY environment variables to 127.0.0.1:7890  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\70\70497ad5ef4dfa41998278b53ba5f4f6060e6a2203c85dff2823c1b493fef67a.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\ea\eae6f945817e84cc9b3a272c59202c9dad7916a10c4e1cc47124825a10f2fe42.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\c2\c2a72a0ed1dcf68db1b8b847f0abb5d2470e0c584373ff429e1166b17fe39fe3.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\4e\4eaf6114c3b031fbcb953854e772cf2d09d5fcd536c121eb6b32b906a76c8fd6.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\93\93c74f09e4c3b031605920cd2959a47290a6c04c4811f54f3cf68b9fe71c0704.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\54\543f9880c63ad39f92547138e5689db4e1d4c086e99bf86a8af15260671b5f07.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\26\264f4037d48b81d0ac2b12a3a4652976bcb5d2bef7d31e3d4db4770f9db0447f.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\be\befa0403c756343c922717a85c48dd9858eb7b38e673355311f6fb8724031914.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\c5\c54281bd569b6db5884fdddcc5b4ec312b5bfc8bffc63cff1acf9edbf169f20a.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\50\50eeed1d9c0dbdd025d78dca252ed780be05306d2e6ace6f88c4eed2bcad8bd2.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\98\98c4d769f29a8e0e4b6236a7d41b1637d7a5d1073f5150abf787ae3531a075bf.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\cd\cd4a8261673a60786d3e6be4bafc780c24e74bb429eefed36460bae745170601.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\17\17e61ff7d07c579a043fef9d1c783642e0e4397129467b8391cdda5ad5e3e565.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\0b\0bfb0818a2ddbabdcc22c7f8dd06d230068439f8aa815cbd67206dd8ec574b66.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\da\daf4f95d024a10befe43ed0fa1632795159e15e2b7b86d4a2a9af960618649fe.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\56\5667e7c0c190d824f593042639035edeedf6274c10d1d9862df6cbd2d7ab1744.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\94\947ce837484e5c4f87c568bd5b381bcb7f437bfc7caaa2a412f3762dd63671cd.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\7a\7a94ba2a497fb6631004822f164b2d0c9c4298b9207ee80abb9e2e552c49d154.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\f9\f9554a403c69fd7d667ff808f467d4a4f18d8af28266a829fa4fc932c5c21c6b.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\44\4473dfe18274a703eba4f1e02eab6091159207eec60a1aed6ff7755427fa0e21.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\53\53b5cdd835cccd9bbd959dfc1ec4d47bfec03d95b3bbbe06d895c52b1b590d0e.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\ec\eca45a3309009fc037d38f8a205cefb46b184b45bb5d512aa0b1b5f06f243be1.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\4d\4d908ebadceb272d33e7425c7bab264351059e5d9f8e3c7a5ad51da4e076d0d8.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\db\db6f82d1bdf56961f6da3c4d579a1f9cb787f2e9fadfb1dc3679ef91952aebd8.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\3f\3fcabb703e8f6506dd0f63804518117f3ad1d0f6a4f533bf5b970234ebaee654.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\1f\1f629d1f4ab217f469b92f8591d40eeb3d47e8ede8cb9cbade57b560d553cda8.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\8d\8de601f7a95005248c7f647f7a65efffb9561d234cc059f23cde7195b7e517b5.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\8e\8e227ed496c5faa962e14f4667ccc7ba4ed7b07e94dc936a218c0b0e36649b10.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\9a\9a420a1b723794c38dda4f96a4bc677279ee604516bc374b62f8102c2746d021.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\48\48a5348dd07cf78fd8fdc68b36c5e6751ee4e367e29afe6411eb14c3705460e8.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\24\24c48f14c11eb9186232b74a8a81f87acbe487f1b34142f80bebfd75337e1d57.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\c9\c937a044242187b28701936b9a263ddba4c5611c26e71dd7a265f51dc0496cd3.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\9b\9b454e354be87716b79991ca2088cff72243e5bb8096010202a1488407bb61ed.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\f3\f32690d05a790abf6df5a44fa84c3d50d17b272e7f1650477c1a11359ccf1587.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\2d\2dcfef9ed533715629865e6c7b81af651d2eaa8cd95c13a31ef02b2860c47f9d.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\5f\5f08607b31368a164b52159dd9447dd0baf4eeb3a7078068df5a715ba978273f.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\65\65e750a50cb6a8644036445d2ee4322d43b17e80e9f84b26c52bb90f71f9cd03.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\99\9966a30de7f19558a032d24c53e59a732ce82903c9760c57bc4738b72542bf00.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\cb\cb97e88a148015bd3e4cb43ad93bee02c24ca1c1107b2ebcb5283608bed15328.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\e7\e73befa5deaac13cbde1b3926621a536f4f362fd1e7b4e6964f7ef86ee20b36d.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\5d\5dba0056cf199e40c61aed5b42316df36660de998d669627e9d974fc40ca614d.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\15\1593a5ae3be8b1d2a391399e23a1f1f347053e0d716dced54ca98ad8ce791a31.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\d5\d5d7945bfc084efa885eca4f48e39dbad99e1ecd0a22ff0e2e69ed22f27053b6.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\69\69813a11b1db2203d7de2d6ec8661c6a0c6d014f1db11ae367ed39b09ab79d97.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\0d\0dce39ed4990a976f2a3b3f4b6e4550640c62cbd69c32b23a9a1c229b255a767.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\09\095e4123d91160ae8954e2ebcfb58943376eb5b434f6bb0f895c541d8bd4d21b.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\07\07d67d1e3be2b2ba9973b27d350c074d5e2012ffeb2f7ce445c5796d56830659.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\57\572596dfe73cae23956703dcb61ada399a793f97deaa16de2b0147cdf7dcaafb.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\69\69043660ea00dc521d45d9fc25ac54de18664fa34ccc4e2044c355e8a656d1ef.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\dd\dd218d8f280897be731d0b50a889f95c88f4cc75def9bea95d317d086cbcff28.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\92\92fe78c3fd39daec2c6e6b6b6bbc4fb56cfb76348d4187799b3e10fc913fe676.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\2b\2b8aac4bcd2928717e6a8e40c0fbfe84d2272baacec56b84ae69eb527f04b3a0.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\ca\caf7ebc1f8530a36f69b676e3428d741a0cc7050c59e47699cb1e70e87549168.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\e6\e69cfa12fc310fe6c04bd36bd566c48daeb7905f5b5d2bf36944b1058b3aef43.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\9e\9e1e3abec4e1ecad27f1355daf2964b5f9492d17bbc471ed5f26420c83a5301b.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\96\96f655e2c5b8277d0a3aa418cee292e416b6253d726152f366833496e2fb0757.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\b0\b0d231ca690b4262759e4d2dabf1bbbe5f3d77fdcecfd7a849f055428ca1255d.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\a4\a43305b41b3a8c3e7f8b84c70c6a58a6058800ea4d00f7f571ad67026514ef72.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\ea\ea5d228274a92c11d3dfe91296acbb0e0919b4cfb165c37f186784b2f7618ea7.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\9e\9ed38f4182b6378a6334a3d842b11be5fd4b2e8687f48d711492bcaf257aa001.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\39\397efeb0939c8e96122d70aacee102fd23d47f64a84c97176ae20df95ea0ce98.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\49\49b0f1dfca11934bdc32692c57e415d40780eb1942386d98b15cfcb099823934.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\1a\1a6713541fb796a9799d721506bc00151cfcbcbbd9eabe42a3d874d9eb9321ad.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\97\9765da2bb0f20424a12c58547911839b432e2010f332c5b8103f9a3db5659268.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\a8\a8130491aa1a1bba3e42d22ba67251529c31c92efb63e8d1e5a07fb27d728fb2.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\8f\8f09e79580876ca5338966f67c0df8d62f5b48c99512cfcabaac1172b0ee361c.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\85\85be8a1f98e2ee8d3d1d54365c886875434d841349aadb28e241e3aea16d1bfd.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\73\73ee3391fa2d1e1e7652ba704df168b0c1d053554219964e86fa65d7d2137cad.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\34\34b87d0b321ed0eb09afb1fe145b1040b4771130b77d6aa6d7ca8b45e91f2692.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\d9\d98fcfabffd3095e5bf7c04ba3c118294e49629988743e9df189cfef72adeb13.zip  
Could not locate cached archive: C:\Users\xxh\AppData\Local\vcpkg\archives\b0\b0df38773118c76632ad7c5223773125da4852b43d19c8be2bec8ead59ba53b1.zip  
Starting package 1/71: boost-build:x64-windows  
Building package boost-build[core]:x64-windows...  
-- Using D:/vcpkg-master/downloads/boostorg-build-boost-1.75.0.tar.gz  
-- Cleaning sources at D:/vcpkg-master/buildtrees/boost-build/src/ost-1.75.0-a06f9834b9.clean. Use --editable to skip cleaning for the packages you specify.  
-- Extracting source D:/vcpkg-master/downloads/boostorg-build-boost-1.75.0.tar.gz  
-- Applying patch fix_options.patch  
-- Using source at D:/vcpkg-master/buildtrees/boost-build/src/ost-1.75.0-a06f9834b9.clean  
-- Using D:/vcpkg-master/downloads/boost_LICENSE_1_0.txt  
-- Using D:/vcpkg-master/downloads/boost-1.75.0-boostcpp.jam  
-- Installing: D:/vcpkg-master/packages/boost-build_x64-windows/share/boost-build/copyright  
-- Bootstrapping...  
CMake Error at scripts/cmake/vcpkg_execute_required_process.cmake:127 (message):  
    Command failed: D:/vcpkg-master/packages/boost-build_x64-windows/tools/boost-build/bootstrap.bat msvc  
    Working Directory: D:/vcpkg-master/packages/boost-build_x64-windows/tools/boost-build  
    Error code: 1  
    See logs for more information:  
      D:\vcpkg-master\buildtrees\boost-build\bootstrap-x64-windows-out.log  
      D:\vcpkg-master\buildtrees\boost-build\bootstrap-x64-windows-err.log  
  
Call Stack (most recent call first):  
  ports/boost-build/portfile.cmake:63 (vcpkg_execute_required_process)  
  scripts/ports.cmake:140 (include)  
  
  
Error: Building package boost-build:x64-windows failed with: BUILD_FAILED  
Please ensure you're using the latest portfiles with `.\vcpkg update`, then  
submit an issue at https://github.com/Microsoft/vcpkg/issues including:  
  Package: boost-build:x64-windows  
  Vcpkg version: 2021-07-16-3be6b75f6972aa72ca34afce39168094432ea300  
  
Additionally, attach any relevant sections from the log files above.

---

## Comment 2

> Username: mclow  
> Created at: 2021-07-20 01:00:59 UTC  
> Url: https://github.com/boostorg/boost/issues/538#issuecomment-882964200  

I'm a bit confused. The error message you got recommends submitting an issue at https://github.com/Microsoft/vcpkg/issues, but you submitted one here (https://github.com/boostorg/boost/issues) instead. Why?
