# #52 - List of non BOOST-prefixed macros [Open]

> Username: ned14  
> Created at: 2019-12-16 13:55:51 UTC  
> Updated at: 2022-02-02 06:21:32 UTC  
> Url: https://github.com/boostorg/wave/issues/52  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines. I have logged the Phoenix and Spirit issues separately at https://github.com/boostorg/phoenix/issues/90 and https://github.com/boostorg/spirit/issues/562:  
  
```  
./boost/wave/wave_config.hpp:#define PHOENIX_THREADSAFE 1  
./boost/wave/wave_config.hpp:#define PHOENIX_LIMIT 6  
./boost/wave/wave_config.hpp:#if PHOENIX_LIMIT < 6  
./boost/wave/wave_config.hpp:// boost/home/classic/spirit/classic_attribute.hpp sets PHOENIX_LIMIT to 3!  
./boost/wave/wave_config.hpp:#error "Boost.Wave: the constant PHOENIX_LIMIT must be at least defined to 6" \  
./boost/wave/preprocessing_hooks.hpp:#if !defined(DEFAULT_PREPROCESSING_HOOKS_HPP_INCLUDED)  
./boost/wave/preprocessing_hooks.hpp:#define DEFAULT_PREPROCESSING_HOOKS_HPP_INCLUDED  
./boost/wave/preprocessing_hooks.hpp:#endif // !defined(DEFAULT_PREPROCESSING_HOOKS_HPP_INCLUDED)  
./boost/wave/whitespace_handling.hpp:#if !defined(WHITESPACE_HANDLING_HPP_INCLUDED)  
./boost/wave/whitespace_handling.hpp:#define WHITESPACE_HANDLING_HPP_INCLUDED  
./boost/wave/whitespace_handling.hpp:#endif // !defined(WHITESPACE_HANDLING_HPP_INCLUDED)  
./boost/wave/token_ids.hpp:#if !defined(TOKEN_IDS_HPP_414E9A58_F079_4789_8AFF_513815CE475B_INCLUDED)  
./boost/wave/token_ids.hpp:#define TOKEN_IDS_HPP_414E9A58_F079_4789_8AFF_513815CE475B_INCLUDED  
./boost/wave/token_ids.hpp:#endif // !defined(TOKEN_IDS_HPP_414E9A58_F079_4789_8AFF_513815CE475B_INCLUDED)  
./boost/wave/util/unput_queue_iterator.hpp:#if !defined(UNPUT_QUEUE_ITERATOR_HPP_76DA23D0_4893_4AD5_ABCC_6CED7CFB89BC_INCLUDED)  
./boost/wave/util/unput_queue_iterator.hpp:#define UNPUT_QUEUE_ITERATOR_HPP_76DA23D0_4893_4AD5_ABCC_6CED7CFB89BC_INCLUDED  
./boost/wave/util/unput_queue_iterator.hpp:#endif // !defined(UNPUT_QUEUE_ITERATOR_HPP_76DA23D0_4893_4AD5_ABCC_6CED7CFB89BC_INCLUDED)  
./boost/wave/util/insert_whitespace_detection.hpp:#if !defined(INSERT_WHITESPACE_DETECTION_HPP_765EF77B_0513_4967_BDD6_6A38148C4C96_INCLUDED)  
./boost/wave/util/insert_whitespace_detection.hpp:#define INSERT_WHITESPACE_DETECTION_HPP_765EF77B_0513_4967_BDD6_6A38148C4C96_INCLUDED  
./boost/wave/util/insert_whitespace_detection.hpp:#endif // !defined(INSERT_WHITESPACE_DETECTION_HPP_765EF77B_0513_4967_BDD6_6A38148C4C96_INCLUDED)  
./boost/wave/util/symbol_table.hpp:#if !defined(SYMBOL_TABLE_HPP_32B0F7C6_3DD6_4113_95A5_E16516C6F45A_INCLUDED)  
./boost/wave/util/symbol_table.hpp:#define SYMBOL_TABLE_HPP_32B0F7C6_3DD6_4113_95A5_E16516C6F45A_INCLUDED  
./boost/wave/util/symbol_table.hpp:#endif // !defined(SYMBOL_TABLE_HPP_32B0F7C6_3DD6_4113_95A5_E16516C6F45A_INCLUDED)  
./boost/wave/util/macro_definition.hpp:#if !defined(MACRO_DEFINITION_HPP_D68A639E_2DA5_4E9C_8ACD_CFE6B903831E_INCLUDED)  
./boost/wave/util/macro_definition.hpp:#define MACRO_DEFINITION_HPP_D68A639E_2DA5_4E9C_8ACD_CFE6B903831E_INCLUDED  
./boost/wave/util/macro_definition.hpp:#endif // !defined(MACRO_DEFINITION_HPP_D68A639E_2DA5_4E9C_8ACD_CFE6B903831E_INCLUDED)  
./boost/wave/util/cpp_iterator.hpp:#if !defined(CPP_ITERATOR_HPP_175CA88F_7273_43FA_9039_BCF7459E1F29_INCLUDED)  
./boost/wave/util/cpp_iterator.hpp:#define CPP_ITERATOR_HPP_175CA88F_7273_43FA_9039_BCF7459E1F29_INCLUDED  
./boost/wave/util/cpp_iterator.hpp:#endif // !defined(CPP_ITERATOR_HPP_175CA88F_7273_43FA_9039_BCF7459E1F29_INCLUDED)  
./boost/wave/util/transform_iterator.hpp:#if !defined(TRANSFORM_ITERATOR_HPP_D492C659_88C7_4258_8C42_192F9AE80EC0_INCLUDED)  
./boost/wave/util/transform_iterator.hpp:#define TRANSFORM_ITERATOR_HPP_D492C659_88C7_4258_8C42_192F9AE80EC0_INCLUDED  
./boost/wave/util/transform_iterator.hpp:#endif // !defined(TRANSFORM_ITERATOR_HPP_D492C659_88C7_4258_8C42_192F9AE80EC0_INCLUDED)  
./boost/wave/util/cpp_ifblock.hpp:#if !defined(CPP_IFBLOCK_HPP_D4676B36_00C5_41F4_BC9F_9CBBAE3B8006_INCLUDED)  
./boost/wave/util/cpp_ifblock.hpp:#define CPP_IFBLOCK_HPP_D4676B36_00C5_41F4_BC9F_9CBBAE3B8006_INCLUDED  
./boost/wave/util/cpp_ifblock.hpp:#endif // !defined(CPP_IFBLOCK_HPP_D4676B36_00C5_41F4_BC9F_9CBBAE3B8006_INCLUDED)  
./boost/wave/util/macro_helpers.hpp:#if !defined(MACRO_HELPERS_HPP_931BBC99_EBFA_4692_8FBE_B555998C2C39_INCLUDED)  
./boost/wave/util/macro_helpers.hpp:#define MACRO_HELPERS_HPP_931BBC99_EBFA_4692_8FBE_B555998C2C39_INCLUDED  
./boost/wave/util/macro_helpers.hpp:#endif // !defined(MACRO_HELPERS_HPP_931BBC99_EBFA_4692_8FBE_B555998C2C39_INCLUDED)  
./boost/wave/util/cpp_macromap.hpp:#if !defined(CPP_MACROMAP_HPP_CB8F51B0_A3F0_411C_AEF4_6FF631B8B414_INCLUDED)  
./boost/wave/util/cpp_macromap.hpp:#define CPP_MACROMAP_HPP_CB8F51B0_A3F0_411C_AEF4_6FF631B8B414_INCLUDED  
./boost/wave/util/cpp_macromap.hpp:#endif // !defined(CPP_MACROMAP_HPP_CB8F51B0_A3F0_411C_AEF4_6FF631B8B414_INCLUDED)  
./boost/wave/util/cpp_include_paths.hpp:#if !defined(CPP_INCLUDE_PATHS_HPP_AF620DA4_B3D2_4221_AD91_8A1ABFFB6944_INCLUDED)  
./boost/wave/util/cpp_include_paths.hpp:#define CPP_INCLUDE_PATHS_HPP_AF620DA4_B3D2_4221_AD91_8A1ABFFB6944_INCLUDED  
./boost/wave/util/cpp_include_paths.hpp:#endif // !defined(CPP_INCLUDE_PATHS_HPP_AF620DA4_B3D2_4221_AD91_8A1ABFFB6944_INCLUDED)  
./boost/wave/util/file_position.hpp:#if !defined(FILE_POSITION_H_52BDEDF7_DAD3_4F24_802F_E66BB8098F68_INCLUDED)  
./boost/wave/util/file_position.hpp:#define FILE_POSITION_H_52BDEDF7_DAD3_4F24_802F_E66BB8098F68_INCLUDED  
./boost/wave/util/file_position.hpp:#endif // !defined(FILE_POSITION_H_52BDEDF7_DAD3_4F24_802F_E66BB8098F68_INCLUDED)  
./boost/wave/util/functor_input.hpp:#if !defined(FUNCTOR_INPUT_HPP_ED3A4C21_8F8A_453F_B438_08214FAC106A_INCLUDED)  
./boost/wave/util/functor_input.hpp:#define FUNCTOR_INPUT_HPP_ED3A4C21_8F8A_453F_B438_08214FAC106A_INCLUDED  
./boost/wave/util/functor_input.hpp:#endif // !defined(FUNCTOR_INPUT_HPP_ED3A4C21_8F8A_453F_B438_08214FAC106A_INCLUDED)  
./boost/wave/util/time_conversion_helper.hpp:#if !defined(TIME_CONVERSION_HELPER_HPP_DA97E389_1797_43BA_82AE_B071064B3EF4_INCLUDED)  
./boost/wave/util/time_conversion_helper.hpp:#define TIME_CONVERSION_HELPER_HPP_DA97E389_1797_43BA_82AE_B071064B3EF4_INCLUDED  
./boost/wave/util/time_conversion_helper.hpp:#endif // !defined(TIME_CONVERSION_HELPER_HPP_DA97E389_1797_43BA_82AE_B071064B3EF4_INCLUDED)  
./boost/wave/util/iteration_context.hpp:#if !defined(ITERATION_CONTEXT_HPP_9556CD16_F11E_4ADC_AC8B_FB9A174BE664_INCLUDED)  
./boost/wave/util/iteration_context.hpp:#define ITERATION_CONTEXT_HPP_9556CD16_F11E_4ADC_AC8B_FB9A174BE664_INCLUDED  
./boost/wave/util/iteration_context.hpp:#endif // !defined(ITERATION_CONTEXT_HPP_9556CD16_F11E_4ADC_AC8B_FB9A174BE664_INCLUDED)  
./boost/wave/util/interpret_pragma.hpp:#if !defined(INTERPRET_PRAGMA_HPP_B1F2315E_C5CE_4ED1_A343_0EF548B7942A_INCLUDED)  
./boost/wave/util/interpret_pragma.hpp:#define INTERPRET_PRAGMA_HPP_B1F2315E_C5CE_4ED1_A343_0EF548B7942A_INCLUDED  
./boost/wave/util/interpret_pragma.hpp:#endif // !defined(INTERPRET_PRAGMA_HPP_B1F2315E_C5CE_4ED1_A343_0EF548B7942A_INCLUDED)  
./boost/wave/wave_config.hpp:#if !defined(WAVE_CONFIG_HPP_F143F90A_A63F_4B27_AC41_9CA4F14F538D_INCLUDED)  
./boost/wave/wave_config.hpp:#define WAVE_CONFIG_HPP_F143F90A_A63F_4B27_AC41_9CA4F14F538D_INCLUDED  
./boost/wave/wave_config.hpp:#endif // !defined(WAVE_CONFIG_HPP_F143F90A_A63F_4B27_AC41_9CA4F14F538D_INCLUDED)  
./boost/wave/wave_version.hpp:#if !defined(WAVE_VERSION_H_9D79ABDB_AC54_4C0A_89B1_F70A2DCFE21E_INCLUDED)  
./boost/wave/wave_version.hpp:#define WAVE_VERSION_H_9D79ABDB_AC54_4C0A_89B1_F70A2DCFE21E_INCLUDED  
./boost/wave/wave_version.hpp:#endif // !defined(WAVE_VERSION_H_9D79ABDB_AC54_4C0A_89B1_F70A2DCFE21E_INCLUDED)  
./boost/wave/cpp_exceptions.hpp:#if !defined(CPP_EXCEPTIONS_HPP_5190E447_A781_4521_A275_5134FF9917D7_INCLUDED)  
./boost/wave/cpp_exceptions.hpp:#define CPP_EXCEPTIONS_HPP_5190E447_A781_4521_A275_5134FF9917D7_INCLUDED  
./boost/wave/cpp_exceptions.hpp:#endif // !defined(CPP_EXCEPTIONS_HPP_5190E447_A781_4521_A275_5134FF9917D7_INCLUDED)  
./boost/wave/cpplexer/cpp_lex_interface.hpp:#if !defined(CPP_LEX_INTERFACE_HPP_E83F52A4_90AC_4FBE_A9A7_B65F7F94C497_INCLUDED)  
./boost/wave/cpplexer/cpp_lex_interface.hpp:#define CPP_LEX_INTERFACE_HPP_E83F52A4_90AC_4FBE_A9A7_B65F7F94C497_INCLUDED  
./boost/wave/cpplexer/cpp_lex_interface.hpp:#endif // !defined(CPP_LEX_INTERFACE_HPP_E83F52A4_90AC_4FBE_A9A7_B65F7F94C497_INCLUDED)  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:#if !defined(CPPLEXER_EXCEPTIONS_HPP_1A09DE1A_6D1F_4091_AF7F_5F13AB0D31AB_INCLUDED)  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:#define CPPLEXER_EXCEPTIONS_HPP_1A09DE1A_6D1F_4091_AF7F_5F13AB0D31AB_INCLUDED  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:#endif // !defined(CPPLEXER_EXCEPTIONS_HPP_1A09DE1A_6D1F_4091_AF7F_5F13AB0D31AB_INCLUDED)  
./boost/wave/cpplexer/re2clex/aq.hpp:#if !defined(AQ_HPP_A21D9145_B643_44C0_81E7_DB346DD67EE1_INCLUDED)  
./boost/wave/cpplexer/re2clex/aq.hpp:#define AQ_HPP_A21D9145_B643_44C0_81E7_DB346DD67EE1_INCLUDED  
./boost/wave/cpplexer/re2clex/aq.hpp:#endif // !defined(AQ_HPP_A21D9145_B643_44C0_81E7_DB346DD67EE1_INCLUDED)  
./boost/wave/cpplexer/re2clex/cpp_re.hpp:#if !defined(CPP_RE_HPP_B76C4F5E_63E9_4B8A_9975_EC32FA6BF027_INCLUDED)  
./boost/wave/cpplexer/re2clex/cpp_re.hpp:#define CPP_RE_HPP_B76C4F5E_63E9_4B8A_9975_EC32FA6BF027_INCLUDED  
./boost/wave/cpplexer/re2clex/cpp_re.hpp:#endif // !defined(CPP_RE_HPP_B76C4F5E_63E9_4B8A_9975_EC32FA6BF027_INCLUDED)  
./boost/wave/cpplexer/re2clex/scanner.hpp:#if !defined(SCANNER_HPP_F4FB01EB_E75C_4537_A146_D34B9895EF37_INCLUDED)  
./boost/wave/cpplexer/re2clex/scanner.hpp:#define SCANNER_HPP_F4FB01EB_E75C_4537_A146_D34B9895EF37_INCLUDED  
./boost/wave/cpplexer/re2clex/scanner.hpp:#endif // !defined(SCANNER_HPP_F4FB01EB_E75C_4537_A146_D34B9895EF37_INCLUDED)  
./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:#if !defined(CPP_RE2C_LEXER_HPP_B81A2629_D5B1_4944_A97D_60254182B9A8_INCLUDED)  
./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:#define CPP_RE2C_LEXER_HPP_B81A2629_D5B1_4944_A97D_60254182B9A8_INCLUDED  
./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:#endif // !defined(CPP_RE2C_LEXER_HPP_B81A2629_D5B1_4944_A97D_60254182B9A8_INCLUDED)  
./boost/wave/cpplexer/validate_universal_char.hpp:#if !defined(VALIDATE_UNIVERSAL_CHAR_HPP_55F1B811_CD76_4C72_8344_CBC69CF3B339_INCLUDED)  
./boost/wave/cpplexer/validate_universal_char.hpp:#define VALIDATE_UNIVERSAL_CHAR_HPP_55F1B811_CD76_4C72_8344_CBC69CF3B339_INCLUDED  
./boost/wave/cpplexer/validate_universal_char.hpp:#endif // !defined(VALIDATE_UNIVERSAL_CHAR_HPP_55F1B811_CD76_4C72_8344_CBC69CF3B339_INCLUDED)  
./boost/wave/cpplexer/cpp_lex_token.hpp:#if !defined(CPP_TOKEN_HPP_53A13BD2_FBAA_444B_9B8B_FCB225C2BBA8_INCLUDED)  
./boost/wave/cpplexer/cpp_lex_token.hpp:#define CPP_TOKEN_HPP_53A13BD2_FBAA_444B_9B8B_FCB225C2BBA8_INCLUDED  
./boost/wave/cpplexer/cpp_lex_token.hpp:#endif // !defined(CPP_TOKEN_HPP_53A13BD2_FBAA_444B_9B8B_FCB225C2BBA8_INCLUDED)  
./boost/wave/cpplexer/cpp_lex_iterator.hpp:#if !defined(CPP_LEX_ITERATOR_HPP_AF0C37E3_CBD8_4F33_A225_51CF576FA61F_INCLUDED)  
./boost/wave/cpplexer/cpp_lex_iterator.hpp:#define CPP_LEX_ITERATOR_HPP_AF0C37E3_CBD8_4F33_A225_51CF576FA61F_INCLUDED  
./boost/wave/cpplexer/cpp_lex_iterator.hpp:#endif // !defined(CPP_LEX_ITERATOR_HPP_AF0C37E3_CBD8_4F33_A225_51CF576FA61F_INCLUDED)  
./boost/wave/cpplexer/token_cache.hpp:#if !defined(TOKEN_CACHE_HPP_4D2320B7_1D56_4113_A114_397E70FA438C_INCLUDED)  
./boost/wave/cpplexer/token_cache.hpp:#define TOKEN_CACHE_HPP_4D2320B7_1D56_4113_A114_397E70FA438C_INCLUDED  
./boost/wave/cpplexer/token_cache.hpp:#endif // !defined(TOKEN_CACHE_HPP_4D2320B7_1D56_4113_A114_397E70FA438C_INCLUDED)  
./boost/wave/grammars/cpp_intlit_grammar.hpp:#if !defined(CPP_INTLIT_GRAMMAR_HPP_2E1E70B1_F15C_4132_8554_10A231B0D91C_INCLUDED)  
./boost/wave/grammars/cpp_intlit_grammar.hpp:#define CPP_INTLIT_GRAMMAR_HPP_2E1E70B1_F15C_4132_8554_10A231B0D91C_INCLUDED  
./boost/wave/grammars/cpp_intlit_grammar.hpp:#endif // !defined(CPP_INTLIT_GRAMMAR_HPP_2E1E70B1_F15C_4132_8554_10A231B0D91C_INCLUDED)  
./boost/wave/grammars/cpp_expression_grammar_gen.hpp:#if !defined(CPP_EXPRESSION_GRAMMAR_GEN_HPP_42399258_6CDC_4101_863D_5C7D95B5A6CA_INCLUDED)  
./boost/wave/grammars/cpp_expression_grammar_gen.hpp:#define CPP_EXPRESSION_GRAMMAR_GEN_HPP_42399258_6CDC_4101_863D_5C7D95B5A6CA_INCLUDED  
./boost/wave/grammars/cpp_expression_grammar_gen.hpp:#endif // !defined(CPP_EXPRESSION_GRAMMAR_GEN_HPP_42399258_6CDC_4101_863D_5C7D95B5A6CA_INCLUDED)  
./boost/wave/grammars/cpp_expression_value.hpp:#if !defined(CPP_EXPRESSION_VALUE_HPP_452FE66D_8754_4107_AF1E_E42255A0C18A_INCLUDED)  
./boost/wave/grammars/cpp_expression_value.hpp:#define CPP_EXPRESSION_VALUE_HPP_452FE66D_8754_4107_AF1E_E42255A0C18A_INCLUDED  
./boost/wave/grammars/cpp_expression_value.hpp:#endif // !defined(CPP_EXPRESSION_VALUE_HPP_452FE66D_8754_4107_AF1E_E42255A0C18A_INCLUDED)  
./boost/wave/grammars/cpp_expression_grammar.hpp:#if !defined(CPP_EXPRESSION_GRAMMAR_HPP_099CD1A4_A6C0_44BE_8F24_0B00F5BE5674_INCLUDED)  
./boost/wave/grammars/cpp_expression_grammar.hpp:#define CPP_EXPRESSION_GRAMMAR_HPP_099CD1A4_A6C0_44BE_8F24_0B00F5BE5674_INCLUDED  
./boost/wave/grammars/cpp_expression_grammar.hpp:#endif // !defined(CPP_EXPRESSION_GRAMMAR_HPP_099CD1A4_A6C0_44BE_8F24_0B00F5BE5674_INCLUDED)  
./boost/wave/grammars/cpp_chlit_grammar.hpp:#if !defined(CPP_CHLIT_GRAMMAR_HPP_9527D349_6592_449A_A409_42A001E6C64C_INCLUDED)  
./boost/wave/grammars/cpp_chlit_grammar.hpp:#define CPP_CHLIT_GRAMMAR_HPP_9527D349_6592_449A_A409_42A001E6C64C_INCLUDED  
./boost/wave/grammars/cpp_chlit_grammar.hpp:#endif // !defined(CPP_CHLIT_GRAMMAR_HPP_9527D349_6592_449A_A409_42A001E6C64C_INCLUDED)  
./boost/wave/grammars/cpp_grammar.hpp:#if !defined(CPP_GRAMMAR_HPP_FEAEBC2E_2734_428B_A7CA_85E5A415E23E_INCLUDED)  
./boost/wave/grammars/cpp_grammar.hpp:#define CPP_GRAMMAR_HPP_FEAEBC2E_2734_428B_A7CA_85E5A415E23E_INCLUDED  
./boost/wave/grammars/cpp_grammar.hpp:#endif // !defined(CPP_GRAMMAR_HPP_FEAEBC2E_2734_428B_A7CA_85E5A415E23E_INCLUDED)  
./boost/wave/grammars/cpp_defined_grammar.hpp:#if !defined(CPP_DEFINED_GRAMMAR_HPP_F48287B2_DC67_40A8_B4A1_800EFBD67869_INCLUDED)  
./boost/wave/grammars/cpp_defined_grammar.hpp:#define CPP_DEFINED_GRAMMAR_HPP_F48287B2_DC67_40A8_B4A1_800EFBD67869_INCLUDED  
./boost/wave/grammars/cpp_defined_grammar.hpp:#endif // !defined(CPP_DEFINED_GRAMMAR_HPP_F48287B2_DC67_40A8_B4A1_800EFBD67869_INCLUDED)  
./boost/wave/grammars/cpp_defined_grammar_gen.hpp:#if !defined(CPP_DEFINED_GRAMMAR_GEN_HPP_825BE9F5_98A3_400D_A97C_AD76B3B08632_INCLUDED)  
./boost/wave/grammars/cpp_defined_grammar_gen.hpp:#define CPP_DEFINED_GRAMMAR_GEN_HPP_825BE9F5_98A3_400D_A97C_AD76B3B08632_INCLUDED  
./boost/wave/grammars/cpp_defined_grammar_gen.hpp:#endif // !defined(CPP_DEFINED_GRAMMAR_GEN_HPP_825BE9F5_98A3_400D_A97C_AD76B3B08632_INCLUDED)  
./boost/wave/grammars/cpp_grammar_gen.hpp:#if !defined(CPP_GRAMMAR_GEN_HPP_80CB8A59_5411_4E45_B406_62531A12FB99_INCLUDED)  
./boost/wave/grammars/cpp_grammar_gen.hpp:#define CPP_GRAMMAR_GEN_HPP_80CB8A59_5411_4E45_B406_62531A12FB99_INCLUDED  
./boost/wave/grammars/cpp_grammar_gen.hpp:#endif // !defined(CPP_GRAMMAR_GEN_HPP_80CB8A59_5411_4E45_B406_62531A12FB99_INCLUDED)  
./boost/wave/grammars/cpp_literal_grammar_gen.hpp:#if !defined(CPP_LITERAL_GRAMMAR_GEN_HPP_67794A6C_468A_4AAB_A757_DEDDB182F5A0_INCLUDED)  
./boost/wave/grammars/cpp_literal_grammar_gen.hpp:#define CPP_LITERAL_GRAMMAR_GEN_HPP_67794A6C_468A_4AAB_A757_DEDDB182F5A0_INCLUDED  
./boost/wave/grammars/cpp_literal_grammar_gen.hpp:#endif // !defined(CPP_LITERAL_GRAMMAR_GEN_HPP_67794A6C_468A_4AAB_A757_DEDDB182F5A0_INCLUDED)  
./boost/wave/grammars/cpp_predef_macros_grammar.hpp:#if !defined(CPP_PREDEF_MACROS_GRAMMAR_HPP_53858C9A_C202_4D60_AD92_DC9CAE4DBB43_INCLUDED)  
./boost/wave/grammars/cpp_predef_macros_grammar.hpp:#define CPP_PREDEF_MACROS_GRAMMAR_HPP_53858C9A_C202_4D60_AD92_DC9CAE4DBB43_INCLUDED  
./boost/wave/grammars/cpp_predef_macros_grammar.hpp:#endif // !defined(CPP_PREDEF_MACROS_GRAMMAR_HPP_53858C9A_C202_4D60_AD92_DC9CAE4DBB43_INCLUDED)  
./boost/wave/grammars/cpp_predef_macros_gen.hpp:#if !defined(CPP_PREDEF_MACROS_GEN_HPP_CADB6D2C_76A4_4988_83E1_EFFC6902B9A2_INCLUDED)  
./boost/wave/grammars/cpp_predef_macros_gen.hpp:#define CPP_PREDEF_MACROS_GEN_HPP_CADB6D2C_76A4_4988_83E1_EFFC6902B9A2_INCLUDED  
./boost/wave/grammars/cpp_predef_macros_gen.hpp:#endif // !defined(CPP_PREDEF_MACROS_GEN_HPP_CADB6D2C_76A4_4988_83E1_EFFC6902B9A2_INCLUDED)  
./boost/wave/cpp_context.hpp:#if !defined(CPP_CONTEXT_HPP_907485E2_6649_4A87_911B_7F7225F3E5B8_INCLUDED)  
./boost/wave/cpp_context.hpp:#define CPP_CONTEXT_HPP_907485E2_6649_4A87_911B_7F7225F3E5B8_INCLUDED  
./boost/wave/cpp_context.hpp:#endif // !defined(CPP_CONTEXT_HPP_907485E2_6649_4A87_911B_7F7225F3E5B8_INCLUDED)  
./boost/wave/cpp_iteration_context.hpp:#if !defined(CPP_ITERATION_CONTEXT_HPP_00312288_9DDB_4668_AFE5_25D3994FD095_INCLUDED)  
./boost/wave/cpp_iteration_context.hpp:#define CPP_ITERATION_CONTEXT_HPP_00312288_9DDB_4668_AFE5_25D3994FD095_INCLUDED  
./boost/wave/cpp_iteration_context.hpp:#endif // !defined(CPP_ITERATION_CONTEXT_HPP_00312288_9DDB_4668_AFE5_25D3994FD095_INCLUDED)  
./boost/wave/language_support.hpp:#if !defined(LANGUAGE_SUPPORT_HPP_93EDD057_2DEF_44BC_BC9F_FDABB9F51AFA_INCLUDED)  
./boost/wave/language_support.hpp:#define LANGUAGE_SUPPORT_HPP_93EDD057_2DEF_44BC_BC9F_FDABB9F51AFA_INCLUDED  
./boost/wave/language_support.hpp:#endif // !defined(LANGUAGE_SUPPORT_HPP_93EDD057_2DEF_44BC_BC9F_FDABB9F51AFA_INCLUDED)  
```

