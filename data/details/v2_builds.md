## Additional Details for Version 2.x Builds (next main public release, concentrating on Gaia velocity data and Tycho color index data)

This goes pretty deep into some data-quality and data-processing weeds. Feel free to skip if you don't want the full story; just note that issues described here will likely affect later versions of the database.

### Data selection criteria

The main goal for AT-HYG was slightly different from that for HYG. The TL;DR version is "Although both attributes are important to both catalogs, HYG prioritizes _completeness_; AT-HYG prioritizes _accuracy_."

For HYG, it was to get, as completely as possible, all the entries from the 3 source catalogs into one merged data set. Since essentially all stars in YBSC were also in HIPPARCOS, the outliers -- the ones that were mostly not identified elsewhere -- were from Gliese. And an explicit part of compiling HYG was to get as many of those as possible, regardless of brightness or additional data about them.

For AT-HYG, it was to get, as completely as possible, accurate data (preferably from Gaia for distances) for as many stars as possible above a specific magnitude limit, while keeping the data set size manageable. The Tycho-2 limit of V ~ 12 is the result. Stars fainter than that limit are subject to being dropped from AT-HYG if they cannot be matched accurately to Gaia for distances or Tycho-2 or Gaia for positions. 

A related condition is because the treatment of multiple stars varies significantly between catalogs, AT-HYG focuses on getting accurate data for _at least_ one star in any given star system in any catalog, but unlike HYG, it does not attempt to get _all_ of the stars in a system, but instead tries to get the most accurate data it can get for at least one. In practice, this is almost always the brightest or "A" component in a multiple-star system.

### Version-specific notes:

#### Generating the Augmented Tycho (AT) catalog v2.5

The acceptance criteria for the Augmented Tycho (AT) catalog (in any version) are:

1. AT contains _every_ star in the Tycho-2 catalog with valid Tycho-2 visual magnitude data. Only 20 of the 2.5M+ records in Tycho-2 are missing a valid visual magnitude.
2. AT contains _as many stars_ with good quality distance information as possible, with Gaia DR3 values preferred, and Gaia DR2 or HIPPARCOS acceptable if Gaia DR3 is not available.
3. AT contains _accurate cross-reference IDs_ for every star that has an available reference: 
    * Tycho-2 (the base catalog), 
    * Gaia, with DR3 values preferred.
    * Other major catalogs, predominantly HIPPARCOS and Henry Draper.

The version of AT used for AT-HYG v2.5 is AT v2.5. The primary difference between AT 1.x and AT 2.x is the addition of velocity data (proper motions and radial velocities). 

The primary Gaia to Tycho-2 cross-reference query for AT v2.5 is similar to that for all previous AT v2.x datasets, but it now excludes values for parallax, proper motion, and radial velocity that have standard errors of measurement larger than the actually measured value.

The cross-referencing to other catalogs (such as the Henry Draper catalog) followed the same process as for AT v1.x; see the AT v1.x construction notes in details_v1.md for more information.

#### Significant changes to AT v2.x, since the original release:

AT v2.0 added proper motion and radial velocity data from Gaia DR3 to most Tycho-2 stars. Significant changes since the original v2.0 release:

* For AT v2.1, one new field was added:

    * the Tycho BT-VT magnitude difference became the "color index" field for stars with only Tycho-2 magnitudes

* For AT v2.2, one new field was added:

    * the M-K spectral type from the "Tycho-2 Spectral Type Catalog" at https://cdsarc.cds.unistra.fr/viz-bin/cat/III/231. This field was added by a straightforward cross-reference of the Tycho-2 star IDs in the two sources.

* For AT v2.3, two changes were made, one minor, one more substantial:

    * The data source type of `NONE` was changed to `N` to save space.
    * The subset of stars from Tycho-2 without Gaia links (in the magnitude range 4.0 - 10.0) used to add parallax information from SIMBAD in AT v1.1 through v2.2 was expanded to include SIMBAD results for proper motions and radial velocity.

* For AT v2.4, some stars' data was reevaluated:
    * some Tycho-2 stars (5710 out of almost 2.5M, or a bit over 0.2%) were stars whose Gaia DR3 parallax was, based on the standard measurement error, indistinguishable from zero. These stars are retained, but with a distance set to `null` and thus not given any 3D position or velocity values.

* For AT v2.5, some more stars' data was reevaluated:
    * some Tycho-2 stars (166 for proper motion, 116978 for radial velocity, about 4.6% overall) were stars whose Gaia data for these velocities was zero within the standard measurement error. These stars are retained, but the corresponding velocities are set to `null`. Stars with no proper motion did not get any 3D velocities. Stars with no radial velocity have 3D velocities calculated assuming a radial velocity of zero.

