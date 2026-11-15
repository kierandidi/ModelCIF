# Validation of proposed component 0.3.0

The dictionary and both complete examples were reviewed on 15 November 2026. The previous generic annotation component and its test results do not apply to this replacement. This PR contains no production adapters or Python runtime dependency.

## Reproduce dictionary checks

Initialize the repository's pinned `mmcif_pdbx` submodule. With Python 3.12 and `mmcif==1.2.1`, run from the repository root:

```sh
mkdir -p build/annotations
build_dict_cli --op build --input_dict_path base/mmcif_ma-generator-local.dic --output_dict_path build/annotations/mmcif_ma.dic --cleanup
build_dict_cli --op build --input_dict_path base/mmcif_ma_ext-generator-local.dic --output_dict_path build/annotations/mmcif_ma_ext.dic --cleanup
```

Build `DictToSdb` and `CifCheck` from [RCSB cpp-dict-pack](https://github.com/rcsb/cpp-dict-pack). This review used commit `b227176b018b79cda33422a1676880e73df4a209` and its DDL 2.3.3. Run checks in a **fresh directory**: the tools can append to diagnostic files from a previous run.

```sh
DictToSdb -ddlFile /path/to/mmcif_ddl.dic -dictFile /path/to/build/annotations/mmcif_ma.dic -dictSdbFile annotations.sdb
CifCheck -dictSdb annotations.sdb -f /path/to/worked-annotations.cif
CifCheck -dictSdb annotations.sdb -f /path/to/extended-annotations.cif
```

Inspect the `*-diag.log` and `*-parser.log` files, not only exit status. The combined dictionary is the standalone validation target; the extension-only dictionary depends on the PDBx parent definitions.

## Results

| Check | Result |
| --- | --- |
| Both dictionary generators | Assembled successfully; 7 new categories and 90 items |
| Combined dictionary against RCSB DDL | No diagnostic errors |
| Both new complete examples | No parser/dictionary diagnostics |
| Invalid context stage | Rejected by CifCheck |
| Declared model with an atom index belonging only to another model | Rejected by composite dictionary link groups |
| Four existing repository examples | Diagnostic text identical to published-dictionary baseline; pre-existing diagnostics remain |
| Field catalogue | 59 unique source mappings and no case-insensitive proposed-name collisions |
| Native coordinate representation | Both example XYZ payloads equal the inspected AtomWorks `_idxs_values_to_cif_dict` output |
| Local reference checks | Reordered atoms, filtered noncontiguous indices and reused indices in distinct models retain interpretation |
| Local negative controls | 13 mutations rejected: duplicate/negative indices, missing/wrong-model endpoints, partial/infinite XYZ, coordinate-group mismatches, unknown/out-of-range/overfull probabilities, identity conflicts and duplicate token offsets |
| Independent encoding check | Biotite CIF/BinaryCIF roundtrip preserves both examples' column payloads and null masks |

The local comparison/mutation checks used Gemmi 0.7.5, NumPy and Biotite 2.0.0. Only the pure serialization helper was executed from the inspected AtomWorks source; the production AtomWorks runtime requires its own dependency set. Authoring/review utilities remain outside the PR. The tests reported here are bounded review checks, not a complete scientific validator.

## Adapter acceptance

DDL2 checks types, keys, enumerations and declared links. Readers must also enforce the contract's broadcast agreement, pair symmetry, finite geometry, group membership, probability/identity rules, defaults, token offsets, profile interpretation and annotation lifecycle. The draft does not claim a production AtomWorks/RFD4/python-modelcif adapter, executed design equivalence, exhaustive API coverage, deposition certification or performance results.

Acceptance tests for those adapters must compare effective model inputs before and after conversion, including masks and inactive states. Cover absolute/group-local conditions, scalar/vector/pair/custom annotations, model stacks, missing values, cropping/permutation, assembly copies, generated defaults, tokenization, expandable markers and ambiguous legacy base-pair rows. Preserving unknown custom fields is distinct from understanding them for required execution.
