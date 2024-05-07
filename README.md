# Yet Another TIPLOC list

GB railway geographic data is well known to be unreliable.

There have been a number of efforts to create a clean data set with varying degrees of success.

This is yet another one.

_What is different about this one?_

The data has been tagged and is traceable through the cleansing process, so for error correction and licencing it is easier to attribute the data, although there is some merging of multiple fields from different sources.

## Data sources

| Rows | Data source                                                                                             | Licence                                                                                                                                                      |
| ---- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 2685 | [NAPTAN](https://beta-naptan.dft.gov.uk/)                                                               | [OGL3](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/)                                                                           |
| 493  | [Rail Delivery Group](https://data.atoc.org/rail-industry-data)                                         | [CC BY 2.0 UK](https://creativecommons.org/licenses/by/2.0/uk/deed.en)                                                                                       |
| 15   | [Network Rail TPS](https://publicdatafeeds.networkrail.co.uk/ntrod/SupportingFileAuthenticate?type=TPS) | [OGL3](https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/transparency/open-data-feeds/network-rail-infrastructure-limited-data-feeds-licence) |
| 695  | [BPLAN](https://wiki.openraildata.com/index.php?title=BPLAN_Geography_Data)                             | [OGL3](https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/transparency/open-data-feeds/network-rail-infrastructure-limited-data-feeds-licence) |
| 208  | [CORPUS](https://publicdatafeeds.networkrail.co.uk/ntrod/SupportingFileAuthenticate?type=CORPUS)        | [OGL3](https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/transparency/open-data-feeds/network-rail-infrastructure-limited-data-feeds-licence) |
| 5996 | [woodpecker](https://github.com/anisotropi4/woodpecker)                                                 | [Creative Commons 4.0](http://creativecommons.org/licenses/by-sa/4.0/)                                                                                       |
| 4075 | [GB Railway Data Ltd](https://railmap.azurewebsites.net/Downloads)                                      | [Creative Commons 4.0](http://creativecommons.org/licenses/by-sa/4.0/)                                                                                       |
| 1070 | [Network Rail FOI2015-01334](https://www.whatdotheyknow.com/request/geographical_information_on_tipl)   | [OGL3](https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/freedom-of-information-foi/)                                                         |
| 108  | [UK2GTFS](https://itsleeds.github.io/UK2GTFS-data/)                                                     | [AGPL-3.0](https://github.com/ITSLeeds/UK2GTFS-data/blob/main/LICENSE)                                                                                       |

## Cleansing process

The outline of cleansing process is (some detail has been omitted for clarity) :-

1. **CORPUS** is taken as the primary input defining relationships between STANOX, TIPLOC and CRS codes. (but contains no geography)
   As we flow through the process, geography is added from various sources to this data.

   Other data rows that are not present in the initial list will be added if the location code is not currently present in the list, so the resulting list is not just a TIPLOC list, but will contain geographic records from other sources, primarily level crossing locations. Where the output rows state the data source, that is the data source of the geography; the initial code data will likely have come from CORPUS.

   CORPUS only contains live locations, so the other datasets may contain locations that are no longer active on the network - or are not present on the Network Rail network (e.g. on island of Ireland)

2. **NAPTAN**: We treat NAPTAN geographic data as authoritative where it is not null (for Crossrail / Elizabeth line stations the NAPTAN data has valid codes but null geography). There are a small number of NAPTAN nodes that do not have a TIPLOC, usually because they are a heritage railway location. For these we insert the value from the Naptan.ATCO field into the TIPLOC field.

3. **Rail Delivery Group MSN**: The Master Station Name file is treated as the next most authoritative source apart from locations on the island of Ireland, for which the geography is bad, so we exclude that data and pick those 56 locations up from another data set.

   The CRS code from ATOC data is used in preference to the CRS code from NAPTAN data, and if present, overwrites the CRS code from the previous data set.

   The reason for this is that a small number of stations have multiple TIPLOCs. Within the NAPTAN data these are given unique distinct CRS codes. This is not desirable from a passenger perspective, where we only want one CRS for what a passenger would view as being one station.

   Hence we use the CRS code from the ATOC data, where there is only one CRS for what a passenger would consider as one station – but this may be replicated in multiple records (each of which has a distinct TIPLOC). Examples of this situation are Retford High Level / Low Level, Lichfield Trent Valley, and several Crossrail / Elizabeth line stations.

   We treat this as our definitive source of stations which have multiple TIPLOCs for a single CRS code.

4. **TPS**: Only a small number of rows in the public TPS data have any geometry. These are clipped to records on the GB mainland, which removes records floating somewhere in the North Sea, leaving 15 records remaining from this data set. For TIPLOC `ABTHA39` alter Northing value from `766451` to `166451`

   For all the remaining data sets a combination of manual cleansing and 3-way voting has been used.

5. **Woodpecker**: the following TIPLOCs are removed from the dataset as a result of manual cleansing: `KRKP861`,`HIGHBYA`,`GRTN`,`BLUANCR`,`WROXHMR`,`MNTFLDS`,`ALBION`,`ALRSFRD`,`HRGY419`,`ECROYDN`,`SPEAPSS`,`REDHLS`,`TALRCEM`,`CRST428`,`CRST421`,`KSTP`,`HOTH343`,`STRHILL`,`PBRO612`,`PBRO552`,`PBRO544`,`PBRO534`,`ELGH225`,`ABIN521`,`ABINUS`,`ABIN528`,`CHTHAP`,`PLMD782`,`IVRN397`,`HYWRDNK`,`FELTDEP`,`LENH334`,`IVRN395`,`NNTN539`,`RTHG858`,`WNCF103`,`MLLNJN`,`RTHG888`,`BARKGLT`,`BEAL134`,`LYDNDGL`,`SLAT675`,`TERM182`,`WLNDNJN`,`WLNDNJW`,`IVRNBLC`,`HITC672`,`IMNGLL7`,`KGMRDDS`,`ABRGS38`,`DRTW68`,`BRAGJN`,`CARLNEC`,`NTHOPKJ`,`CRST423`,`WLWY182`,`LEED928`,`GRNGMRR`,`BLTCDRJ`,`BLTCWER`

   Geography for `BSAUWCP` (coalWasher) is replaced with `(54.7256, -1.8717)`

   Geography for `KINBTLP` (kinbraceTimber) is replaced with `(58.2546, -3.9411)`

   Rows with the following STANOX codes are removed: `7802`,`8751`,`13045`,`13048`,`24428`,`36400`,`56120`,`56309`,`56310`,`58560`,`82017`

6. **GB Railway Data Ltd (Rail Map)**: The following TIPLOCs are removed from the dataset as a result of manual cleansing:

   `CBOMDS`,`TESTSCR`,`GODALBN`,`WELSRHY`,`DONC108`,`ONIBURY`,`EBWVSGF`,`LLWRW2`,`EASTHAM`,`CRKLLAF`,`MANSNHS`,`EMBKMNT`,`GLNG26`,`CHAT65`,`KGMRUD1`,`CRGNTPS`,`WLBCGBR`,`HITC941`,`MRGMMID`,`XTEST01`,`XTEST02`,`GLGN185`,`EHBJN`,`POLMOCE`,`MURODS`,`BEATCE`,`BDNHABD`

   The Railmap data contains a large number of records that are not TIPLOCs, but are level crossing and junction locations - these can be distinguished by being 9 characters long starting 'ELOC' e.g. ELOC02161

7. **Network Rail FOI2015-01334**: The following TIPLOCs are removed from the dataset as a result of manual cleansing:
   `GRTN`,`RBRTRPG`,`WROXHMR`,`GLNG26`,`CBOMDS`,`IVRNBLC`,`CHAT65`,`HITC672`,`IMNGLL7`,`KGMRUD1`,`ABRGS38`,`DRTW68`,`CRGNTPS`,`WLBCGBR`,`CARLNEC`,`HITC941`,`MRGMMID`,`NTHOPKJ`,`CRST423`,`WLWY182`,`LEED928`,`GRNGMRR`,`BLTCDRJ`,`BLTCWER`,`BESTGBR`,`BEST931`,`BESTRR`,`BESTHST`,`BEST918`,`SWNACFC`,`SWNAGE`,`SWNAHMC`,`SWNAHNH`,`SWNAMGF`,`SWNANDN`,`TESTSCR`,`HRGY419`,`ALRSFRD`,`GODALBN`,`LEAGSDG`,`SWNLMD`,`HIGHBYE`,`DONC108`,`CLCH033`,`CLCH072`,`HOTH343`,`BORN421`,`BORN419`,`BORN412`,`BORN414`,`ONIBURY`,`HIGHBYA`,`WHMP236`,`WHMP617`,`REDHLS`

   TIPLOCs at E286200 N931659 and E260885 N667397 are removed. These represent two clusters of TIPLOCs that had multiple identical values in this dataset.

8. For TIPLOCs present in all 3 data sets of Woodpecker, Railmap and OpenRailData, a 3 way vote takes place between then with the two closest points being retained in the set and the outlying point eliminated.

9. **UK2GTFS**: TIPLOCs at (58.25884, -3.94107) and (55.87958, -4.22536) are removed. These represent two clusters of TIPLOCs that had multiple identical values in this datasets.

   The following TIPLOCs are removed from the dataset as a result of manual cleansing:
   `YSTRDMS`,`RBRTRPG`,`HOTH343`,`SWNLMD`,`BEST918`,`BEST931`,`BESTGBR`,`BESTHST`,`CLCH033`,`CLCH072`,`BESTRR`,`BESTTSB`,`WHMP236`,`WHMP617`,`BORN421`,`BORN419`,`BORN412`,`BORN414`,`WHMP236`,`WHMP617`

10. This set of TIPLOCs is combined with the output from step(8) and another 3 way vote takes place, with the two closest points being retained.

11. **BPLAN**: most of the geometry in BPLAN data is bad. The geometry is removed from BPLAN records where:

    - Easting <=1 or Northing <=1 or Easting >=999999 or Northing >= 999999
    - OR Easting = 10000
    - OR Easting = 99999 and Northing = 99999
    - OR `PIACNZA`,`DNKIRK`,`GRNLUDR`

    The remaining records are clipped to GB mainland (+ 1km buffer), and geography removed from records falling into the sea.

12. **STANOX deduplication**: For records that do not have a STANOX code, we attempt to find another location within 15m and assign the same STANOX code (potential duplicate locations but without a STANOX code assigned to them, because they are not rows from CORPUS)

    Records that have the same STANOX, but different TIPLOCs are treated as distinct locations. Otherwise we treat records with the same STANOX and same name (description) and records with the same geography (less than 100m apart) as the same.

13. The first two digits of STANOX code should indicate the geographical area – so we group by the first two digits, and determine the minimum circle size that all that group can fit within. (as described here https://wiki.openraildata.com/index.php/STANOX_Areas )

    This geographic numbering scheme was applied when STANOX codes were created, but may not apply to subsequently created STANOX, so should only be treated as a warning indicator of a potentially bad geographic location rather than a hard error.

    If the circle size is >100km we flag this, and this is then used to manually spot outlying locations which are then manually added to the lists of excluded locations.

## Output Files

The main output file is in CSV format, (tiplocs-merged.csv)

This file is a close approximation to a GTFS format stop location file, with some of the standard GTFS field names being used for slightly different purposes.

Some records will contain null geographies. These are almost exclusively BPLAN records where the indicated location was in the sea, so we retain the record by remove the known bad geometry.

| Field           | Description                                                            |
| --------------- | ---------------------------------------------------------------------- |
| `stop_id`       | TIPLOC code                                                            |
| `stop_code`     | CRS code                                                               |
| `stop_name`     | Description / human friendly name                                      |
| `platform_code` | STANOX code                                                            |
| `stop_url`      | data source this record originated from (see list at top of this file) |
| `stop_lon`      | Longitude (WGS84)                                                      |
| `stop_lat`      | Latitude (WGS84)                                                       |
| `easting`       | GB national grid easting (EPSG:27700)                                  |
| `northing`      | GB national grid northing (EPSG:27700)                                 |

## CRS (co-ordinate referencing system)

Geography is provided in both lat-long format (WGS84 / EPSG:4326) and also GB national grid (OSGB36 / EPSG:27700)

A number of data points are on the island of Ireland where GB National Grid is not a recommended projection system.
https://www.gov.uk/guidance/uk-geospatial-data-standards-coordinate-reference-systems

For locations on the island of Ireland it is recommended to re-project the data from WGS84 into a more appropriate projection system such as Irish National Grid TM75 (EPSG:29903) or preferably ITM (Irish Transverse Mercator EPSG:2157).

## Licensing

We do not intend to add any additional licence restrictions to the use of this data, however the licence of the contributing sources must be respected.

In terms of ability to commercially re-use this data, of the contributing data sources GPL3 appears to the most restrictive licence, to best of our understanding. So we are publishing this data under GPL3 license terms. It is still vital that the licenses of respective data sources are followed, and attribution is provided where required by their licenses.

This dataset is partially comprised of public sector information licensed under the Open Government Licence v3.0.

This is **not** an official data set.
