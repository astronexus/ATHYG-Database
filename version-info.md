## AT-HYG Version Details

### Version-Specific Changes

The current version is v3.2. See the main README.md file for a description of this version's changes.

#### Changes from version v3.0 to v3.1:
#### Incorporate changes from HYG v4.0 to v4.1

The update for v3.1 adds 11 new proper names for stars that correspond directly to 11 new proper names in HYG v4.1, specifically to components of double or multiple stars
where a specific proper name for the "B" component was desired. No other data has been changed.

#### Changes from version v2.6 to v3.0:
##### Change version of HYG used from v3.8 to v4.0

The update for v3.0 adds 10 new proper names for stars that correspond directly to 10 new proper names in HYG v4.0. No other data has been changed.

##### Change license to CC BY-SA-4.0

This is the main motivation for a major version update. HYG v4.0 is licensed under the newer CC BY-SA-4.0 license stated above, and so newer versions of AT-HYG (3.0 and up) will also use the same license. Versions prior to v3.0 will continue to have the CC BY-SA-2.5 license.

#### Changes from version v2.5 to v2.6:
##### Change version of HYG used from v3.7 to v3.8

The update for v2.6 adds 78 new proper names for stars that correspond directly to 78 new proper names in HYG v3.8. No other data has been changed.

#### Changes from version v2.4 to v2.5:
##### Remove problematic proper motion and radial velocity distance data from Tycho-2/Gaia DR3 link table

The update for v2.5 is similar to the change for the previous version (v2.4). For the Augmented Tycho (AT) source catalog, I identified stars in the original link table between Tycho-2 and Gaia DR3 data that had Gaia standard errors of measurement on either the proper motion or the radial velocity that exceeded the actual value. Since these figures can't be reliably distinguished from "unmeasurably small proper motion or radial velocity", they have all been converted into empty/NULL values for the corresponding velocity in this data set, and thus in all derivatives, such as AT-HYG.

The number of proper motions that were invalidated is quite low: 166 out of 2.5M stars. This is in part because I was only concerned about total error, not per-component error (i.e., it was fine for the error in the R.A. component to exceed the actual component, so long as the total error for the measurement was still less than the total proper motion overall). Problematic radial velocities were more common: 116,978 stars total. As with other data sources, proper motions from Tycho-2 and radial velocities from one of the HYG sources could be used in place of the missing Gaia results, but these are extremely uncommon (only a tiny number of Gaia proper motions were invalidated, and relatively few HYG stars compared to AT stars had radial velocity data of any quality). 

#### Changes from version v2.3 to v2.4:
##### Remove problematic parallax / distance data from Tycho-2/Gaia DR3 link table

I identified 5715 stars in the original link table between Tycho-2 and Gaia DR3 data that had Gaia standard errors of measurement on the parallax that exceeded the actual parallax value. Since these figures can't be reliably distinguished from "unmeasurably small parallax / large distance", they have all been converted into empty/NULL values for the distance in this data set, and thus in all derivatives, such as AT-HYG.

