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

#### Full Catalog (version 2.4 - data/athyg_v24-*.csv)

The full catalog, even when compressed, is too large for simple hosting in this repository. It is currently split into 2 components, which should be downloaded, uncompressed, and concatenated, e.g. (Linux command for version 2.4):

`cat athyg_v24-1.csv athyg_v24-2.csv > athyg_v24.csv`

The full CSV can then be imported into the database tool of your choosing.

#### Subset files (version 2.4 - data/subsets)

Several smaller subset files are available if the full catalog is still too large. One of these, the "HYGLike" subset, is designed to have a similar schema to the HYG Database and be a drop-in replacement for it in many applications. The other subset files are simle extracts from the main AT-HYG that have a lower (brighter) magnitude cutoff for most stars.

See data/subsets/README.md for more details about these files.

### Current Version: 

The current version of AT-HYG is version v2.4 (data/athyg_v24-*.csv.gz). 

#### Changes from version 2.3:

##### Remove problematic parallax / distance data from Tycho-2/Gaia DR3 link table

I identified 5715 stars in the original link table between Tycho-2 and Gaia DR3 data that had Gaia standard errors of measurement on the parallax that exceeded the actual parallax value. Since these figures can't be reliably distinguished from "unmeasurably small parallax / large distance", they have all been converted into empty/NULL values for the distance in this data set, and thus in all derivatives, such as AT-HYG.

#### Comparison to HYG

The HYG catalog lists proper motions and radial velocities in the same units as AT-HYG (milliarcsec/year and km/sec respectively), but has the Cartesian velocities vx, vy, and vz in parsecs per year. AT-HYG uses kilometers per second, in part to reduce the space needed for figures in CSV format (the figures in pc/year have 5 or 6 leading zeroes) and in part to make direct comparisons to the radial velocity simple. The conversion factor for converting km/sec to pc/year is 1 km/sec = 1.02269 E-6 pc/year.

Apart from the difference in units for vx, vy, and vz, and a few slightly different field names, AT-HYG 2.0 is almost a drop-in replacement for HYG 3.x for many applications -- in particular, for any application that needs only the stars' catalog IDs, historical IDs (like Henry Draper or traditional names), magnitudes, positions, and velocities. The fields missing from AT-HYG that are present in HYG are:

* Variable star IDs and approximate magnitude ranges
* Explicit identification of components in multiple stars (in terms of a "base ID" representing the primary and a component ID)

### Other Versions:

See version-info.md for more details on previous versions of the catalog.