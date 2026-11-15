# Annotation storage contract

Proposed component 0.3.0. The [DDL2 component](../../../base/mmcif_ma-annotation.dic) defines all proposed items. This contract supplies cross-field rules that types and simple parent links cannot fully express. The field catalogue covers a scoped 59-entry source inventory, not every plugin or a certified production adapter.

## Layout and identity

One-body scalars remain extra `atom_site` columns. One-body fixed-width vectors retain `idx0,val0,...`; two-body annotations retain `idx0,idx1,val0,...`. The separate payload categories include `model_num` even for one model. Four current vector annotations have exactly three numeric XYZ fields. Higher fixed widths require defined extension-dictionary items, with numeric component order (val2 precedes val10). Do not claim arbitrary tensor-shape support from the current two-dimensional codec; a higher-rank extension must declare shape, axis interpretation and flattening order explicitly.

Zero-body extensions use one row with val0 and no idx columns, keyed by model_num for per-model values or entry_id for an entry-wide value. There is no zero-body entry in the inspected 59-entry inventory. The extension guide below gives the dictionary pattern; it is not an additional built-in annotation.

A snapshot has one `ma_annotation_context` row keyed by entry_id. Its data_id identifies an annotation artifact, separately from coordinate/model data. Authored requests, effective consumed values and derived output are separate snapshots/blocks. Existing associated-file/data-group/protocol records link them. A file containing several models explicitly scopes every indexed row to a declared ma_model_list.ordinal_id. All indexed endpoints belong to that model.

`(atom_site.pdbx_PDB_model_num, atom_site.ma_atom_array_index)` uniquely identifies an annotation endpoint. Indices are nonnegative saved identifiers; they need not be contiguous after filtering. They are not atom_site.id or row positions. Consumers remap using the saved column. Filtering may remove corresponding annotation rows; assembly/copy/concatenation assigns distinct model/instance identities and consistent new indices. Missing or ambiguous references are errors. Group-local geometry and token membership must be revalidated after edits; retaining stale metadata is not a valid roundtrip.

## Levels and broadcasts

| Level | Subject identity while retaining atom-index storage |
| --- | --- |
| atom | Model and saved atom index |
| residue | Model and consistent label_asym_id/label_entity_id/label_seq_id for polymers; explicit chemical instance identity for non-polymers |
| token | Model and explicit ma_token_id membership |
| chain | Model and chain instance, normally label_asym_id after assembly expansion |
| molecule | Model and explicit ma_molecule_id membership |
| system | Whole entry or selected model, as declared by the zero-body definition |

Body order and level are independent of the number of member atoms and the value shape. Broadcast values at residue/token/chain/molecule level must agree across the same subject, including their null/sentinel states. Atom-level annotations such as ChainCentroid retain atom-level semantics despite their names. A pair's idx0/idx1 identify atoms that resolve to subjects at the declared level. Reversed symmetric pairs and redundant broadcasts must agree; contradictory values are invalid. Do not collapse rows without this check.

Token and molecule IDs are strings scoped to a model. Every token belongs to one molecule. Where index-group conditioning is present, every member atom supplies its token, molecule and token_index; token offsets agree within a token and are unique within a model/molecule/index-group scope. Offset differences across groups are unspecified. Tokenization is recorded as an effective result and as a versioned preparation method; `atomize` alone is insufficient.

## Values and defaults

Keep native finite numbers, True/False booleans, integer enum codes, <M> for inactive sequence, x.x.x.x for inactive CATH and documented -1 sentinels. These sentinels have item-specific meanings. False is inactive for hotspot, terminus and the inherited chain mask; it is not a universal negative constraint. A literal text period used by source-label fields must be quoted so it is distinct from a CIF null.

For floating target/probability items, `.` means no supplied condition and replaces the source NaN sentinel; `?` means unknown. Unknown required values must be resolved or rejected, not silently interpreted as unconditioned. For other items, the dictionary and normal CIF null meanings apply. NaN/infinity are not finite numeric payloads. Scalar zero and False must never be discarded merely because they are false-like.

Omitting an entire annotation means that the snapshot does not supply it. Do not activate an implicit scientific condition solely because an item is absent. Missing vector/pair rows supply no request. Known constant defaults and structure-dependent default functions are interpreted by a declared execution profile; an effective snapshot writes every consumed value, including True defaults for indexed status and materialized grouping. Sparse pairs need only store effective constraints, with omission semantics defined by their annotation. No executable default function is loaded from a CIF file.

