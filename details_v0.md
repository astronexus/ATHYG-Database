## Additional Details

This goes pretty deep into some data-quality and data-processing weeds. Feel free to skip if you don't want the full story; just note that issues described here will likely affect later versions of the database.

### Data selection criteria

The main goal for AT-HYG was slightly different from that for HYG.

For HYG, it was to get, as completely as possible, all the entries from the 3 source catalogs into one merged data set. Since essentially all stars in YBSC were also in HIPPARCOS, the outliers -- the ones that were mostly not identified elsewhere -- were from Gliese. And an explicit part of compiling HYG was to get as many of those as possible, regardless of brightness.

For AT-HYG, it was to get, as completely as possible, accurate data (preferably from GAIA for distances) for as many stars as possible above a specific magnitude limit, while keeping the data set size manageable. The Tycho-2 limit of V ~ 12 is the result. Stars fainter than that limit are subject to being dropped from AT-HYG if they cannot be matched accurately to GAIA for distances or Tycho-2 or GAIA for positions.

### Version-specific notes:

#### v0.3

This version made the following changes wrt. v0.2:

* Adding several thousand stars from the Tycho-2 Supplement #1
* Adding a number of stars that had a valid GAIA ID (and distance) but could not be matched directly via HIP, HD, or Gliese ID to GAIA and Tycho-2. These total 60 stars with a distance under 10 pc (32.6 light years).
* A small number of stars were either removed entirely, or made available to GAIA matching (and hence better distance values) after some small changes to the HYG v3.x catalog. See the HYG section for more details.
* A more conscious decision to not attempt to ID higher-level components (e.g., Gliese "B" or "C") if they are not easily matched with existing data. This is in part to avoid duplicates between stars that are clearly outlined as multiple in Gliese and ones whose Tycho or GAIA entries cannot be easily matched to a Gliese entry (e.g., because it was too dim to be in HIP or HD). Because of this, many fewer Gliese "B", "C", etc. stars in HYG are labeled as such in AT-HYG, although they often will exist without such a label if Tycho-2 or GAIA included them, and a close examination of the stars very close to the "A" star in R.A. and Dec will reveal these unlabeled components.
#### v0.2

This is the first public release. Details of its construction are outlined below.
### Data merging

Merging GAIA and Tycho-2 is straightforward:

* The online GAIA query tool at https://gaia.aip.de/query/ gives a query form where it's possible to build and then download a GAIA-Tycho-2 link table, which also contains the GAIA positions and distances.
* The full Tycho-2 dataset itself is available at https://cdsarc.cds.unistra.fr/viz-bin/cat/I/259#/browse, and again, it's pretty straightforward to pick and choose the fields to include.
* By a similar process, the Tycho-2 Supplement #1 can be obtained from https://cdsarc.cds.unistra.fr/ftp/cats/I/259/suppl_1.dat.gz . This contains a number of stars that were not included in the main Tycho-2 catalog, but have valid and useful data.
* A Tycho2 to HD link table can be built from data at https://cdsarc.cds.unistra.fr/viz-bin/qcat?J/A+A/386/709. Then it was possible to merge on HIP ID or HD ID, with HIP being preferred when possible. 

The end result, after combining appropriate subsets of these 4 data sources, is the Augmented Tycho (AT) catalog, which is every star in the Tycho-2 catalog(excluding a _very_ small number -- 20 entries -- without a valid visual magnitude), combined with GAIA and HD IDs plus GAIA distances/parallaxes.

Merging this with HYG was a little trickier, especially since HYG contains many stars (mostly ones from Gliese) that had no ID in common with either Tycho-2 or GAIA. As noted above, I have decided not to try to combine every last Gliese-only star in HYG against AT in the final merge. This means that, in practice, _most_ Gliese stars fainter than V ~ 12 will not be present, unless they also have a HIP or HD ID (uncommon), or they fall into certain categories (in more detail below). A capsule summary of the AT to HYG merge is here:

1. loop through AT, adding HYG data via HIP ID or HD ID.
    * but omit stars whose HIP ID or HD ID had already been seen at least once before
2. loop though HYG, adding stars from HYG that had a HIP ID or HD ID but weren't already added
    * this addresses a few hundred stars that were in HIP but not the main Tycho-2 catalog
