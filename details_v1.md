## Additional Details

This goes pretty deep into some data-quality and data-processing weeds. Feel free to skip if you don't want the full story; just note that issues described here will likely affect later versions of the database.

### Data selection criteria

The main goal for AT-HYG was slightly different from that for HYG.

For HYG, it was to get, as completely as possible, all the entries from the 3 source catalogs into one merged data set. Since essentially all stars in YBSC were also in HIPPARCOS, the outliers -- the ones that were mostly not identified elsewhere -- were from Gliese. And an explicit part of compiling HYG was to get as many of those as possible, regardless of brightness or additional data about them.

For AT-HYG, it was to get, as completely as possible, accurate data (preferably from GAIA for distances) for as many stars as possible above a specific magnitude limit, while keeping the data set size manageable. The Tycho-2 limit of V ~ 12 is the result. Stars fainter than that limit are subject to being dropped from AT-HYG if they cannot be matched accurately to GAIA for distances or Tycho-2 or GAIA for positions. 

A related condition is because the treatment of multiple stars varies significantly between catalogs, AT-HYG focuses on getting accurate data for _at least_ one star in any given star system in any catalog, but unlike HYG, it does not attempt to get _all_ of the stars in a system, but instead tries to get the most accurate data it can get for at least one. In practice, this is almost always the brightest or "A" component in a multiple-star system.

### Version-specific notes:

#### General

There have been several changes from all of the alpha (v0.x) versions, the main ones being:

* The decision to concentrate on stars that were either single or the primary component of their star systems (usually the "A" component) was made explicit at all stages. In multiple stars, "B" and other fainter components from HYG are subject to being dropped if they cannot be unambiguously associated with a star in Tycho or Gaia.
* There is a more comprehensive procedure to assign HYG stars, especially Gliese stars, without an explicit link to Tycho from HYG via a HYG->HIPPARCOS or HYG->Henry Draper ID reference. 
* A small number of cross-references (for HIP->HD and TYC->HD in particular) were found to be incorrect, leading either to duplicate entries from multiple sources or to valid Tycho entries that were missing their HYG data in the final catalog.

### Data merging

#### Generating the Augmented Tycho (AT) catalog

The acceptance criteria for the Augmented Tycho (AT) catalog are:

1. AT contains _every_ star in the Tycho-2 catalog with valid Tycho-2 visual magnitude data. Only 20 of the 2.5M+ records in Tycho-2 are missing a valid visual magnitude.
2. AT contains _as many stars_ with good quality distance information as possible, with Gaia DR3 values preferred, and Gaia DR2 or HIPPARCOS acceptable if Gaia DR3 is not available.
3. AT contains _accurate cross-reference IDs_ for every star that has an available reference: 
    * Tycho-2 (the base catalog), 
    * Gaia, with DR3 values preferred.
    * Other major catalogs, predominantly HIPPARCOS and Henry Draper.

The bulk of the additional data comes from Gaia. Merging Gaia and Tycho-2 is straightforward:

* The online Gaia query tool at https://gaia.aip.de/query/ gives a query form where it's possible to build and then download a Gaia-Tycho-2 link table, which also contains the Gaia DR3 positions and distances.

The specific query I used was:

`select g."source_id", 
    t.original_ext_source_id, 
    g.ra, 
    g.dec, 
    g.parallax 
from "gaiadr3"."gaia_source_lite" g 
inner join "gaiaedr3"."tycho2tdsc_merge_best_neighbour" t on g."source_id"=t."source_id";
`

* The full Tycho-2 dataset itself is available at https://cdsarc.cds.unistra.fr/viz-bin/cat/I/259#/browse, and again, it's pretty straightforward to pick and choose the fields to include. One of these fields is the HIPPARCOS (HIP) ID for the Tycho-2 star when it exists.
* By a similar process, the Tycho-2 Supplement #1 can be obtained from https://cdsarc.cds.unistra.fr/ftp/cats/I/259/suppl_1.dat.gz. This contains a number of stars that were not included in the main Tycho-2 catalog, but have valid and useful data.
* A Tycho2 to HD link table can be built from data at https://cdsarc.cds.unistra.fr/viz-bin/qcat?J/A+A/386/709. Then it is possible to merge the two Tycho-2 datasets on HIP ID or HD ID, with HIP being preferred when possible. 
* (for versions 1.1+) Finally, a large subset (~10K) of these stars, based on having V magnitude <=10.0 and no Gaia distance after all these cross-references, were looked up in SIMBAD and their Gaia IDs and parallaxes found when possible. 

The end result, after combining the two Tycho-2 datasets and adding the Gaia and HD data, is the Augmented Tycho (AT) catalog. 

AT contains _every_ star in the Tycho-2 catalog with valid Tycho-2 visual magnitude data (only 20 stars out of over 2.5 million dropped, because of invalid magnitude values), along with its HIP ID, HD ID, Gaia DR3 ID, and Gaia distance/parallax when these values are known.

