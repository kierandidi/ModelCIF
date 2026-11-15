# Annotated CIF as a ModelCIF extension

Standardize the annotation layout used by AtomWorks and RFD4-Proteina within ModelCIF. Keep scalar annotations as `atom_site` columns, vectors as `idx0/val0/val1/val2` rows, and sparse pairs as `idx0/idx1/val0` rows. Define their meanings in the dictionary and add the context needed for **tool pipelines, comparable benchmarks and exchangeable preprocessed datasets**.

**Proposed component 0.3.0; working-group draft.** This revision starts from the existing storage contract. It defines 7 new categories and 90 items: one context category, six native payload categories, 52 scalar annotation items and four reference/membership items on the existing `atom_site` category. It covers 59 inventoried source entries; distance uses existing ModelCIF restraints. ModelCIF 1.4.9 remains the released standard. No released category is removed or redefined.

## Preserve the existing construction

The name still identifies the annotation, body order and semantic level. Body order counts operands: 0 for a system value, 1 for one subject, 2 for a pair. The six levels remain atom, residue, token, chain, molecule and system. A residue-level scalar can remain broadcast over its atoms; consumers check agreement before collapsing it.

| Existing storage | Proposed storage | Actual change |
| --- | --- | --- |
| `_atom_site.condition_sequence_1_residue` | `_atom_site.ma_condition_sequence_1_residue` | Prefix and a dictionary definition; same broadcast values |
| `_atom_site.condition_sequence-probability-ALA_1_residue` | `_atom_site.ma_condition_sequence_probability_ALA_1_residue` | Same component-specific column; hyphens become underscores |
| `_condition_coordinate_1_atom.idx0/val0/val1/val2` | `_ma_condition_coordinate_1_atom.idx0/val0/val1/val2` | Same XYZ row; explicit `model_num` |
| InternalCoordinate plus CoordinateGroup | Same named vector table and scalar group column, with `ma_` | Preserve -1/0/positive group convention and define its meaning |
| Chain/base-pair `idx0/idx1/val0` tables | Same tables with `ma_` and `model_num` | Preserve sparse atom-index storage and declared level |
| `atom_site.atom_array_index` | `atom_site.ma_atom_array_index` | Preserve zero-based saved indices; define model scope |

`ma_` follows existing ModelCIF names such as `ma_model_list`; it is not an annotation level. Add it to the annotation item inside `atom_site`, not to the category name `atom_site`. Replace hyphens in native names with underscores to meet the DDL2 name types used by ModelCIF; the mapping is explicit and checked for collisions. The exact [59-entry field catalogue](annotation-coverage.md) includes the special native names `atomize` and `is_unindexed_insertion`.

## Minimal shared context

One `ma_annotation_context` row links the block's annotation snapshot to `ma_data`, declares its authored/effective/derived stage and whether input conditions are required for execution. Existing ModelCIF data groups, protocol steps, software and associated files describe production and consumption. Distinct snapshots use separate blocks/files; multiple coordinate models may share a snapshot through explicit `model_num` values.

```cif
_ma_annotation_context.entry_id annotation_example
_ma_annotation_context.data_id 3
_ma_annotation_context.stage authored
_ma_annotation_context.required_for_execution True

loop_
_ma_condition_coordinate_1_atom.model_num
_ma_condition_coordinate_1_atom.idx0
_ma_condition_coordinate_1_atom.val0
_ma_condition_coordinate_1_atom.val1
_ma_condition_coordinate_1_atom.val2
1 0 0.0 0.0 0.0
1 1 3.8 0.0 0.0
```

The dictionary fixes XYZ order, angstrom units, types and semantics once. These definitions are not repeated in every file. Indices 0/1 resolve to `atom_site.ma_atom_array_index` in model 1; they remain valid when atom rows are reordered. All indexed tables include `model_num`, including single-model files.

## Categories and examples

