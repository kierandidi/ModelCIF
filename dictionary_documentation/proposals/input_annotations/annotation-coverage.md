# Canonical field catalogue

The proposal preserves the named AtomWorks/RFD4 annotation layout. These 59 entries are a scoped inventory from four source modules, including 28 dynamically generated probability classes; other plugins can register additional fields. The dictionary defines 52 scalar annotation columns and six idx/val payload categories. The remaining entry, distance, maps to existing ModelCIF restraints.

All names below are proposed, not released. For scalar entries the target belongs to the existing atom_site category. Each separate table also has model_num. Types, enum meanings, units and omission rules are in the DDL2 component and [contract](annotation-module.md).

| Native name | Proposed name | Level/body | Storage |
| --- | --- | --- | --- |
| `annotation_expsegmin_1_residue` | `_atom_site.ma_annotation_expsegmin_1_residue` | residue/1 | atom column |
| `annotation_expsegmax_1_residue` | `_atom_site.ma_annotation_expsegmax_1_residue` | residue/1 | atom column |
| `atomize` | `_atom_site.ma_atomize` | atom/1 | atom column |
| `condition_sequence_1_residue` | `_atom_site.ma_condition_sequence_1_residue` | residue/1 | atom column |
| `condition_coordinate_1_atom` | `ma_condition_coordinate_1_atom` | atom/1 | idx/val table |
| `condition_index_1_residue` | `_atom_site.ma_condition_index_1_residue` | residue/1 | atom column |
| `condition_distance_2_atom` | `ma_distance_restraints` | atom/2 | existing restraint |
| `condition_n-terminus_1_residue` | `_atom_site.ma_condition_n_terminus_1_residue` | residue/1 | atom column |
| `condition_c-terminus_1_residue` | `_atom_site.ma_condition_c_terminus_1_residue` | residue/1 | atom column |
| `condition_chain_2_chain` | `ma_condition_chain_2_chain` | chain/2 | idx/val table |
| `condition_hotspot_1_atom` | `_atom_site.ma_condition_hotspot_1_atom` | atom/1 | atom column |
| `condition_rasa-binned_1_atom` | `_atom_site.ma_condition_rasa_binned_1_atom` | atom/1 | atom column |
| `condition_hbond_1_atom` | `_atom_site.ma_condition_hbond_1_atom` | atom/1 | atom column |
| `condition_pistack_1_atom` | `_atom_site.ma_condition_pistack_1_atom` | atom/1 | atom column |
| `condition_pication_1_atom` | `_atom_site.ma_condition_pication_1_atom` | atom/1 | atom column |
| `condition_saltbridge_1_atom` | `_atom_site.ma_condition_saltbridge_1_atom` | atom/1 | atom column |
| `condition_hydrophobic_1_atom` | `_atom_site.ma_condition_hydrophobic_1_atom` | atom/1 | atom column |
| `condition_metal-coord_1_atom` | `_atom_site.ma_condition_metal_coord_1_atom` | atom/1 | atom column |
| `condition_secondary-structure_1_residue` | `_atom_site.ma_condition_secondary_structure_1_residue` | residue/1 | atom column |
| `condition_index-group_1_token` | `_atom_site.ma_condition_index_group_1_token` | token/1 | atom column |
| `condition_cath-label_1_residue` | `_atom_site.ma_condition_cath_label_1_residue` | residue/1 | atom column |
| `condition_InternalCoordinate_1_atom` | `ma_condition_InternalCoordinate_1_atom` | atom/1 | idx/val table |
| `condition_ChainCentroid_1_atom` | `ma_condition_ChainCentroid_1_atom` | atom/1 | idx/val table |
| `condition_CoordinateGroup_1_atom` | `_atom_site.ma_condition_CoordinateGroup_1_atom` | atom/1 | atom column |
| `condition_basepair_2_residue` | `ma_condition_basepair_2_residue` | residue/2 | idx/val table |
| `condition_na-ss-state_1_residue` | `_atom_site.ma_condition_na_ss_state_1_residue` | residue/1 | atom column |
| `annotation_src-coordinate-conditioned-region_1_atom` | `_atom_site.ma_annotation_src_coordinate_conditioned_region_1_atom` | atom/1 | atom column |
| `annotation_src-component_1_atom` | `_atom_site.ma_annotation_src_component_1_atom` | atom/1 | atom column |
| `annotation_src-index-group_1_atom` | `_atom_site.ma_annotation_src_index_group_1_atom` | atom/1 | atom column |
| `is_unindexed_insertion` | `_atom_site.ma_is_unindexed_insertion` | atom/1 | atom column |
| `annotation_coords-with-insertion_1_atom` | `ma_annotation_coords_with_insertion_1_atom` | atom/1 | idx/val table |
| `condition_sequence-probability-ALA_1_residue` | `_atom_site.ma_condition_sequence_probability_ALA_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-ARG_1_residue` | `_atom_site.ma_condition_sequence_probability_ARG_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-ASN_1_residue` | `_atom_site.ma_condition_sequence_probability_ASN_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-ASP_1_residue` | `_atom_site.ma_condition_sequence_probability_ASP_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-CYS_1_residue` | `_atom_site.ma_condition_sequence_probability_CYS_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-GLN_1_residue` | `_atom_site.ma_condition_sequence_probability_GLN_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-GLU_1_residue` | `_atom_site.ma_condition_sequence_probability_GLU_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-GLY_1_residue` | `_atom_site.ma_condition_sequence_probability_GLY_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-HIS_1_residue` | `_atom_site.ma_condition_sequence_probability_HIS_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-ILE_1_residue` | `_atom_site.ma_condition_sequence_probability_ILE_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-LEU_1_residue` | `_atom_site.ma_condition_sequence_probability_LEU_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-LYS_1_residue` | `_atom_site.ma_condition_sequence_probability_LYS_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-MET_1_residue` | `_atom_site.ma_condition_sequence_probability_MET_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-PHE_1_residue` | `_atom_site.ma_condition_sequence_probability_PHE_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-PRO_1_residue` | `_atom_site.ma_condition_sequence_probability_PRO_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-SER_1_residue` | `_atom_site.ma_condition_sequence_probability_SER_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-THR_1_residue` | `_atom_site.ma_condition_sequence_probability_THR_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-TRP_1_residue` | `_atom_site.ma_condition_sequence_probability_TRP_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-TYR_1_residue` | `_atom_site.ma_condition_sequence_probability_TYR_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-VAL_1_residue` | `_atom_site.ma_condition_sequence_probability_VAL_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-A_1_residue` | `_atom_site.ma_condition_sequence_probability_A_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-C_1_residue` | `_atom_site.ma_condition_sequence_probability_C_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-G_1_residue` | `_atom_site.ma_condition_sequence_probability_G_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-U_1_residue` | `_atom_site.ma_condition_sequence_probability_U_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-DA_1_residue` | `_atom_site.ma_condition_sequence_probability_DA_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-DC_1_residue` | `_atom_site.ma_condition_sequence_probability_DC_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-DG_1_residue` | `_atom_site.ma_condition_sequence_probability_DG_1_residue` | residue/1 | atom column |
| `condition_sequence-probability-DT_1_residue` | `_atom_site.ma_condition_sequence_probability_DT_1_residue` | residue/1 | atom column |

