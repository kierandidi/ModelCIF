# Annotated CIF in ModelCIF

Incorporate the annotation data contract used by AtomWorks and RFD4-Proteina into ModelCIF, enabling seamless tool pipelines, meaningful cross-tool benchmarking and exchangeable preprocessed datasets. Preserve familiar subject/value rows and existing Python APIs. Older files may be converted to this single standardized representation.

**Proposed dictionary component 0.2.0 for working-group review.** The extension has 16 categories and 157 items. Both dictionary generators include it; published ModelCIF remains 1.4.9. No released category is removed or redefined. Maintainers determine final names, scientific profiles and release packaging.

## Compact construction

**Definition + shared context + subject/value row.** Atom coordinates remain one atom/XYZ row. Residue probabilities remain one residue/component/value row. Only applicable columns are written. Other annotation forms reuse one value table; grouping, geometry and arrays add metadata only when needed.

The complete examples use the canonical layouts of **this proposed module**; they are not yet released ModelCIF categories. The compact example uses five annotation categories. A file includes additional categories only for the features it needs.

## Comparison with AtomWorks files

AtomWorks already preserves annotation values, including scalars, vectors and sparse pairs. This proposal retains that subject/value model and the intended Python API, while changing the on-disk representation and making its semantics independently interpretable. Conversion involves reference resolution, broadcasting/default rules and declared metadata, as well as new names.

| Aspect | AtomWorks annotated CIF | Proposed ModelCIF representation |
| --- | --- | --- |
| Annotation identity | Python class/registry and a name such as condition_coordinate_1_atom encode meaning, body order and level. | A shared definition gives the scientific key/version, level, body order, type, units and omission rule. |
| Scalar values | One-body scalars are extra atom_site columns; residue/chain values may be broadcast across their atoms. | Typed atom/residue/value rows identify the semantic subject. Collapse broadcast values only after checking agreement. |
| XYZ and pairs | Annotation-specific categories use idx0, optional idx1, and val0, val1, etc. | XYZ stays on one atom row with an explicit frame. Pair endpoints share one value row with definition-level symmetry. |
| References | Zero-based saved indices are tied to atom_site.atom_array_index; the reader remaps them after filtering. | Atom IDs and chain/entity/sequence identities have dictionary links and model/data scope. Edits must remap or invalidate references. |
| Defaults and states | Registered code defines defaults and masks; inactive values may use NaN, sentinels or enums. | Explicit specified/unconstrained/unknown/not_applicable states and scalar defaults; structure-dependent values are materialized. |
| Arrays and groups | The inspected codec writes (N,k) payloads as val columns; grouping and channel interpretation come from registered code. | Shapes, labels, token membership, offsets and frames are declared. General arrays use element rows and can be more verbose. |
| Workflow and design | The annotation codec itself does not declare the proposed set/stage/protocol contract or logical-subject records. | Shared sets link ModelCIF data/models; authored/effective/derived stages, logical positions and realization are explicit. |

