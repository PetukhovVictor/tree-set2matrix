# ast-set2matrix

Program for step-by-step conversion of AST set to the matrix (dataset) using [**ast2vec**](https://github.com/PetukhovVictor/ast2vec)

Available steps (stages):
- **asts2vectors**: ast from specified input folder conversion to vectors in specified output folder (with same path);
- **sparse_transformation**: convert "vectors" (but actually is map "feature-value") to sparse representation (two formats: matrix or map);
- **normalize**: normalization feature values by the number of all features in the current file;
- **collect_statistic**: collection features statistic (create sorted lists with features and their frequency for specified n).

## Program use
### AST to vectors
#### Program arguments

* **-s, --stage** -> asts2vectors;
* **-i, --input_folder**: input folder with ASTs (in JSON format);
* **-o, --output_folder**: output folder with "vectors" (but actually is map "feature-value");
* **--ast2vec_path**: path to [ast2vec](https://github.com/PetukhovVictor/ast2vec)

Also see ast2vec README: https://github.com/PetukhovVictor/ast2vec (ASTs format, output format, etc)

At this stage a file with all features is generated (all_features.json in the output folder). **This file required in the next stages.**

#### Example of use
```
python3 main.py -s asts2vectors -i ./ast -o ./ast_vectors --ast2vec_path ../ast2vec/
```

### Sparse transformation
#### Program arguments

* **-s, --stage** -> sparse_transformation;
* **-i, --input_folder**: input folder with "vectors" (but actually is map "feature-value") - from previous stage (asts2vectors);
* **-o, --output_folder**: output folder with sparsed "vectors";
* **--sparse_format**: list or map.
* **--all_features_file**: file with all feature generated by asts2vectors stage.

#### Output format

If the 'list' was specified (for example):
```
[4, 0, 0, 11, 0, 0, 0, 12, 0, 0, 0, 0, 65, 4, 0, 0, 0, 0, 0, 91, 0, 1, 0, 0, 0, 0, 0, 0, 45, 0, 0, 103, 0, 0, 0, 0, 0, 9, 3]
```
Order elements same as in all features file.

If the 'map' was specified (for example):
```
{"FUN": 14, "MODIFIER_LIST": 3, "FUN:MODIFIER_LIST": 1, "public": 0, "MODIFIER_LIST:public": 0, "FUN:public": 1, "FUN:MODIFIER_LIST:public": 0, "MODIFIER_LIST:WHITE_SPACE": 0, "FUN:MODIFIER_LIST:WHITE_SPACE": 0, "open": 8, "MODIFIER_LIST:open": 0, "FUN:open": 1, "FUN:MODIFIER_LIST:open": 0, "fun": 1, "FUN:fun": 0, "FUN:IDENTIFIER": 2, "VALUE_PARAMETER_LIST": 3, "FUN:VALUE_PARAMETER_LIST": 0, "VALUE_PARAMETER_LIST:LPAR": 1, "FUN:LPAR": 1, "FUN:VALUE_PARAMETER_LIST:LPAR": 0, "VALUE_PARAMETER": 6, "VALUE_PARAMETER_LIST:VALUE_PARAMETER": 1, "FUN:VALUE_PARAMETER": 1, "FUN:VALUE_PARAMETER_LIST:VALUE_PARAMETER": 0, "VALUE_PARAMETER:IDENTIFIER": 1, "VALUE_PARAMETER_LIST:IDENTIFIER": 1, "VALUE_PARAMETER_LIST:VALUE_PARAMETER:IDENTIFIER": 0, "FUN:VALUE_PARAMETER:IDENTIFIER": 1, "FUN:VALUE_PARAMETER_LIST:IDENTIFIER": 0, "COLON": 2, "VALUE_PARAMETER:COLON": 0, "VALUE_PARAMETER_LIST:COLON": 1, "FUN:COLON": 22, "VALUE_PARAMETER_LIST:VALUE_PARAMETER:COLON": 2, "FUN:VALUE_PARAMETER:COLON": 0, "FUN:VALUE_PARAMETER_LIST:COLON": 0, "VALUE_PARAMETER:WHITE_SPACE": 13}
```

#### Example of use
```
python3 main.py -s sparse_transformation -i ./ast_vectors -o ./ast_vectors_sparsed --all_features_file ./ast_vectors/all_features.json
```

### Normalization
#### Program arguments

* **-s, --stage** -> normalize;
* **-i, --input_folder**: input folder with "vectors" (but actually is map "feature-value") - from previous stages (sparse_transformation with sparse_format=map or asts2vectors: sparsed or not);
* **-o, --output_folder**: output folder with normalized "vectors";
* **--all_features_file**: file with all feature generated by asts2vectors stage.

#### Example output

```
{"FUN": 0.0007215007215007215, "MODIFIER_LIST": 0.0007215007215007215, "FUN:MODIFIER_LIST": 0.0007215007215007215, "public": 0.0007215007215007215, "MODIFIER_LIST:public": 0.0007215007215007215, "FUN:public": 0.0007215007215007215, "FUN:MODIFIER_LIST:public": 0.0007215007215007215, "MODIFIER_LIST:WHITE_SPACE": 0.0007215007215007215, "FUN:MODIFIER_LIST:WHITE_SPACE": 0.0007215007215007215, "open": 0.0007215007215007215, "MODIFIER_LIST:open": 0.0007215007215007215, "FUN:open": 0.0007215007215007215, "FUN:MODIFIER_LIST:open": 0.0007215007215007215, "fun": 0.0007215007215007215, "FUN:fun": 0.0007215007215007215, "FUN:IDENTIFIER": 0.0007215007215007215, "VALUE_PARAMETER_LIST": 0.0007215007215007215, "FUN:VALUE_PARAMETER_LIST": 0.0007215007215007215, "VALUE_PARAMETER_LIST:LPAR": 0.0007215007215007215, "FUN:LPAR": 0.0007215007215007215, "FUN:VALUE_PARAMETER_LIST:LPAR": 0.0007215007215007215, "VALUE_PARAMETER_LIST:RPAR": 0.0007215007215007215, "LBRACE": 0.0007215007215007215, "BLOCK:LBRACE": 0.0007215007215007215, "FUN:LBRACE": 0.0007215007215007215, "FUN:BLOCK:LBRACE": 0.0007215007215007215, "RETURN": 0.0007215007215007215, "BLOCK:RETURN": 0.0007215007215007215, "FUN:RETURN": 0.0007215007215007215, "FUN:BLOCK:RETURN": 0.0007215007215007215, "return": 0.0007215007215007215}
```

#### Example of use
```
main.py -s normalize -i ./ast_vectors -o ./ast_vectors_normalized --all_features_file ./ast_vectors/all_features.json
```

### Statistic collection

Collection feature frequency statistic via feature statistic file generated by asts2vectors stage.

#### Program arguments

* **-s, --stage** -> collect_statistic;
* **-o, --output_folder**: output folder with statistic files (all_features_sorted.json, all_features_sorted_1.json, , all_features_sorted_2.json, ..., all_features_sorted_n.json);
* **--all_features_file**: file with all feature generated by asts2vectors stage.


#### Example of use
```
python3 main.py -s collect_statistic -o ./features_statistic --all_features_file ./ast_vectors/all_features.json
```