As before, AT v2.5 contains _every_ star in the Tycho-2 catalog with valid Tycho-2 visual magnitude data (only 20 stars out of over 2.5 million dropped, because of invalid magnitude values), along with its HIP ID, HD ID, Gaia DR3 ID, and Gaia distance/parallax, proper motions, and radial velocities when these values are known. It also contains computed values for the Cartesian velocity components vx, vy, and vz that can be used directly whenever possible.

#### Generating the merged Augmented Tycho-HYG (AT-HYG) catalog v2.5

This followed an almost identical process to the process described in details_v1.md.

The current starting points for AT-HYG v2.5 are:

* AT v2.5 (described above)
* HYG v3.7.

HYG v3.7 is a minor update to the version used for AT-HYG v1.1 and older builds (HYG v3.6.1). It adds some data for Yale Bright Star Catalog stars that was missing or incorrect in previous versions.

Significant differences in the AT-HYG build from v1.x were:

* v2.0+
    * add a step to check AT for missing proper motion and/or radial velocity data, and substitute values from HYG when known. 
    * If there was a change to the AT velocity data, recalculate the vx, vy, and vz values obtained from the proper motions and radial velocity, and update the AT-HYG catalog with these new values.
* v2.1+ 
    * add the color indexes from HYG when relevant
    * remove the constellation labeling step, now that constellations are correctly assigned for all stars in AT v2.1 as well as recent versions of HYG.
    * add proper motion and radial velocity data from SIMBAD to the subset of Gliese stars that used SIMBAD for parallax lookups.
* v2.2+
    * add spectral types for the stars in AT v2.2 when available, and from HYG when available there but not in AT.
* v2.3 - v2.5
    * no major changes to merging AT results with HYG. The changes from each AT version (AT v2.2 to AT v2.5) filter naturally into the corresponding AT-HYG version.

### Data quality

Data quality issues are largely the same as in details_v1.md, except for issues specific to proper motion and radial velocity data.

AT-HYG v2.5 addresses a data quality issue recorded in all prior AT-HYG releases -- a small number (~ 5%) of stars that should not have been assigned radial velocity based on the Gaia DR3 results, and an even smaller number (only 0.007 %) that should not have been assigned proper motion data based on the Gaia DR3 results

Currently, there are two data quality items present in AT-HYG 2.1 through AT-HYG v2.5 that are not entirely addressed.

#### Still outstanding in v2.5

Two issues/aspects of the data set that are still present in v2.5, and relatively minor by comparison to the previously resolved aspects in the last few minor versions:

1. (v2.1+) There are several thousand HYG stars with only a Gliese ID (no HIP or HR IDs). These can't be linked directly to AT-HYG, so they have been augmented with a SIMBAD query to get a valid Tycho-2 ID cross-reference, and to do additional lookups for parallax, proper motions, and radial velocities. (Note -- this is a separate SIMBAD lookup from the one described above to get Gaia data from stars in Tycho-2 without a previously indexed Tycho-Gaia cross-reference.) 

The parallaxes and proper motions almost always came back from Gaia DR2 or DR3. However, especially for the fainter Gliese stars, the radial velocities could come from all sorts of catalogs, many of which were specialized ones just for a subset of M dwarfs. I did not attempt to break these sources out in detail; any radial velocity for this set of stars that was not a Gaia-sourced RV has an "rv_src" of "OTHER" in AT-HYG. 

2. (v2.0+) In HYG stars that were not augmented with a SIMBAD lookup (i.e. ones with adequate direct cross-references to Tycho-2), radial velocities have a similar issue, but a bit less extreme. For these stars, the main source for RV was a somewhat niche catalog oriented specifically towards collecting radial velocities (Wilson Evans Batten), but some RVs came from Yale, and some others from Gliese. All HYG RVs for records *not* augmented by SIMBAD will fall into one of these three sources. As a result, these radial velocities were given an "rv_src" of "HYG" in AT-HYG. I spot checked a few of the HYG radial velocities in SIMBAD and, in general, they are of acceptable quality, just generally lower precision than ones from Gaia.

### Possible additions in later releases

One possibility is adding variable star IDs, as an extra ID paralleling other historically significant IDs such as Bayer designations. This would add one of the few remaining unused HYG fields to AT-HYG. Since there is a significant difference (~ 0.1 - 0.2 mag) between the V magnitudes used for HYG-sourced magnitudes and the VT magnitudes in Tycho-2, addressing magnitude ranges is currently out of scope for the combined AT-HYG catalog.

Another possibility is trying to break out the proper motion and radial velocity sources used by HYG and by direct SIMBAD lookups more exactly.