Sequence-probability columns identify components by name, retain explicit zeros and use [0,1] finite values. Specified mass is at most one, complete mass is one, with tolerance 1e-6. Inactive components can share remaining mass without implicit uniformity/renormalization. Unknown components remain unresolved. An exact sequence request fixes one-hot probabilities; conflicts are errors. The 28 named components are not a universal alphabet: additional components require defined extension items and chemical identities. Reference sequence alone is not a requested identity.

## Geometry and existing categories

Coordinate val0/val1/val2 are model Cartesian XYZ in angstroms. InternalCoordinate uses the same row shape but references the positive CoordinateGroup label on each atom. -1 means no coordinate request, 0 selects an absolute target, and positive groups have independent rigid placement. Complete finite triples and matching membership are required. Each positive group may rotate/translate together; its relative geometry remains fixed. Whole-group renaming is harmless; splitting or merging membership is not. If CoordinateGroup is omitted, absolute coordinate rows still have their dictionary-defined meaning; internal coordinates require it.

Centroid/reference coordinates retain the saved values and declared frame semantics. Preserve centering, sampled noise, selected atoms and transformation provenance. A program must not recompute a different target while claiming a lossless conversion.

Native distance targets use existing ma_restraints/ma_distance_restraints records when semantics match. Resolve saved indices to the existing structural identifiers; provide lower/upper limits, atom/residue granularity and restraint provenance. An exact positive distance can use equal bounds only if exact distance is the intended meaning. Do not invent tolerance, loss or a zero-distance request from inactive data. Existing ModelCIF QA, energy and experimental result categories remain authoritative for evaluated results.

Expandable markers retain expsegmin/expsegmax scalar fields and require paired nonnegative bounds with min <= max; -1/-1 is inactive. The marked input residue is an algorithmic segment marker whose realized length may differ, including zero. Its status must be explicit in input-model details; marker coordinates must not be silently treated as physical output coordinates or fixed targets. Retain ordering and the producing software/profile. These fields do not encode arbitrary coordinate-free topology or prove an output correspondence. Such capabilities need their own precise extension rather than being inferred from placeholder coordinates.

## Operational profiles and custom annotations

The dictionary fixes scientific meanings it can define consistently. An execution profile must additionally fix interaction partners/criteria, RASA bins and algorithms, CATH release, secondary-structure assignment/failure handling, tokenization and other model-specific choices. A profile cannot redefine a dictionary item. If these meanings are unavailable, the file can be preserved but required execution must fail explicitly.

Base-pair legacy values need special care: the source enum describes 0 unspecified, 1 paired and 2 unpaired, but an inspected RFD4 path treats every sparse row as positive. Rows carrying 0/2 require an identified interpretation and adapter; do not silently claim equivalent conditioning. Local source-label strings are preserved as labels and are not promoted to globally resolved correspondence without the missing context.

A custom class is exported with a versioned DDL2 dictionary using a distinct namespace. Declare the dictionary name/version/location with audit_conform and distribute the exact definition with prepared datasets. Define every item, type, key, unit, value width, component order, level, symmetry, grouping, transform and omission/default rule needed for independent interpretation. Provide scientific methods/provenance, not only a Python import path. Validators load the combined ModelCIF and declared extension definitions. Preserve unsupported content; reject unsupported required execution. Publishing a custom dictionary is not working-group acceptance or automatic deposition support.

For example, an entry-wide training weight can retain this zero-body layout:

```cif
_example_annotation_sample_weight_0_system.entry_id example_entry
_example_annotation_sample_weight_0_system.val0 0.5
```

Its extension dictionary defines entry_id as the unique key linked to _entry.id, val0 as a finite nonnegative dimensionless real training-example weight, body order zero and system level. It also states whether an absent weight is unspecified or defaults to one. This example is an extension-authoring pattern, not a proposed built-in ModelCIF category.

## Library adoption

Preserve the Python registry, StandardAnnotation/AtomArrayPlus APIs, shapes, broadcast storage and sparse-pair objects. Implement reversible name mapping, CIF null handling, metadata ownership and explicit model/index references. Retain field metadata during read/write/copy/slice/assembly operations. Code that relies on a private default must export the effective values and relevant profile.

RFD4 adapters must compare equivalent effective model inputs, not only matching file bytes. Test absolute and group-local targets, component probabilities, defaults, tokenization, model stacks, cropping/permutation, symmetry copies and legacy ambiguity. Old datasets can be converted once with updated manifests/checksums. No network architecture change is implied; production adapter effort is not measured by this dictionary change. Generic CIF readers can preserve these tags, while python-modelcif needs handlers or an explicit preservation path before claiming annotation-aware roundtrips.
