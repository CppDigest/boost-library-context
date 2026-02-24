# #22 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:29:14 UTC  
> Updated at: 2020-02-18 03:02:11 UTC  
> Closed at: 2020-02-18 03:02:11 UTC  
> Url: https://github.com/boostorg/qvm/issues/22  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost_1_72_0/boost/qvm/all.hpp:#ifndef UUID_BE94EA1A31B211E0BBF943CFDFD72085  
./boost_1_72_0/boost/qvm/all.hpp:#define UUID_BE94EA1A31B211E0BBF943CFDFD72085  
./boost_1_72_0/boost/qvm/deduce_mat.hpp:#ifndef UUID_C5DC682E196211E0A4C1686BDFD72085  
./boost_1_72_0/boost/qvm/deduce_mat.hpp:#define UUID_C5DC682E196211E0A4C1686BDFD72085  
./boost_1_72_0/boost/qvm/deduce_quat.hpp:#ifndef UUID_435FA7E8196311E0A176DE6BDFD72085  
./boost_1_72_0/boost/qvm/deduce_quat.hpp:#define UUID_435FA7E8196311E0A176DE6BDFD72085  
./boost_1_72_0/boost/qvm/deduce_scalar.hpp:#ifndef UUID_F20566FC196311E0B99D606CDFD72085  
./boost_1_72_0/boost/qvm/deduce_scalar.hpp:#define UUID_F20566FC196311E0B99D606CDFD72085  
./boost_1_72_0/boost/qvm/deduce_vec.hpp:#ifndef UUID_7E7AB138196311E0907B246CDFD72085  
./boost_1_72_0/boost/qvm/deduce_vec.hpp:#define UUID_7E7AB138196311E0907B246CDFD72085  
./boost_1_72_0/boost/qvm/detail/cofactor_impl.hpp:#ifndef UUID_995547FAAE0E11DE8CF511E755D89593  
./boost_1_72_0/boost/qvm/detail/cofactor_impl.hpp:#define UUID_995547FAAE0E11DE8CF511E755D89593  
./boost_1_72_0/boost/qvm/detail/determinant_impl.hpp:#ifndef UUID_3DCF6B90AE0E11DE9A315BE555D89593  
./boost_1_72_0/boost/qvm/detail/determinant_impl.hpp:#define UUID_3DCF6B90AE0E11DE9A315BE555D89593  
./boost_1_72_0/boost/qvm/detail/mat_assign.hpp:#ifndef UUID_47136D2C385411E7BA27D3B681262D2E  
./boost_1_72_0/boost/qvm/detail/mat_assign.hpp:#define UUID_47136D2C385411E7BA27D3B681262D2E  
./boost_1_72_0/boost/qvm/detail/quat_assign.hpp:#ifndef UUID_86A42DA4385511E7822024B881262D2E  
./boost_1_72_0/boost/qvm/detail/quat_assign.hpp:#define UUID_86A42DA4385511E7822024B881262D2E  
./boost_1_72_0/boost/qvm/detail/remove_const.hpp:#ifndef UUID_4E340430AE4C11DEBA56149755D89593  
./boost_1_72_0/boost/qvm/detail/remove_const.hpp:#define UUID_4E340430AE4C11DEBA56149755D89593  
./boost_1_72_0/boost/qvm/detail/swizzle_traits.hpp:#ifndef UUID_E831FAD6B38F11DE8CECBF0D56D89593  
./boost_1_72_0/boost/qvm/detail/swizzle_traits.hpp:#define UUID_E831FAD6B38F11DE8CECBF0D56D89593  
./boost_1_72_0/boost/qvm/detail/transp_impl.hpp:#ifndef UUID_B3B8081A277711E09E007F2DDFD72085  
./boost_1_72_0/boost/qvm/detail/transp_impl.hpp:#define UUID_B3B8081A277711E09E007F2DDFD72085  
./boost_1_72_0/boost/qvm/detail/vec_assign.hpp:#ifndef UUID_185557CE385511E780ACD7B781262D2E  
./boost_1_72_0/boost/qvm/detail/vec_assign.hpp:#define UUID_185557CE385511E780ACD7B781262D2E  
./boost_1_72_0/boost/qvm/enable_if.hpp:#ifndef UUID_A4FA0794391911DF85A6622B56D89593  
./boost_1_72_0/boost/qvm/enable_if.hpp:#define UUID_A4FA0794391911DF85A6622B56D89593  
./boost_1_72_0/boost/qvm/error.hpp:#ifndef UUID_92B1247AAE1111DE9655F2FC55D89593  
./boost_1_72_0/boost/qvm/error.hpp:#define UUID_92B1247AAE1111DE9655F2FC55D89593  
./boost_1_72_0/boost/qvm/map.hpp:#ifndef UUID_74318C76B4ED11E58A3AE198BB8E7F8B  
./boost_1_72_0/boost/qvm/map.hpp:#define UUID_74318C76B4ED11E58A3AE198BB8E7F8B  
./boost_1_72_0/boost/qvm/map_mat_mat.hpp:#ifndef UUID_20D98340A3EB11DEB2180CD156D89593  
./boost_1_72_0/boost/qvm/map_mat_mat.hpp:#define UUID_20D98340A3EB11DEB2180CD156D89593  
./boost_1_72_0/boost/qvm/map_mat_vec.hpp:#ifndef UUID_5265FC7CA1C011DE9EBDFFA956D89593  
./boost_1_72_0/boost/qvm/map_mat_vec.hpp:#define UUID_5265FC7CA1C011DE9EBDFFA956D89593  
./boost_1_72_0/boost/qvm/map_vec_mat.hpp:#ifndef UUID_3EDF999CA1C011DEBA5C8DA956D89593  
./boost_1_72_0/boost/qvm/map_vec_mat.hpp:#define UUID_3EDF999CA1C011DEBA5C8DA956D89593  
./boost_1_72_0/boost/qvm/mat.hpp:#ifndef UUID_67E67D68A32F11DEA56FD18556D89593  
./boost_1_72_0/boost/qvm/mat.hpp:#define UUID_67E67D68A32F11DEA56FD18556D89593  
./boost_1_72_0/boost/qvm/math.hpp:#ifndef UUID_5FD6A664ACC811DEAAFF8A8055D89593  
./boost_1_72_0/boost/qvm/math.hpp:#define UUID_5FD6A664ACC811DEAAFF8A8055D89593  
./boost_1_72_0/boost/qvm/mat_access.hpp:#ifndef UUID_47B1D1217B411E08424FA0ADFD72085  
./boost_1_72_0/boost/qvm/mat_access.hpp:#define UUID_47B1D1217B411E08424FA0ADFD72085  
./boost_1_72_0/boost/qvm/mat_operations.hpp:#ifndef UUID_4F915D9ED30A11DF962186E3DFD72085  
./boost_1_72_0/boost/qvm/mat_operations.hpp:#define UUID_4F915D9ED30A11DF962186E3DFD72085  
./boost_1_72_0/boost/qvm/mat_traits.hpp:#ifndef UUID_8C06FE26A3E711DEA02C88BA55D89593  
./boost_1_72_0/boost/qvm/mat_traits.hpp:#define UUID_8C06FE26A3E711DEA02C88BA55D89593  
./boost_1_72_0/boost/qvm/mat_traits_array.hpp:#ifndef UUID_FA3ED0DCA17911DEA6BBA09955D89593  
./boost_1_72_0/boost/qvm/mat_traits_array.hpp:#define UUID_FA3ED0DCA17911DEA6BBA09955D89593  
./boost_1_72_0/boost/qvm/mat_traits_defaults.hpp:#ifndef UUID_FB4D5BEAC71B11E68D0EEF1707624D53  
./boost_1_72_0/boost/qvm/mat_traits_defaults.hpp:#define UUID_FB4D5BEAC71B11E68D0EEF1707624D53  
./boost_1_72_0/boost/qvm/operations.hpp:#ifndef UUID_0B9D9FB6B95D11DEA8E0305E55D89593  
./boost_1_72_0/boost/qvm/operations.hpp:#define UUID_0B9D9FB6B95D11DEA8E0305E55D89593  
./boost_1_72_0/boost/qvm/quat.hpp:#ifndef UUID_49C5A1042AEF11DF9603880056D89593  
./boost_1_72_0/boost/qvm/quat.hpp:#define UUID_49C5A1042AEF11DF9603880056D89593  
./boost_1_72_0/boost/qvm/quat_access.hpp:#ifndef UUID_8AC84A2217C411E0A7AF3A1BDFD72085  
./boost_1_72_0/boost/qvm/quat_access.hpp:#define UUID_8AC84A2217C411E0A7AF3A1BDFD72085  
./boost_1_72_0/boost/qvm/quat_operations.hpp:#ifndef UUID_E6519754D19211DFB8405F74DFD72085  
./boost_1_72_0/boost/qvm/quat_operations.hpp:#define UUID_E6519754D19211DFB8405F74DFD72085  
./boost_1_72_0/boost/qvm/quat_traits.hpp:#ifndef UUID_EF321CBE275911E084A4550FDFD72085  
./boost_1_72_0/boost/qvm/quat_traits.hpp:#define UUID_EF321CBE275911E084A4550FDFD72085  
./boost_1_72_0/boost/qvm/quat_traits_array.hpp:#ifndef UUID_6D6B1EE2119A11E291554FEE6188709B  
./boost_1_72_0/boost/qvm/quat_traits_array.hpp:#define UUID_6D6B1EE2119A11E291554FEE6188709B  
./boost_1_72_0/boost/qvm/quat_traits_defaults.hpp:#ifndef UUID_0595DEC2C72D11E6B6B77C2A07624D53  
./boost_1_72_0/boost/qvm/quat_traits_defaults.hpp:#define UUID_0595DEC2C72D11E6B6B77C2A07624D53  
./boost_1_72_0/boost/qvm/quat_vec_operations.hpp:#ifndef UUID_51968952D30A11DFAFE78CE3DFD72085  
./boost_1_72_0/boost/qvm/quat_vec_operations.hpp:#define UUID_51968952D30A11DFAFE78CE3DFD72085  
./boost_1_72_0/boost/qvm/scalar_traits.hpp:#ifndef UUID_57E1C032B9F311DEB7D9BAFE55D89593  
./boost_1_72_0/boost/qvm/scalar_traits.hpp:#define UUID_57E1C032B9F311DEB7D9BAFE55D89593  
./boost_1_72_0/boost/qvm/swizzle.hpp:#ifndef UUID_3B4919D8A41411DEB0818E4C56D89593  
./boost_1_72_0/boost/qvm/swizzle.hpp:#define UUID_3B4919D8A41411DEB0818E4C56D89593  
./boost_1_72_0/boost/qvm/to_string.hpp:#ifndef UUID_2812944066B011E29F616DCB6188709B  
./boost_1_72_0/boost/qvm/to_string.hpp:#define UUID_2812944066B011E29F616DCB6188709B  
./boost_1_72_0/boost/qvm/vec.hpp:#ifndef UUID_44EB56F0A33711DEB31B41BB56D89593  
./boost_1_72_0/boost/qvm/vec.hpp:#define UUID_44EB56F0A33711DEB31B41BB56D89593  
./boost_1_72_0/boost/qvm/vec_access.hpp:#ifndef UUID_10F2D1EA17B511E0BA29FE0BDFD72085  
./boost_1_72_0/boost/qvm/vec_access.hpp:#define UUID_10F2D1EA17B511E0BA29FE0BDFD72085  
./boost_1_72_0/boost/qvm/vec_mat_operations.hpp:#ifndef UUID_A61EC088D31511DFA59D2B03E0D72085  
./boost_1_72_0/boost/qvm/vec_mat_operations.hpp:#define UUID_A61EC088D31511DFA59D2B03E0D72085  
./boost_1_72_0/boost/qvm/vec_operations.hpp:#ifndef UUID_384AFF3AD23A11DFA80B754FE0D72085  
./boost_1_72_0/boost/qvm/vec_operations.hpp:#define UUID_384AFF3AD23A11DFA80B754FE0D72085  
./boost_1_72_0/boost/qvm/vec_traits.hpp:#ifndef UUID_01224884FC4111DF9570F7E4DED72085  
./boost_1_72_0/boost/qvm/vec_traits.hpp:#define UUID_01224884FC4111DF9570F7E4DED72085  
./boost_1_72_0/boost/qvm/vec_traits_array.hpp:#ifndef UUID_DEC6035EA1C211DEA5E8ECB856D89593  
./boost_1_72_0/boost/qvm/vec_traits_array.hpp:#define UUID_DEC6035EA1C211DEA5E8ECB856D89593  
./boost_1_72_0/boost/qvm/vec_traits_defaults.hpp:#ifndef UUID_DFFD61ACC72311E6955FFF1F07624D53  
./boost_1_72_0/boost/qvm/vec_traits_defaults.hpp:#define UUID_DFFD61ACC72311E6955FFF1F07624D53  
./boost_1_72_0/libs/qvm/test/gold.hpp:#ifndef UUID_907229FCB3A711DE83C152F855D89593  
./boost_1_72_0/libs/qvm/test/gold.hpp:#define UUID_907229FCB3A711DE83C152F855D89593  
./boost_1_72_0/libs/qvm/test/test_qvm.hpp:#ifndef UUID_06E5D36EB6C211DEA317E19C55D89593  
./boost_1_72_0/libs/qvm/test/test_qvm.hpp:#define UUID_06E5D36EB6C211DEA317E19C55D89593  
./boost_1_72_0/libs/qvm/test/test_qvm_matrix.hpp:#ifndef UUID_9C471450B3A611DEAF56C1F155D89593  
./boost_1_72_0/libs/qvm/test/test_qvm_matrix.hpp:#define UUID_9C471450B3A611DEAF56C1F155D89593  
./boost_1_72_0/libs/qvm/test/test_qvm_quaternion.hpp:#ifndef UUID_EF9152E42E4711DFB699737156D89593  
./boost_1_72_0/libs/qvm/test/test_qvm_quaternion.hpp:#define UUID_EF9152E42E4711DFB699737156D89593  
./boost_1_72_0/libs/qvm/test/test_qvm_vector.hpp:#ifndef UUID_02C176D6B3AB11DE979F9A0D56D89593  
./boost_1_72_0/libs/qvm/test/test_qvm_vector.hpp:#define UUID_02C176D6B3AB11DE979F9A0D56D89593  
```
