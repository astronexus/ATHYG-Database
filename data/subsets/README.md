## AT-HYG Subsets

For some applications, the full AT-HYG catalog may still be too large. I have therefore created several subsets of the catalog for use when the full catalog may be too large. Subsets are based on the current version of AT-HYG and will be replaced when AT-HYG versions change.

### Current Data Subsets:

All of these use the current base version of AT-HYG (v3.0), which in turn used HYG 4.0 for the HYG data properties.

The first two subsets, in addition to the magnitude cutoffs described, also include all nearby stars (to 100.0 light years) regardless of magnitude. The field names and data values are identical to the corresponding full AT-HYG entries. In particular, the `id` field in these subsets is the same as the serial `id` field in the full AT-HYG catalog, so its values will not be consistently consecutive in the subsets.

#### AT-HYG v3.0 to magnitude 11: 871,153 stars

This subset contains all AT-HYG stars to V (for HIP and Gliese) or VT (for Tycho-2) magnitude +11.0.

#### AT-HYG v3.0 to magnitude 10: 330,341 stars

This subset contains all AT-HYG stars to V (for HIP and Gliese) or VT (for Tycho-2) magnitude +10.0. 

#### HYGLike v3.0 (all HYG stars in AT-HYG): 118,971 stars

This subset follows the field name and contents for HYG v3.x and v4.x as closely as possible, for all stars in AT-HYG that have an entry in HYG. The subset is intended as a drop-in replacement for HYG for many applications that use HYG, but with better data. For example, just under 90% of stars in HYG v3.x have Gaia DR3 distance data in AT-HYG v3.0, and thus also in HYGLike v3.0. The only difference in field names is the addition of the `*_src` fields from AT-HYG to identify the sources for each of the various data points. These fields can be dropped if desired in an application using the catalog.

There are a few small differences from both the original HYG and AT-HYG, mostly to handle fields in HYG that are not currently in AT-HYG, as well as to address a difference in design criteria in the two catalogs. HYG prioritized completeness, while AT-HYG prioritized accuracy, especially for 3D positions and velocities. In particular, as a subset of AT-HYG, HYGLike inherits the AT-HYG design criterion of prioritizing accurate position and velocity information for at least one star in every Tycho-2 star system over identifying every last component of every Tycho-2 star system.

* Some Gliese _secondary_ and _tertiary_ stars in HYG are not currently matched to cross-references used to build AT-HYG. They will not be in the HYGLike subset. All Gliese _primary_ stars in HYG are present in AT-HYG, and thus also in HYGLike.
* The HYGLike `id` field is the AT-HYG `id` field rather than the HYG `id` field. 
* For consistency with HYG, the Sun's ('Sol') `id` is 0 as it is in HYG, not 1 as in AT-HYG.
* The HYGLike `comp`, `comp_id`, and `base` fields are pure placeholders, since AT-HYG does not track star multiplicity in the same way as HYG. The placeholder logic treats each star as single (more precisely, as not a component of a multiple star system).
* The HYGLike `lum` field is blank; if desired, calculate stellar luminosity from the absolute magnitude `absmag`. AT-HYG normalized most data to avoid redundancy.
* The HYGLike `var`, `var_min`, and `var_max` fields are blank. AT-HYG may include variable star IDs at some point, but is not currently planned to include variable star magnitude ranges.