Source: the inspected [writer/reader](https://github.com/RosettaCommons/atomworks/blob/d922a471381dd0f8bccaf78c384a6b56b6f8e391/src/atomworks/io/utils/standard_annotations/serialization.py), [name/level definitions](https://github.com/RosettaCommons/atomworks/blob/d922a471381dd0f8bccaf78c384a6b56b6f8e391/src/atomworks/io/utils/standard_annotations/base.py) and [index-preserving structure writer](https://github.com/RosettaCommons/atomworks/blob/d922a471381dd0f8bccaf78c384a6b56b6f8e391/src/atomworks/io/utils/io_utils.py) at AtomWorks `d922a47`; the serializer and base-definition blobs match the public production branch inspected on 15 November 2026. RFD4 registers the 28 probability classes downstream; AtomWorks serializes them through this same codec.

For an absolute coordinate condition, indices 0/1 resolve through `atom_array_index` to atom IDs 10/20. These comparison fragments show the stored XYZ values; the files below provide complete structural, chemical, definition and workflow records.

```cif
loop_
_atom_site.id
_atom_site.atom_array_index
10 0
20 1

loop_
_condition_coordinate_1_atom.idx0
_condition_coordinate_1_atom.val0
_condition_coordinate_1_atom.val1
_condition_coordinate_1_atom.val2
0 0 0 0
1 3.8 0 0
```

The proposed atom rows retain XYZ together and add shared set/definition IDs and a declared frame:

```cif
loop_
_ma_annotation_frame.id
_ma_annotation_frame.kind
_ma_annotation_frame.model_id
M 'model Cartesian' 1

loop_
_ma_annotation_atom.id
_ma_annotation_atom.set_id
_ma_annotation_atom.definition_id
_ma_annotation_atom.atom_id
_ma_annotation_atom.x
_ma_annotation_atom.y
_ma_annotation_atom.z
_ma_annotation_atom.frame_id
1 1 1 10 0 0 0 M
2 1 1 20 3.8 0 0 M
```

Frame M preserves absolute model placement. The complete examples instead use independent rigid frame G to illustrate RFD4-style group-local targets: the equivalent source input also needs its internal-coordinate payload and coordinate-group membership. Changing only M to G would change the condition's meaning. Sequence probabilities similarly move from per-component scalar fields, potentially broadcast per atom, to one residue/component/value row; zero stays explicit and inactive source values need semantic conversion.

## The 16 proposed categories

`ma_` is the namespace prefix used by existing ModelCIF categories, such as `ma_model_list`. In `_ma_annotation_atom.x`, the leading underscore starts a CIF data name, `ma_annotation_atom` names the category and `x` names the item. It is not another annotation level. All proposed category names below have prefix `ma_annotation_`. Keys are dictionary keys, not external array offsets. A/B refer to the complete examples below. The [DDL2 component](../../../base/mmcif_ma-annotation.dic) is authoritative for every item, type and parent link.

| Category | Key | Role | Canonical example |
| --- | --- | --- | --- |
| `ma_annotation_definition` | `id` | Meaning, version, level, body order and type | A/B: 1 = atom coordinate; 2 = residue probability |
| `ma_annotation_set` | `id` | Shared data/model and execution context | A/B: 1 = data 3, model 1, authored conditions |
| `ma_annotation_atom` | `id` | Direct atom and typed or XYZ value | A/B: atom 10 -> (0,0,0), frame G |
| `ma_annotation_residue` | `id` | Direct polymer residue and typed value | A/B: A / entity 1 / residue 1, ALA = 0.7 |
| `ma_annotation_value` | `id` | System, chain, group, logical, pair and default values | B: row 2 relates subjects 1/2; row 8 is a default |
| `ma_annotation_frame` | `id` | Model placement or independent rigid geometry | A/B: G is independent rigid |
| `ma_annotation_group` | `id` | Token, molecule, index or context definition | B: groups 1/2 tokens; 3 molecule; 4 index; 5 context |
| `ma_annotation_group_member` | `group_id, ordinal_id` | Ordered atom, child-group or logical membership | B: (1,1) -> atom 10; (4,1) -> token 1, offset 0 |
| `ma_annotation_subject` | `id` | Reusable reference or logical position/segment | B: subject 3 is a length-one position in context 5 |
| `ma_annotation_relation` | `id` | Topology or protocol-linked source/output mapping | B: row 2 realizes subject 3 as subject 5 in step 1 |
| `ma_annotation_domain` | `id` | Named, versioned closed vocabulary | B: domain 1 = roles; domain 2 = ALA/GLY |
| `ma_annotation_domain_value` | `domain_id, code` | Vocabulary member and meaning/component | B: (1,donor) is a role; (2,ALA) identifies ALA |
| `ma_annotation_array` | `id` | Array identity and omitted-element state | B: array 1 leaves absent elements unknown |
| `ma_annotation_axis` | `id` | Shared or instance-specific dimension | B: axis 2 fixes two columns; axis 3 supplies two rows |
| `ma_annotation_axis_label` | `axis_id, index` | Text, component or Cartesian axis label | B: (2,1) = feature_a; (3,1) = row_a |
| `ma_annotation_array_value` | `array_id, element_index` | Typed element in row-major order | B: elements 1/2 = 0.1/0; 3 inapplicable; 4 unknown |

## Complete examples

- **A — [worked-annotations.cif](worked-annotations.cif):** a complete data block with atoms 10/20, model/data/protocol records, both annotation definitions, required authored set 1, independent frame G, two XYZ targets and ALA=0.7 / GLY=0 at residue A/1. It uses five annotation categories and contains no omitted sections.
- **B — [extended-annotations.cif](extended-annotations.cif):** a separate complete data block exercising all 16 categories. It adds custom domains, explicit tokens/index offsets, a sparse residue pair, scalar default plus explicit override, a labelled 2×2 array, and logical positions/segments with ordered output mapping. It contains no omitted sections.

In B, value row 5 attaches array 1 to token group 1. Definition 9 supplies its rank/type; axis 2 and its feature labels are shared, while axis 3 supplies two row labels. The array is `[[0.1, 0], [not_applicable, unknown]]`: element 4 is absent and inherits the array's omission state. Value row 8 supplies the indexed default YES; row 7 explicitly overrides token 2 to unconstrained. Logical position 3 realizes as output subject 5; segment 4 permits length 0–2 and is deleted in this illustrated step.

The `example:` definitions are illustrative scientific meanings, not approved shared conditions. These files demonstrate representation, not a completed design run. [Interpretation rules](annotation-module.md), [coverage/migration](annotation-coverage.md) and [validation](validation.md) complete the specification.

## Standards integration and adoption

Reuse the protein-design vocabulary introduced by [issue #31](https://github.com/ihmwg/ModelCIF/issues/31): `backbone generation`, `sequence design`, **`structure validation`**, `intermediate backbone`, `intermediate sequence` and `input structure`. The issue originally used “structural validation”; the released spelling is “structure validation”. Reuse ModelCIF data groups, protocols, software, applicable restraints and existing QA/energy/experimental result categories. Annotation artifacts use the current `other` content type with details. DDL2 category/item definitions follow the existing dictionary and the proposal approach in [issue #29](https://github.com/ihmwg/ModelCIF/issues/29).

AtomWorks needs canonical I/O, metadata retention and reference remapping/invalidation after edits. RFD4 needs adapters that record authored/effective inputs and provenance, and verify equivalent model conditioning. A versioned converter can rewrite old files and dataset manifests; ambiguous legacy values must be reported. These production adapters are not implemented by this dictionary PR.

Prepared-data profiles specify sources/CCD versions, recipes, licenses, splits, checksums and tensor axes. Benchmark profiles additionally specify effective inputs, budgets, evaluators and all-attempt accounting. A mask does not hide identity or coordinates still supplied in the structure. Associated resources need standard-defined interpretation and explicit file links; investigation-level metadata should align with InvestigationCIF.

References: [published dictionary/history](https://mmcif.wwpdb.org/dictionaries/mmcif_ma.dic/Index/), [usage guide](../../documentation.md), [Tauriello et al., ModelCIF Update (2026)](https://doi.org/10.1016/j.jmb.2026.169658).
