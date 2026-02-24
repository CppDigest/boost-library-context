# #266 Move Boost dependencies to library instead of project requirements [Merged]

> Username: Flamefire  
> Created at: 2025-10-20 15:22:11 UTC  
> Updated at: 2025-10-21 07:59:13 UTC  
> Merged at: 2025-10-21 07:59:09 UTC  
> Closed at: 2025-10-21 07:59:09 UTC  
> Url: https://github.com/boostorg/locale/pull/266  

Otherwise all test builds and imported libs will link to those too. This causes e.g. Boost.Thread to be build and installed as a shared library due to e.g. `<library>icuuc/<link>shared`

---

## Comment 1

> Username: uilianries  
> Created_at: 2025-10-21 06:16:46 UTC  
> Url: https://github.com/boostorg/locale/pull/266#issuecomment-3424875222  

Hello!  
  
Just confirming this PR fixes the PR https://github.com/boostorg/boost/issues/1051  
  
Tested locally using the following script:  
  
```bash  
#!/usr/bin/env bash  
  
set -ex  
  
TEMP_DIR=$(mktemp -d)  
BOOST_MINOR_VERSION="88"  
INSTALL_DIR=$(mktemp -d)  
  
pushd $TEMP_DIR  
wget "https://archives.boost.io/release/1.${BOOST_MINOR_VERSION}.0/source/boost_1_${BOOST_MINOR_VERSION}_0.tar.bz2"  
tar jxf boost_1_${BOOST_MINOR_VERSION}_0.tar.bz2  
cd boost_1_${BOOST_MINOR_VERSION}_0/  
  
wget "https://patch-diff.githubusercontent.com/raw/boostorg/locale/pull/266.patch"  
sed -i -e 's/a\/build/a\/libs\/locale\/build/g' 266.patch  
sed -i -e 's/b\/build/b\/libs\/locale\/build/g' 266.patch  
git apply 266.patch  
  
./bootstrap.sh  
./b2 link=static install --prefix="${INSTALL_DIR}"  
popd  
  
ls "${INSTALL_DIR}/lib"  
  
find "${INSTALL_DIR}/lib" -type f -name "*.so*" -exec false {} +  
  
echo "Validation completed successfully. No .so files found in ${INSTALL_DIR}/lib"  
exit 0  
```  
  
The full build log can be found here: [boost-fix-locale-pr266.log](https://github.com/user-attachments/files/23014455/boost-fix-locale-pr266.log)  
  
Regards!

---
