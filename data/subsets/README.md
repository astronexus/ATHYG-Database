# AT-HYG Subsets

For some applications, the full AT-HYG catalog may still be too large. I have therefore created several subsets of the catalog for use when the full catalog may be too large. Subsets are based on the current version of AT-HYG and will be replaced when AT-HYG versions change.

## Current Data Subsets:

All of these use the current base version of AT-HYG (v3.2), which in turn used HYG 4.1 for the HYG data properties.

### AT-HYG Data Schema

These all use the same column names and contents as the full AT-HYG database. The field names and data values are identical to the corresponding full AT-HYG entries. In particular, the `id` field in these subsets is the same as the serial `id` field in the full AT-HYG catalog, so its values will not be consistently consecutive in the subsets.

The first two subsets, in addition to the magnitude cutoffs described, also include all nearby stars (to 100.0 light years) regardless of magnitude. 

#### athyg_32_reduced_m11.csv: AT-HYG v3.2 to magnitude 11 (871,139 stars)

This subset contains all AT-HYG stars to V (for HIP and Gliese) or VT (for Tycho-2) magnitude +11.0.

#### athyg_32_reduced_m10.csv: AT-HYG v3.2 to magnitude 10 (330,327 stars)

This subset contains all AT-HYG stars to V (for HIP and Gliese) or VT (for Tycho-2) magnitude +10.0. 

#### athyg_32_classic_ids.csv: AT-HYG v3.2 stars with 'classic' names and IDs (317,175 stars)

This subset contains all AT-HYG stars with a proper name (from the official IAU list), a Bayer Greek letter designation, a Flamsteed number, or a catalog identifier in any of these catalogs: HIPPARCOS, Henry Draper, Yale Bright Star (HR), and Gliese/Jahreiss.

#### athyg_32_hyg_ids.csv: AT-HYG v3.2 stars with HYG IDs (118,971 stars)

This subset contains all AT-HYG stars with at least some data coming from HYG. Specifically, it contains all AT-HYG stars that were initially sourced from HYG (and then possibly augmented with Gaia data later on). It is designed to be a HYG-equivalent in the AT-HYG data schema, for use with AT-HYG-reading applications that want to use the HYG data as closely as possible.

Since this is a subset of AT-HYG, it inherits the data improvements from Gaia that characterize AT-HYG and distinguish it from the original HYG. For example, just under 90% of stars in HYG v3.x have Gaia DR3 distance data in AT-HYG v3.2, and thus also in this subset of AT-HYG data. 

### HYG Data Schema

These use the same column names and (with a few minor exceptions, noted below) the same data values as the HYG catalog (v4.x).

#### hyglike_from_athyg_v32.csv: HYGLike v3.2 (all HYG stars in AT-HYG) (118,971 stars)

This is the same data set as the "AT-HYG stars with HYG IDs" above, but follows the field name and contents for HYG v3.x and v4.x as closely as possible. It is intended as a drop-in replacement for HYG for many applications that use HYG, but with better data. The only difference in field names from HYG v3.x and v4.x is the addition of the `*_src` fields from AT-HYG to identify the sources for each of the various data points. These fields can be dropped if desired in an application using the catalog.

There are a few small differences from both the original HYG and from AT-HYG, mostly to handle fields in HYG that are not currently in AT-HYG, as well as to address a difference in design criteria in the two catalogs. HYG prioritized completeness, while AT-HYG prioritized accuracy, especially for 3D positions and velocities. In particular, as a subset of AT-HYG, HYGLike inherits the AT-HYG design criterion of prioritizing accurate position and velocity information for at least one star in every Tycho-2 star system over identifying every last component of every Tycho-2 star system.

* Some Gliese _secondary_ and _tertiary_ stars in HYG are not currently matched to cross-references used to build AT-HYG. They will not be in the HYGLike subset. All Gliese _primary_ stars in HYG are present in AT-HYG, and thus also in HYGLike.
* The HYGLike `id` field is the AT-HYG `id` field rather than the HYG `id` field. 
* For consistency with HYG, the Sun's ('Sol') `id` is 0 as it is in HYG, not 1 as in AT-HYG.
* The HYGLike `comp`, `comp_id`, and `base` fields are pure placeholders, since AT-HYG does not track star multiplicity in the same way as HYG. The placeholder logic treats each star as single (more precisely, as not a component of a multiple star system).
* The HYGLike `lum` field is blank; if desired, calculate stellar luminosity from the absolute magnitude `absmag`. AT-HYG normalized most data to avoid redundancy.
* The HYGLike `var`, `var_min`, and `var_max` fields are blank. AT-HYG may include variable star IDs at some point, but is not currently planned to include variable star magnitude ranges.