| New category | Contents |
| --- | --- |
| `ma_annotation_context` | One snapshot and its data/protocol context |
| `ma_condition_coordinate_1_atom` | Absolute XYZ targets |
| `ma_condition_InternalCoordinate_1_atom` | XYZ targets local to positive coordinate groups |
| `ma_condition_ChainCentroid_1_atom` | Saved per-atom centroid targets |
| `ma_annotation_coords_with_insertion_1_atom` | Derived reference XYZ in the output frame |
| `ma_condition_chain_2_chain` | Sparse chain relations carried by atom-index pairs |
| `ma_condition_basepair_2_residue` | Sparse residue-pair conditions carried by atom-index pairs |

The existing `atom_site` category holds the scalar fields and optional `ma_token_id`, `ma_molecule_id`, `ma_token_index` membership/offset fields. Use them when interpreting token/molecule annotations requires those identities. They record the actual grouping instead of requiring another tool to reproduce a private tokenizer.

- [Complete absolute-target example](worked-annotations.cif): ordinary structural records, ModelCIF data/protocol records, broadcast scalar/probability columns and two XYZ target rows.
- [Complete group-local example](extended-annotations.cif): the same layout with CoordinateGroup 7, internal XYZ, token/molecule membership and a sparse chain pair. Group 7 may rotate/translate as a whole; the two examples therefore express different geometric conditions.

## Interoperability rules

Retain existing finite values, boolean True/False, integer enums and documented sentinels. Write inactive floating targets as CIF `.` rather than numeric NaN; `?` means unknown and cannot silently become an inactive condition. Keep explicit zero probabilities. Whole XYZ triples must be finite or wholly unset. A missing annotation is not a claim about values generated by software defaults: effective snapshots materialize the values and grouping actually consumed. The [contract](annotation-module.md) specifies reference lifecycle, broadcasts, omissions, custom definitions and execution checks.

Custom annotations use versioned **DDL2 extension dictionaries**, declared with `_audit_conform`, and retain the same scalar/vector/pair layouts. The dictionary supplies exact field names, types, widths, units, level, masks/defaults and scientific meaning. Unbounded `valN` fields are not automatically valid; each published shape has defined items. Custom names use their own namespace and do not become released ModelCIF names merely by adopting `ma_`. A standardization path through the working group remains available. This uses one CIF/dictionary system without a competing generic value encoding.

## ModelCIF integration and adoption

Reuse the design vocabulary from [issue #31](https://github.com/ihmwg/ModelCIF/issues/31): `backbone generation`, `sequence design`, released spelling `structure validation`, `intermediate backbone`, `intermediate sequence`, and `input structure`. Use `ma_data.content_type = other` with details for annotation artifacts. Use existing distance restraints when semantics match, and existing QA/energy/experimental categories for results; do not write conflicting duplicate representations.

AtomWorks needs a file-name mapping, ModelCIF metadata retention, explicit model/index handling and CIF-null conversion; its registry, arrays, broadcasting and sparse-pair APIs remain. RFD4 needs an export/import adapter that saves effective defaults, tokenization, operational profiles and provenance, and rejects ambiguous legacy values. No neural-network architecture change is implied. Production equivalence must be tested; this PR does not implement those adapters.

Prepared datasets link versioned sources/CCD, recipes, licenses, splits and checksummed manifests through ModelCIF data/software/associated-file records. Benchmark profiles additionally identify effective inputs, budgets, evaluators and all attempts. A mask does not hide identity or coordinates still supplied in the input. The format makes these records exchangeable; it does not by itself establish benchmark fairness or a speed improvement.

The proposal, [DDL2 component](../../../base/mmcif_ma-annotation.dic), field catalogue, complete examples and [validation notes](validation.md) are the review package. Scientific profile definitions and working-group acceptance remain necessary. References: [ModelCIF dictionary/history](https://mmcif.wwpdb.org/dictionaries/mmcif_ma.dic/Index/), [2026 update](https://doi.org/10.1016/j.jmb.2026.169658), [dictionary structure](https://mmcif.wwpdb.org/docs/tutorials/mechanics/pdbx-mmcif-dict-struct.html), [audit_conform](https://mmcif.wwpdb.org/dictionaries/mmcif_pdbx_v50.dic/Categories/audit_conform.html).