#### Changes from version v2.2 to v2.3:
##### Additional radial velocity data for ~ 10K stars
All versions of AT-HYG after v1.0 identified a set of about 10K Tycho-2 stars that lacked Gaia DR3 cross-references in the original query to get the references, but which do have Gaia DR2 or DR3 IDs, and hence Gaia parallax data, according to the SIMBAD service of online astronomical object data (https://simbad.cds.unistra.fr/simbad/). 

This group of stars was identified during the AT-HYG v1.x builds as a set of stars desirable to get additional information for, using SIMBAD to get the relevant information. In particular, it consists of the relatively brighter stars in Tycho-2 (down to magnitude 10.0) where this information was missing originally.

From v1.1 to v2.2, this supplementary lookup set included Gaia DR2 and DR3 parallaxes but not proper motions or radial velocities. The absence of proper motions was not considered significant because essentially all Tycho-2 stars have good proper motions from Tycho-2. However, Tycho-2 has no radial velocities, so this subset of stars had consistently incomplete velocity data.

The update in v2.3 works with the exact same set of stars, and adds similar lookups for SIMBAD's current preferred proper motions and radial velocities, inserting them under the same conditions that v1.1 through v2.2 used to insert the SIMBAD Gaia parallax data:

* there was no prior cross-reference between Tycho-2 and Gaia, so the original query contained no Gaia position or velocity data (most common case)
* there was a prior cross-reference, but it did not return Gaia radial velocity data when it was available (very uncommon case)

The update primarily affects stars in the range VT = 7.0 to 10.0, so it has negligible impact on naked-eye stars (from Earth), but has a more noticeable one on stars somewhat dimmer than the usual naked eye limits.

##### Data format changes

To save space in CSV format, the `NONE` value (for data sources when the source contained no usable data) has been changed to `N`.

#### Changes from v2.1 to v2.2
##### Spectral types for Tycho-2 stars

Spectral types for 351,864 stars in Tycho-2 were obtained from the catalog at https://cdsarc.cds.unistra.fr/viz-bin/cat/III/231 (original reference: "The Tycho-2 Spectral Type Catalog", Candace O. Wright et al 2003 AJ 125 359). These follow the usual MK spectral type categories. The update was a fairly straightforward addition to the Augmented Tycho catalog.

When AT-HYG v2.2 was built, it used these spectral types in order of priority:

* This catalog ("The Tycho-2 Spectral Type Catalog"): `spect_src` = "T"
* The HYG catalog's original sources, predominantly the original HD catalog: `spect_src` = "HYG"
* No value: `spect_src` = "NONE" or "N"


#### Changes from v2.0 to v2.1
##### Color index ("ci") field

Version 2.1 adds a color index ("CI") field to the database. The color index comes either from Tycho-2 data ("mag_src" field = "T") or from HYG ("mag_src" field predominantly "HIP", a few "GJ" [Gliese] values). 

The main difference between the Tycho-2 data and the other sources is Tycho-2 used slightly different magnitude measurements. HIPPARCOS and the older HYG sources used the common 20th-century "B" and "V" filter types for their measurements. Tycho-2 used "BT" and "VT", which are close to, but not exactly the same as, B and V respectively. The "ci" field for the "T" magnitue source is therefore BT-VT rather than B-V.

For stars with either color index (B-V or BT-VT) close to 0, the differences are minimal, but for stars where VT is significantly smaller (i.e., brighter) than BT, the Tycho-2 BT-VT is noticeably larger than B-V. It is about 0.1 units higher for Sunlike (main sequence, spectral type G) stars and 0.25-0.3 for main-sequence M stars. 

##### Additional data from SIMBAD lookups

In previous versions of AT-HYG, a subset of HYG stars with Gliese IDs were run through data lookups in SIMBAD to get additional HYG-Tycho 2 cross-references and also improved parallax values from Gaia-DR2 or -DR3. This process was expanded to include proper motions and radial velocities in AT-HYG v2.1, adding a number of velocities that were missing or lower-quality in AT-HYG v2.0.

##### An (as in, one) incorrect radial velocity from SIMBAD

The star GJ 2077, in the initial release of v2.1, had an incorrect radial velocity as a result of a SIMBAD lookup returning a strange value. 

GJ 2077 has an RV of -140.0 km/s in the Gliese catalog, but SIMBAD returned a value of z = 2.188 (~0.8 c!) because of what seems to be a case of mistaken identity: a relatively recent publication (https://www.aanda.org/articles/aa/pdf/2010/10/aa14188-10.pdf) created a catalog of QSOs and classified it as a quasar with that redshift, instead of a white dwarf star. Shortly after originally releasing v2.1, I found the discrepancy and recalculated GJ 2077's Cartesian velocities with the older Gliese RV of -140.0 km/s and reuploaded. This did not seem to merit a distinct version of the catalog, so I left it as v2.1.

I spot-checked a few other large RVs and did not see anything else remotely as discrepant, so this version should be stable until the next significant change to types of data included in the catalog.

#### Changes from v1.1 to v2.0

Version 2.0 adds 3D velocity data to the data in version 1.1.

The new fields are:

* `rv`: The radial velocity in km/sec.
* `rv_src`: The source for the radial velocity.
* `pm_ra`, `pm_dec`: Proper motion in right ascension and declination respectively, in milliarcseconds per year. `pm_ra` has already been adjusted by cos(declination) to convert to true milliarcseconds.
* `pm_src`: The source for the proper motion.
* `vx`,`vy`,`vz`: The Cartesian coordinates for the velocity, in km/sec. The coordinate system is the same as for `x0`,`y0`,`z0`.

The source field ("*_src")values are described in more detail below, under "Source Indicator meanings".

#### Changes from v1.0 to v1.1

No new stars were added, but two significant changes to data were made:

1. Improvements to Gaia distance data. Approximately 10K stars were identified in SIMBAD that lacked Gaia DR3 distances in AT-HYG 1.0 and were relatively bright (V < 10.0). 
These stars were assigned either a Gaia DR2 or DR3 parallax when found in SIMBAD, with DR3 being preferred.
Of these, 7726 got new Gaia DR3 values and 813 got Gaia DR2 values.
2. Constellation IDs are now available for all 2.4M+ stars. They were added by the same process that updated HYG v3.6+ to high-accuracy constellation IDs. The HYG
constellations were taken as canonical, so only non-HYG stars were updated.

The first change also led to a change in the `dist_src` field. For Gaia distances, this can be either `GAIA_DR2` or `GAIA_DR3` depending on which Gaia data release
was used.  Gaia DR2 data was used only for stars that did not have a valid Gaia DR3 value for parallax (in this release, 813 stars)


#### Previous Versions: v0.x

Versions v0.x were public alpha releases. Version v0.1 was mostly to make sure data processing was working as intended. It was not publicly released.

Versions v0.2 and v0.3 have the same field structure as v1.0, but significantly changed the way many of the HYG stars, especially from Gliese, were merged. 

These versions are deprecated and should be replaced with v1.x or v2.x.

### Data Formats
#### Version 1.x Data Format

Version 1.x has the following data fields:

1. `id`: A numeric ID for each star, after sorting all entries by right ascension.
2. `tyc`: The Tycho-2 ID, with leading zeros removed from the first and second portion (for consistency with Gaia linking tables)
3. `gaia`: The Gaia Data Release 3 ID.
4. `hyg`: The HYG main catalog ID from HYG v3.
5. `hip`: The HIPPARCOS ID, from HYG if known, otherwise Tycho-2.
6. `hd`: The Henry Draper (HD) catalog ID, from HYG if known, otherwise Tycho-2.
7. `hr`: The Harvard / Yale Bright Star Catalog ID, from HYG.
8. `gl`: The Gliese ID, from HYG
9. `bayer`: The Bayer (Greek letter) designation, from HYG
10. `flam`: The Flamsteed number, from HYG
11. `con`: The three-letter constellation abbreviation, from HYG
12. `proper`: A proper name for the star, from HYG
13. `ra`: Right ascension (epoch + equinox 2000.0), in hours, from HYG or TYC
14. `dec`: Declination (epoch + equinox 2000.0), in degrees, from HYG or TYC
15. `pos_src`: Indicator of source for the position fields `ra` and `dec` (see below)
16. `dist`: Distance from Sol in parsecs. From Gaia if known, otherwise HYG.
17. `x0`
18. `y0`
19. `z0`: These three fields are Cartesian coordinates. The directions are such that x is towards RA 0, Dec 0, y towards RA 6 hr., Dec 0, and z towards Dec 90 degrees.
20. `dist_src`: Indicator of source for the distance fields `dist`, `x0`, `y0`, `z0` (see below). `x0`, `y0`, and `z0` also depend on `ra` and `dec`, so they will also be determined by the position source. An extremely common combination is raw distance from Gaia but the position from TYC.
21. `mag`: V or VT magnitude for the star
22. `absmag`: Corresponding absolute magnitude
23. `mag_src`: Indicator of source for the magnitude field `mag` (see below). `absmag` depends on both apparent magnitude and distance, so may be determined by values from two sources.

Since different versions of the DB may add or remove records, the `id` value is necessarily associated with a specific version. 

#### Version 2.x Data Format

The fields for Version 1 are present unchanged, but with the following new fields:

24. `rv`: Radial velocity in km/sec.
25. `rv_src`: Indicator of source for the radial velocity
26. `pm_ra`: Proper motion in right ascension, milliarcseconds/year (aleady multiplied by cos(dec))
27. `pm_dec`: Proper motion in declination, milliarcseconds/year
28. `pm_src`: Indicator of source for the proper motions
29. `vx`:  
30. `vy`:  
31. `vz`: These three fields are Cartesian velocity values, using the same coordinate system described for the `x0`,`y0`, and `z0` fields above.
32. `spect`: MK spectral type (v2.2+)
33. `spect_src`: Source for the spectral type (v2.2+)

Additionally, in versions 2.1 and up, the following added field is present:

34. `ci`: the color index (BT-VT for Tycho-2 stars, B-V for others). This field is inserted after the `absmag` field, to keep magnitude-related data grouped together.


### Source Data And Assembly:

The data sources for version 1.1 and later versions are:

- The full Tycho-2 catalog available from https://cdsarc.cds.unistra.fr/viz-bin/cat/I/259#/browse .
- The first Tycho-2 Supplemental catalog, available from https://cdsarc.cds.unistra.fr/ftp/cats/I/259/suppl_1.dat.gz.
- A link table catalog between Tycho-2 and Henry Draper, generated from the catalog at https://cdsarc.cds.unistra.fr/viz-bin/qcat?J/A+A/386/709.
- A link table catalog between Tycho-2 and Gaia DR3. I used the query facility at https://gaia.aip.de/query/. Details of the specific query are in the details_v1.md file.
- A collection of relatively bright stars (V < 10.0) that were missing valid Gaia distances after the first 4 catalogs above were linked. These were queried in SIMBAD and Gaia information added for ones that had a valid Gaia parallax reference.
- HYG v3.6.1 (for v1.1) or HYG v..7 (for v2.x), from this site. 
- A collection of data for Gliese IDs from SIMBAD (https://simbad.cds.unistra.fr/simbad/) that linked Gliese IDs to Tycho-2 and Gaia DR3 IDs when no other linking information was available. The list of Gliese IDs was taken directly from the HYG catalog and matched to other catalogs via automated queries to SIMBAD.

The first five catalogs, after correcting a few cross-reference errors, consitute the Augmented Tycho or AT catalog. This catalog contains every valid (both position and magnitude are present and within reasonable bounds) Tycho-2 star from the main and first supplemental Tycho-2 catalogs, along with the cross-reference IDs to Henry Draper and Gaia and, when available, Gaia DR2 or DR3 distances.

Linking the AT catalog to HYG via the HIPPARCOS and Henry Draper IDs in AT gives most HYG cross-references, which add HYG data such as proper name, Bayer and Flamsteed IDs, and spectral type to the catalog for the linked stars.

Finally, most unlinked HYG records (no HIPPARCOS or Henry Draper IDs) can be linked via the Gliese ID and a suitable Tycho-2 or Gaia DR3 ID found via SIMBAD. The list of Gliese IDs included all single and "A" multiple star components from HYG, as well as a few selected secondary or tertiary stars when they were well-separated and well-characterized in all catalogs.

The end result, after fixing a few more cross-reference errors and validating subsets of the data against other lists -- e.g., the Wikipedia article on nearest stars, https://en.wikipedia.org/wiki/List_of_nearest_stars_and_brown_dwarfs -- is the AT-HYG catalog.

### Additional Details

Information about how this data set was generated are in the "details" files:

- `details_v0.md`: Information about the alpha (v0.x) releases, which covers some of the core aspects of the data processing used in later releases
- `details_v1.md`: information about the first (v1.x) releases
- `details_v1_errata.md`: Information about various source catalog errors found, and corrected in, v1.0
- `details_v2.md`: information about the current (v2.x) releases

A few of the more important details are described here:

#### Details on velocity calculations in v2.x

The vast majority of proper motion data came from Gaia DR3, with HIP and Tycho-2 providing a few values, especially for stars too bright to have been measured by Gaia. A small number of HYG stars only in the Gliese catalog kept their proper motion values from that catalog.

Cartesian velocities were not calculated if either the distance or the proper motions were missing or otherwise unknown.

A large majority of radial velocities came from Gaia DR3, but about 20% of Tycho-2 stars lack a reliable Gaia radial velocity. As with proper motions, this includes bright stars in HYG that were too bright for Gaia to measure accurately, and inherited their radial velocity from HYG. As noted, this HYG radial velocity comes from various sources of generally acceptable (bthough lower precision than Gaia) radial velocity values.

When the radial velocity was missing or equal to zero, the Cartesian velocities were still calculated, but assuming no radial component. This makes those particular values accurate only for short time frames.


#### Source Indicator meanings:

Source indicator fields ("*_src") identify the data source used for a value. The indicator fields apply to fields for the following data:

- Distances
- Positions (equatorial coordinates from Earth)
- Apparent Magnitudes

Note that some fields have dependencies on multiple sources: for example, the Cartesian coordinates depend on both the position and distance, and absolute magnitudes depend on distance and (apparent) magnitudes.

- `TYC`: Fields are from Tycho-2. [Positions, magnitudes, proper motions]
- `T`: Short form of `TYC` used in v2.0+.
- `GAIA`: Fields are from Gaia. [Distances only, v1.0]
- `GAIA_DR2`, `GAIA_DR3`: Fields from Gaia and the specified data release (DR2 or DR3) [Distances, proper motions, radial velocities, v1.1+]
- `G_R2`, `G_R3`: Short version of Gaia DR sources used in v2.0+.
- `HIP`: Fields are from HIPPARCOS. [Positions, distances, magnitudes, proper motions]
- `HIP_X`: Fields are from HIPPARCOS overriding a known Tycho-2 value. This is only for the special case where the Tycho-2 star position was a "non-mean" position and hence not having the same epoch as the rest of the data. [Positions only]
- `GLIESE`: Fields are from Gliese. [Positions, distances, magnitudes, proper motions, radial velocities]
- `GJ`: Short form of `GLIESE` used in v2.0+.
- `OTHER`: Fields are from another source. Currently applies only to the special entry for Sol. [Positions, distances, or magnitudes]
- `NONE`: No valid source of this data [Distances, proper motions, radial velocities]
- `N`: Short form of `NONE` used in v2.3+. 

#### Data By Source Types

Here are the numbers of stars for each combination of possible source types in v1.0. The values for later versions are similar (most previous `GAIA` distances became `G_R3`, and a few thousand missing / `NONE` distances became `G_R2` or `G_R3`). 

The most common entry in AT-HYG has a position and brightness (apparent magnitude) from Tycho-2 and a distance from Gaia. 
All told, the very large majority of stars have a Gaia DR3 distance, including about 90% of HIPPARCOS entries (some of which are too bright to have good Gaia DR3 data at the moment):


|Position|Distance|Magnitude|Total Count
|--------|--------|---------|-----
TYC      | GAIA   | TYC     | 2367088
TYC      | GAIA   | HIP     | 104668
TYC      | NONE   | TYC     | 66116
TYC      | HIP    | HIP     | 11897
GLIESE   | GAIA   | GLIESE  | 952
HIP_X    | GAIA   | HIP     | 694
TYC      | NONE   | HIP     | 512
HIP_X    | HIP    |HIP      | 196
GLIESE   | GLIESE | GLIESE  | 30
TYC      | GLIESE | HIP     | 9
HIP_X    | NONE   | HIP     | 3
OTHER    | OTHER  | OTHER   | 1