#### Generating the merged Augmented Tycho-HYG (AT-HYG) catalog

Merging this with HYG was a little trickier, especially since HYG contains many stars (mostly ones from Gliese) that had no ID in HYG in common with either Tycho-2 or Gaia. As noted above, I have decided not to try to combine every last Gliese-only star in HYG against AT in the final merge. However, I have attempted to add as many such stars as possible that are consistent with the overall design goal of adding at least one star from every star system in HYG to AT-HYG.

A capsule summary of the AT to HYG merge is here:

1. start with a recent version of AT and HYG.
2. generate a list of all single stars, as well as all primary ("A") stars in HYG, that lacked a HIP ID or an HD ID. These generally came from Gliese. 
3. using SIMBAD, match Gliese IDs to Tycho and Gaia data (DR3 if possible, but DR2 was acceptable if DR3 was missing). The result was a list of Gliese stars with cross-references directly to Tycho-2 and to Gaia, which were not available from other sources.
4. loop through AT, adding HYG data via HIP ID or HD ID.
    * but omit stars whose HIP ID or HD ID had already been seen at least once before in the loop
5. loop though HYG, adding stars from HYG that had a HIP ID or HD ID but weren't already added
    * this addresses a few stars that are in HIP but not the main Tycho-2 catalog
6. loop through HYG, adding stars from HYG that lack HIP or HD IDs (generally from Gliese), by matching a Gliese ID to one in the list prepared in steps 1 and 2, and updating the HYG entry with Gaia distances and Tycho-2 IDs
7. add the Sun as a special case (it doesn't fit any of the above categories)
8. sort the resulting catalog by right ascension.
9. sequentially number the entries and use the sequential numbers as IDs.

The current starting points for version 1.1 (step 1) are:

* AT v1.01
* HYG v3.6.1

AT v1.01 is a minor update to AT v1.0 that adds 7.7K Gaia distances that were not ID'd in v1.0.

HYG v3.6.1 is a more significant update to HYG, which adds constellation names to all stars in HYG as well as fixing a few incorrect labels. The previous version, HYG 3.5, fixed a small number of errors that contributed to some duplicate and missing data in AT-HYG in initial runs. The build of AT v1.0 also addressed some cross-reference errors in AT v0.x. Details are in the "details_v1_errata.md" file.

The next two steps (2 and 3) generate a large number of cross-references for all the Gliese single and "A" component stars identifiable in the HYG database. This list replaces the rather _ad hoc_ approach to Gliese star cross-referencing in the alpha (v0.x) releases, which relied on approaches such as unconditionally adding Gliese stars with proper names (e.g., 'Wolf 359') or adding ones manually identified within a certain distance range in Gaia.

A total of 3,186 Gliese stars, with as much information known about them as possible -- HYG database ID, parallax from SIMBAD, source for parallax (Gaia DR3 for the vast majority), catalog IDs in HIP, Tyc, and Gaia if known -- results from this review.

Step 4 ensures that every star that had a known HIP or HD ID in either catalog (Tycho-2 or HYG) is correctly matched up and the best data used in the final merged catalog. The condition in step 4 largely removes companion stars in Tycho-2 (generally indicated by -2, -3, etc. in the TYC ID) that have the same HIP or HD ID as a star in HYG. There are, relatively speaking, many more resolved companion stars in Tycho-2 than in HIP. In keeping with the overall design principles of AT-HYG, components were generally dropped when they could not be assigned unique HIP or HD IDs. Since the Tycho-2 catalog was sorted by Tycho-2 ID, the end result was almost always the Tycho-2 primary (the ...-1 star) being assigned the HIP or HD ID and the other Tycho-2 entries being left alone.

Step 5 only affects a few stars, generally ones that somehow had a HIP ID with no corresponding TYC ID. It is generally done concurrently with step 6.

Step 6 applies the extra data collected in steps 1 + 2 for Gliese stars in HYG from SIMBAD, which found Gaia parallaxes and Tycho-2 IDs for the large majority of them. Gliese stars processed in step 6 could thus be matched to stars in HYG, their IDs in Tycho-2 (when known), and their distances from Gaia (when known). When no additional data from Tycho-2 or Gaia could be identified, the stars were added as they were found in HYG.

The last few steps ensure that the catalog is in a typical order for astronomical catalogs. The Sun is part of HYG, but since it lacks other IDs, it has to be added as a special case. It has a placeholder right ascension of 0 hours exactly, so it is always the first entry, with a sequential ID of 1.

Note that since different versions of AT-HYG may add or remove stars as a correction, the sequential ID numbers may be version-dependent. Once the catalog is fairly stable to significant changes, entries determined to be included by mistake may simply be deleted without renumbering, to preserve long-term stability of the ID numbers.

An intended design for a given major version (e.g. AT-HYG 1.x vs 2.x) is that IDs are stable across minor versions (e.g. AT-HYG 1.1 or v1.2). The only _allowed_ changes to the sequence within a major version would be to delete stars found to be invalid, without renumbering. This way, a given AT-HYG sequential ID is guaranteed to refer to the same object across a major version.

### Data quality

#### Known Omissions:

In AT-HYG v1.x, all stars from HYG with a HIPPARCOS ID (117951 stars) have been successfully cross-referenced to a star in the final catalog. 

In AT-HYG v1.x, all but 20 stars from HYG with a Henry Draper ID (98813 stars, many of which also have a HIP ID) have been successfully cross-referenced to a star in the final catalog. Of these 20 stars, 3 are known to exist in AT-HYG as Tycho-2 stars (Gamma Lep B, 17 Cyg B, and 37 Cet B), that were not matched to a HYG star, primarily because they are faint secondary ("B") stars not matched by the catalog matching flow. The remaining 17, which are not in AT-HYG, are mostly very close secondary stars that are not necessarily worth breaking out as separate entities. Some lack a Tycho-2 ID. Many are members of extremely close binaries -- separations 2 arcseonds or less -- whose "A" components have a combined system magnitude in HYG and thus also AT-HYG. Disentangling the details of the last few HYG HD multiples is a possible task for a later version of the catalog, but is not planned immediately.

#### Errors in other catalog cross-references

During initial tests of the data merging described previously, as well as all alpha (v0.x) data set development, there were persistently:

1. some duplicate entries. These were stars that had the same HIP and/or HD IDs, but different Tycho and Gaia IDs, in different stages of the merge, and hence treated as different stars.
2. some unmatched entries (mostly from the custom Gliese processing phase). These were stars that were missing an ID needed to link HYG data to AT data.

These discrepancies could largely be traced to various errors in the cross-reference IDs, primarily in the Tycho->HD cross-references, but also a few in the original HYG database and in HIP->HD cross-references.

The errors I found are detailed in the file details_v1_errata.md. Fixing these errors reduced the number of duplicated or missing HYG entries in AT-HYG from approximately 75 entries in the final merges of alpha versions v0.2 or v0.3 to zero erroneous stars with a known HIP ID and 20 stars with a known HD ID failing to be updated with HYG data (all of which are members of multiple star systems with at least 1 other component in AT-HYG that _was_ correctly updated with HYG data).

#### Tycho-2 magnitudes

HYG (all 3 of its source catalogs) use V magnitudes. Tycho-2 uses VT, which is very close to V for whitish stars (BT-VT ~ 0) but gets progressively larger (i.e., dimmer) as B-V or BT-VT increases. The difference is about 0.1 for early K stars like Epsilon Eridani and 0.15 - 0.2 for faint M stars like Proxima Centauri. For _general_ charting purposes the difference is small, but be aware that for a significant number of stars differences between Tycho and HIPPARCOS (etc.) magnitudes are noticeable.

V magnitudes can be approximated from the VT + BT version of color index:

`V = VT - 0.090 * (BT-VT)`

and this information may be included in later versions of the catalog.

#### Tycho-2 positions

All Tycho-2 stars have an _equinox_ of J2000.0. That is, the relationship between the equatorial and ecliptic coordinates is the one existing at J2000. However, not all stars have an _epoch_ of J2000, meaning that there may be small differences in "actual" position due to the stars' proper motion.

Tycho-2 defines a field called "pflag" that identifies important conditions on positions. Most Tycho-2 positions have an empty pflag, meaning that the positions (R.A. and dec) are mean values from multiple observations that have been corrected to epoch J2000. 

Approximately 4% of stars in Tycho-2 have a pflag of "X", meaning that the position is not a mean of several measurements, _and_ the epoch is the mission base epoch of J1991.5 instead of J2000. These are most often multiple stars, but there are a few singles in the mix.

All HYG positions have been adjusted to epoch + equinox J2000.0. As a result, to avoid inconsistencies in some Tycho-2 positions, stars with a pflag of X in Tycho and a valid HIP match have had their positions replaced with HIP positions. This is the basis of the "HIP_X" position source indicator.

To clean up HYG, I applied an appropriate number of years of proper motion data to the HIPPARCOS data to bring it to epoch 2000.0. I didn't do this _directly_ with Tycho-2 pflag "X" stars because pflag = "X" also means no Tycho-2 proper motions. One of the likely improvements for v1.x or v2.x of the catalog is to use GAIA proper motions, when available, to adjust Tycho-2 stars with pflag = "X". 

#### Possible additions in later releases

Here are some items that are possible additions:

1. Add proper motion data from GAIA.
2. Add radial velocity values (and so also full 3D space velocities) from GAIA.
3. Add BT (blue) magnitudes to Tycho's VT, giving us color index (on the BT and VT values) and also possibly an estimate of V magnitudes for Tycho stars, making magnitude values more consistent
4. Add spectral types directly from HD stars to ones that don't have them in HIP.
5. (done in v1.1) Add 3-letter constellation labels for non-HYG entries.