---

## Comment 1

> Username: hkaiser  
> Created at: 2019-12-18 10:52:09 UTC  
> Url: https://github.com/boostorg/wave/issues/52#issuecomment-566981191  

I have not seen any serious discussion of the requirement for prefixing all macros with Boost on the mailing list. Could you give me a link to the email thread, please?

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-05-22 02:56:18 UTC  
> Url: https://github.com/boostorg/wave/issues/52#issuecomment-632450478  

@hkaiser sounds from the related discussion that this is a good thing to do even for the include guards. Shouldn't be hard to tackle. Should we simply prepend `BOOST_` on all of them or should the names be simplified in any way as well? I am unfamiliar with the idiom of using those UUIDs or whatever they are...

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-05-22 12:05:15 UTC  
> Url: https://github.com/boostorg/wave/issues/52#issuecomment-632658004  

@jefftrull Just prepending `BOOST_` should be sufficient. We have used the UUIDs just to make sure the guards are unique. There is no real scheme behind the naming of the guards.

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-06-15 20:23:44 UTC  
> Url: https://github.com/boostorg/wave/issues/52#issuecomment-644364293  

There is one remaining macro in the list above, from Phoenix - we would have to coordinate with them https://github.com/boostorg/phoenix/issues/90

---

## Comment 5

> Username: jefftrull  
> Created at: 2022-02-02 06:21:32 UTC  
> Url: https://github.com/boostorg/wave/issues/52#issuecomment-1027624097  

I just realized that the remaining Phoenix macros are from Spirit Classic and (judging from a quick look at their issue tracker) are unlikely to be addressed. At this late date it would be better to upgrade to V2 or X3 than to pursue the last few macros.