3. loop through HYG, examining the remaining stars that lacked HIP or HD IDs (generally from Gliese), and unconditionally adding ones (i.e., simply appending them to the list of data records) that met the following criteria:
    * had a proper name in HYG, _or_
    * were not marked as being a companion star to a known HIP or HD star (e.g. through their shared Gliese IDs)

The rationale for the criteria in 1. was to match _primary_ stars in Tycho multiples to _primary or combined_ stars in HYG multiples. This is because many stars that are clearly separate in Tycho-2 (and GAIA) are not distinct in HIPPARCOS. Except for a few very wide doubles like Alpha Centauri and 61 Cygni, HIPPARCOS entries for double stars usually apply to the system as a whole and include a system-total magnitude. So long as the Tycho catalog is sorted in numerical order, duplicate HIP matches for secondary and other components (which have a -2, -3, etc at the end of their Tycho-2 ID) can be dropped automatically simply by making sure only one HIP ID match to Tycho-2 is made per HIP ID.

The rationales for the criteria in 3. were:

* "proper name": a small number of stars have only Gliese IDs, but also a proper name, from the IAU Working Group for Star Names or a similar source, and belong in AT-HYG in any event. This is especially true for some famous nearby stars like Wolf 359.
* "not companions": as noted, Tycho separated many stars that were not counted in HIPPARCOS as separate stars. So if their companions _do_ exist in HYG, they have a Gliese ID only and can't be matched to a corresponding Tycho entry straightforwardly. Omitting stars from HYG that were companions without HIP or HD IDs was necessary to avoid lots of duplicates. In practice, this means a significant number of HYG stars exist in Tycho-2, and will have entirely accurate positions, magnitudes, and (if also in GAIA) distances, but won't be linked to other information the way they would be if they could be linked via IDs in HYG.

A long-term project for v1.x (or later) will be to refine this process a bit more and make it a bit more transparent.

### Tycho-2 magnitudes

HYG (all 3 of its source catalogs) use V magnitudes. Tycho-2 uses VT, which is very close to V for whitish stars (BT-VT ~ 0) but gets progressively larger (i.e., dimmer) as B-V or BT-VT increases. The difference is about 0.1 for early K stars like Epsilon Eridani and 0.15 - 0.2 for faint M stars like Proxima Centauri. For _general_ charting purposes the difference is small, but be aware that for a significant number of stars differences between Tycho and HIPPARCOS (etc.) magnitudes are noticeable.

V magnitudes can be approximated from the VT + BT version of color index:

`V = VT - 0.090 * (BT-VT)`

and this information may be included in later versions of the catalog.

### Tycho-2 positions

Tycho-2 defines a field called "pflag" that identifies important conditions on positions. Most Tycho-2 positions have an empty pflag, meaning that the positions (R.A. and dec) are mean values from multiple observations that have been corrected to epoch 2000. 

Approximately 4% of stars in Tycho-2 have a pflag of "X", meaning that the position is not a mean of several measurements, _and_ the epoch is the mission base epoch of 1991.5 instead of 2000. These are most often multiple stars, but there are a few singles in the mix.

All HYG positions have been adjusted to epoch + equinox 2000.0. As a result, to avoid inconsistencies in some Tycho-2 positions, stars with a pflag of X in Tycho and a valid HIP match have had their positions replaced with HIP positions. This is the basis of the "HIP_X" position source indicator.

To clean up HYG, I applied an appropriate number of years of proper motion data to the HIPPARCOS data to bring it to epoch 2000.0. I didn't do this _directly_ with Tycho-2 pflag "X" stars because pflag = "X" also means no Tycho-2 proper motions. One of the likely improvements for v1.x or v2.x of the catalog is to use GAIA proper motions, when available, to adjust Tycho-2 stars with pflag = "X". This is not part of any v0.x releases.

### Possible additions in later releases

Here are some items that are possible additions:

1. Add proper motion data from GAIA.
2. Add radial velocity values (and so also full 3D space velocities) from GAIA.
3. Add BT (blue) magnitudes to Tycho's VT, giving us color index (on the BT and VT values) and also possibly an estimate of V magnitudes for Tycho stars, making magnitude values more consistent
4. Add spectral types directly from HD stars to ones that don't have them in HIP.
5. Add 3-letter constellation labels for non-HYG entries.

