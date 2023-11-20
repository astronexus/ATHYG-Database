## Augmented Tycho - HYG (AT-HYG)

### Rationale

HIPPARCOS (and thus HYG) is getting a little dated, while Gaia (its ultimate replacement for many purposes) is absolutely gigantic -- 1.3 billion records -- and too large for many general-purpose applications. For this update, I wanted to accomplish the following:

1. Incorporate Gaia results into the old but popular HYG dataset.
2. Use a well-defined modern star catalog as a primary basis for IDs, rather than the somewhat _ad hoc_ system in HYG.
3. Create a larger database than HYG that is suitable for today's faster systems, but still small enough to be manageable in many applications.

I chose the Tycho-2 catalog for this purpose. The Tycho-2 catalog's size (~2.5M records) and coverage (essentially complete to V = 11, mostly complete to V = 11.5) makes it suitable for many current applications. It's large enough to be comprehensive for all but the deepest charts, but small enough to be manageable in fairly simple applications. 

Tycho-2 (released in 2000) is only slightly younger than HIPPARCOS, so the idea was to combine it with the newer Gaia DR3 results (2020-2022) whenever feasible. This gives high-quality distance information for over 2M stars instead of the lower-precision results from HIPPARCOS for 0.1M. Adding Gaia results to Tycho-2 gives an "Augmented" Tycho-2 dataset (AT), which is the basis for most stars in the new catalog.

By merging AT with HYG, the "classical" IDs and names for a large number of Tycho / Gaia stars are also available. The result is AT-HYG. 

### Download Format

#### Full Catalog (data/athyg_v23-*.csv)

The full catalog, even when compressed, is too large for simple hosting in this repository. It is currently split into 2 components, which should be downloaded, uncompressed, and concatenated, e.g. (Linux command for version 2.3):

`cat athyg_v23-1.csv athyg_v23-2.csv > athyg_v23.csv`

The full CSV can then be imported into the database tool of your choosing.

#### Subset files (data/subsets)

Several smaller subset files are available if the full catalog is still too large. One of these, the "HYGLike" subset, is designed to have a similar schema to the HYG Database and be a drop-in replacement for it in many applications. The other subset files are simle extracts from the main AT-HYG that have a lower (brighter) magnitude cutoff for most stars.

See data/subsets/README.md for more details about these files.

### Current Version: 

The current version of AT-HYG is version v2.3 (data/athyg_v23-*.csv.gz). 

#### Changes from version 2.2:

##### Additional radial velocity data for ~ 10K stars
All versions of AT-HYG from v1.1 to v2.3 identified a set of Tycho-2 stars that lacked Gaia DR3 cross-references in the original query to get the references, but which do have Gaia DR2 or DR3 IDs, and hence Gaia parallax data, according to the SIMBAD service of online astronomical object data (https://simbad.cds.unistra.fr/simbad/). This is a group of about 10K stars identified during the AT-HYG v1.x builds as a set of stars desirable to get additional information for, using SIMBAD to get the relevant information. In particular, it consists of the relatively brighter stars in Tycho-2 (down to magnitude 10.0) where this information was missing originally.

From v1.1 to v2.2, this supplementary lookup set included Gaia DR2 and DR3 parallaxes but not proper motions or radial velocities. The absence of proper motions was not considered significant because essentially all Tycho-2 stars have good proper motions from Tycho-2. However, Tycho-2 has no radial velocities, so this subset of stars had consistently incomplete velocity data.

The update in v2.3 works with the exact same set of stars, and adds similar lookups for SIMBAD's current preferred proper motions and radial velocities, inserting them under the same conditions that v1.1 through v2.2 used to insert the SIMBAD Gaia parallax data:

* there was no prior cross-reference between Tycho-2 and Gaia, so the original query contained no Gaia position or velocity data (most common case)
* there was a prior cross-reference, but it did not return Gaia radial velocity data when it was available (very uncommon case)

The update primarily affects stars in the range VT = 7.0 to 10.0, so it has negligible impact on naked-eye stars (from Earth), but has a more noticeable one on stars somewhat dimmer than the usual naked eye limits.

##### Data format changes

To save space in CSV format, the `NONE` value (for data sources when the source contained no usable data) has been changed to `N`.

#### Comparison to HYG

The HYG catalog lists proper motions and radial velocities in the same units as AT-HYG (milliarcsec/year and km/sec respectively), but has the Cartesian velocities vx, vy, and vz in parsecs per year. AT-HYG uses kilometers per second, in part to reduce the space needed for figures in CSV format (the figures in pc/year have 5 or 6 leading zeroes) and in part to make direct comparisons to the radial velocity simple. The conversion factor for converting km/sec to pc/year is 1 km/sec = 1.02269 E-6 pc/year.

Apart from the difference in units for vx, vy, and vz, and a few slightly different field names, AT-HYG 2.0 is almost a drop-in replacement for HYG 3.x for many applications -- in particular, for any application that needs only the stars' catalog IDs, historical IDs (like Henry Draper or traditional names), magnitudes, positions, and velocities. The fields missing from AT-HYG that are present in HYG are:

* Variable star IDs and approximate magnitude ranges
* Explicit identification of components in multiple stars (in terms of a "base ID" representing the primary and a component ID)

### Other Versions:

See version-info.md for more details on previous versions of the catalog.