## Source and interpretation notes

AtomWorks was inspected at d922a471381dd0f8bccaf78c384a6b56b6f8e391; its serializer/base-definition blobs matched the public production branch checked on 15 November 2026. RFD4 was inspected at 68058693cb88762a8aaf806571e13b291ce6318a. The earlier inventory was rechecked against these source definitions. Values/masks and actual consumer behavior matter more than class names alone.

- [AtomWorks serializer and reader](https://github.com/RosettaCommons/atomworks/blob/d922a471381dd0f8bccaf78c384a6b56b6f8e391/src/atomworks/io/utils/standard_annotations/serialization.py)
- [AtomWorks standard definitions](https://github.com/RosettaCommons/atomworks/blob/d922a471381dd0f8bccaf78c384a6b56b6f8e391/src/atomworks/io/utils/standard_annotations/definitions.py)
- [AtomWorks conditions](https://github.com/RosettaCommons/atomworks/blob/d922a471381dd0f8bccaf78c384a6b56b6f8e391/src/atomworks/ml/conditions/definitions.py)
- [RFD4 conditions and probability family](https://github.com/RosettaCommons/RFD4-Proteina/blob/68058693cb88762a8aaf806571e13b291ce6318a/src/rfproteina/conditions.py)
- [RFD4 derived annotations](https://github.com/RosettaCommons/RFD4-Proteina/blob/68058693cb88762a8aaf806571e13b291ce6318a/src/rfproteina/utils/standard_annotations.py)

The boolean chain mask does not establish that False is an active different-chain constraint. The base-pair enum does not establish that all RFD4 consumers honor negative rows. The source-label fields do not establish global correspondence. RASA, interaction and classification labels need operational profiles. The proposal preserves these values and states their limits rather than inventing missing scientific semantics.

The codec supports scalar and fixed-width vector payloads, including sparse pairs. The API also exposes six levels, defaults and registration; interoperable grouping/default behavior is specified in the contract. Arbitrary tensor ranks, coordinate-free future topology and automatic execution of unknown Python classes are not claimed as implemented by this component.
