# YA_Tiploc_List
Yet another TIPLOC list
=======================

GB railway geographic data is well known to be unreliable.

There have been a number of efforts to create a clean data set with varying degrees of success.

This is yet another one.

What is different about this one ?

The data has been tagged and is traceable through the cleansing process, so for error correction and licencing it is easier to attribute the data.
(although there is some merging of multiple fields from multiple sources)

====================================================================================================================================

Rows:	Data source:	Url:	                            Licence:
------	------------   -----                                --------------------                                                          
2685	NAPTAN 	    https://beta-naptan.dft.gov.uk/    	OGL3 https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/

495		ATOC	    https://data.atoc.org/how-to        Creative Commons England and Wales Public Licence

15		TPS	      	https://publicdatafeeds.networkrail.co.uk/ntrod/SupportingFileAuthenticate?type=TPS
														OGL3  https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/transparency/open-data-feeds/network-rail-infrastructure-limited-data-feeds-licence

695		BPLAN	    https://wiki.openraildata.com/index.php?title=BPLAN_Geography_Data
					https://wiki.openraildata.com/index.php?title=Network_Rail_API_Platform
														OGL3 https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/transparency/open-data-feeds/network-rail-infrastructure-limited-data-feeds-licence

208		CORPUS	    https://publicdatafeeds.networkrail.co.uk/ntrod/SupportingFileAuthenticate?type=CORPUS
              	    https://wiki.openraildata.com/index.php?title=Reference_Data
														OGL3 https://www.networkrail.co.uk/who-we-are/transparency-and-ethics/transparency/open-data-feeds/network-rail-infrastructure-limited-data-feeds-licence

5993	woodpecker 	https://github.com/anisotropi4/woodpecker  Apache 2.0  https://github.com/anisotropi4/woodpecker/blob/main/LICENSE

4074	railmap	    https://railmap.azurewebsites.net/Downloads    Creative Commons 4.0  http://creativecommons.org/licenses/by-sa/4.0/

1075	openRailData https://wiki.openraildata.com/index.php/Identifying_Locations
              		https://wiki.openraildata.com/index.php?title=File:TIPLOC_Eastings_and_Northings.xlsx.gz
                                                        Creative Commons 4.0 https://creativecommons.org/licenses/by-sa/4.0/        

108		UK2GTFS	    https://itsleeds.github.io/UK2GTFS/
              		https://github.com/ITSLeeds/UK2GTFS-data   GPL3  https://www.r-project.org/Licenses/GPL-3

==================================================================================================================================


The outline of cleansing process is (some detail has been omitted for clarity) :-

1)	The process builds up a list of locations, adding new locations to the list at each step. If a location already exists in the list it is not added again 
(TIPLOC code being treated as the primary key for this purpose)
Where a tiploc is already present in the list, if a the list entry has a blank STANOX or CRS, and an entry from a subsequent dataset has a value for these fields – then the STANOX / CRS field values are added from the subsequent dataset.

2)	We treat NAPTAN geographic data as authoritative where it is not null.
(for Crossrail / Elizabeth line stations the NAPTAN data has valid codes but null geography)
There are a small number of NAPTAN nodes that do not have a TIPLOC, usually because they are a heritage railway location. For these we insert the value from the Naptan.ATCO field into the TIPLOC field.

3)	ATOC MSN (Master Station Name file) is treated as the next most authoritative source.
(apart from locations on the island of Ireland, for which the geography is bad, so we exclude that data and pick those 56 locations up from another data set)

The CRS code from ATOC data is used in preference to the CRS code from NAPTAN data, and if present, overwrites the CRS code from the previous data set.

The reason for this is that a small number of stations have multiple TIPLOCs. Within the NAPTAN data these are given unique distinct CRS codes.
This is not desirable from a passenger perspective, where we only want one CRS for what a passenger would view as being one station.

Hence we use the CRS code from the ATOC data, where there is only one CRS for what a passenger would consider as one station – but this may be replicated in multiple records (each of which has a distinct TIPLOC). Examples of this situation are Retford High Level / Low Level, Lichfield Trent Valley, and several Crossrail / Elizabeth line stations.

We treat this as our definitive source of stations which have multiple TIPLOCs for a single CRS code.

4)	TPS: Only a small number of rows in the public TPS data have any geometry. These are clipped to records on the GB mainland, which removes records floating somewhere in the North Sea, leaving 15 records remaining from this data set.
For TIPLOC ‘ABTHA39’ alter Northing value from 766451 to 166451


For all the remaining data sets a combination of manual cleansing and 3-way voting has been used.

5)	Woodpecker:
The following TIPLOCs are removed from the dataset as a result of manual cleansing KRKP861,HIGHBYA,GRTN,BLUANCR,WROXHMR,MNTFLDS,ALBION,ALRSFRD,HRGY419,ECROYDN,SPEAPSS,REDHLS,TALRCEM,CRST428,CRST421,KSTP,HOTH343,STRHILL,PBRO612,PBRO552,PBRO544,PBRO534,ELGH225,ABIN521,ABINUS,ABIN528,CHTHAP,PLMD782,IVRN397,HYWRDNK,FELTDEP,LENH334,IVRN395,NNTN539,RTHG858,WNCF103,MLLNJN,RTHG888,BARKGLT,BEAL134,LYDNDGL,SLAT675,TERM182,WLNDNJN,WLNDNJW,IVRNBLC,HITC672,IMNGLL7,KGMRDDS,ABRGS38,DRTW68,BRAGJN,CARLNEC,NTHOPKJ,CRST423,WLWY182,LEED928,GRNGMRR,BLTCDRJ,BLTCWER

Geography for BSAUWCP (coalWasher) is replaced with long -1.8717, lat 54.7256
Geography for KINBTLP (kinbraceTimber) is replaced with long -3.9411, lat 58.2546

STANOX code is removed from row with STANOX= 79801
Rows with the following STANOX codes are removed.
8751,13045,13048,24428,36400,56120,58560,72283,82107,7802,82017


6)	Railmap:
The following TIPLOCs are removed from the dataset as a result of manual cleansing CBOMDS,TESTSCR,GODALBN,WELSRHY,DONC108,ONIBURY,EBWVSGF,LLWRW2,EASTHAM,CRKLLAF,MANSNHS,EMBKMNT,GLNG26,CHAT65,KGMRUD1,CRGNTPS,WLBCGBR,HITC941,MRGMMID,XTEST01,XTEST02

STANOX code is removed from rows with STANOX
46245,87581

The Railmap data contains a large number of records that are not TIPLOCs, but are level crossing and junction locations - these can be distinguished by being 9 characters long starting 'ELOC' e.g. ELOC02161 


7)	OpenRailData:
The following TIPLOCs are removed from the dataset as a result of manual cleansing 
GRTN,RBRTRPG,WROXHMR,GLNG26,CBOMDS,IVRNBLC,CHAT65,HITC672,IMNGLL7,KGMRUD1,ABRGS38,DRTW68,CRGNTPS,WLBCGBR,CARLNEC,HITC941,MRGMMID,NTHOPKJ,CRST423,WLWY182,LEED928,GRNGMRR,BLTCDRJ,BLTCWER,BESTGBR,BEST931,BESTRR,BESTHST,BEST918,SWNACFC,SWNAGE,SWNAHMC,SWNAHNH,SWNAMGF,SWNANDN,TESTSCR,HRGY419

TIPLOCS with easting = 286200 and northing = 931659 are removed.
TIPLOCS with easting = 260885 and northing = 667397 are removed.
These represent two clusters of TIPLOCs that had multiple identical values in this dataset.

Row with TIPLOC ‘BEST344’ has the TIPLOC code removed.

8)	For TIPLOCs present in all 3 data sets of Woodpecker, Railmap and OpenRailData, a 3 way vote takes place between then, with the two closest points being retained in the set, and the outlying point eliminated.


9)	UK2GTFS: 
TIPLOCS with long -3.94107, lat 58.25884 are removed
TIPLOCS with long -4.22536, lat 55.87958 are removed
These represent two clusters of TIPLOCs that had multiple identical values in this datasets.

10)	This set of TIPLOCs is combined with the output from step(8) and another 3 way vote takes place, with the two closest points being retained.

11)	BPLAN most of the geometry in BPLAN data is bad. We use it as a source of codes, but remove a lot of the geometry.
The geometry is removed from BPLAN records where :-
	Easting <=1 or Northing <=1 or Easting >=999999 or Northing >= 999999
	OR Easting = 10000
	OR Easting = 99999 and Northing = 99999
	OR PIACNZA,DNKIRK,GRNLUDR

For rows where the TIPLOC code is
WANTMPL,AMNG488,THYMGBR,ALRSFRD
The STANOX code is removed from the record.

The remaining records are clipped to GB mainland (+ 1km buffer), and geography removed from records falling in the sea.


12)	CORPUS
These records do not contain any geography, so are purely used to add missing location codes.
For rows where the STANOX code is 79801,87018 – the STANOX code is removed (arising from manual cleansing)

Some records have a null TIPLOC, some records have a null STANOX. Some STANOX codes appear to be associated with more than one (nearby) TIPLOC location.

Records are matched on TIPLOC, and if this fails, then matched on STANOX, copying in the relevant code fields if not already present in the set.


13)	STANOX deduplication
For records that do not have a STANOX code, we attempt to find another location within 15m and assign the same STANOX code
(potential duplicate locations but without a STANOX code assigned to them)

By this point in the process, particularly for records that do not have a TIPLOC code, we may have created duplicate STANOX records, so we attempt to de-duplicate these, without removing records that are nearby with different TIPLOC but the same STANOX

Records that have the same STANOX, but different TIPLOCs are treated as distinct locations.
Otherwise we treat records with the same STANOX and same name (description) as the same.
Otherwise we treat records with the same geography (less than 100m apart) as the same.


Finally:-
14)	The first two digits of STANOX code should indicate the geographical area – so we group by the first two digits, and determine the minimum circle size that all that group can fit within. (as described here https://wiki.openraildata.com/index.php/STANOX_Areas )

If the circle size is >100km we flag this, and this is then used to manually spot outlying locations which are then manually added to the lists of excluded locations.
There appears to be a very small number of STANOX codes from multiple official sources that are outliers – so the numbering scheme may not be applied to 100% of locations as expected.


Output Files
------------
The main output file is in CSV format, (tiplocs-merged.csv)

This file is a close approximation to a GTFS format stop location file, with some of the standard field names being used for slightly different purposes.

"stop_code"			CRS code
"stop_id"			TIPLOC code
"stop_name"			Description / human friendly name
"platform_code"			STANOX code
"stop_url"			data source this record originated from (see list at top of this file)
"easting"			GB national grid easting (EPSG:27700)
"northing"			GB national grid northing (EPSG:27700)
"stop_lon"			Longitude (WGS84)
"stop_lat"			Latitude (WGS84)


CRS (co-ordinate referencing system)
-----------------------------------
Geography is provided in both lat-long format (WGS84 / EPSG:4326) and also GB national grid (OSGB36 / EPSG:27700)

a number of data points are on the island of Ireland where GB national grid is not a recommended projection system.
https://www.gov.uk/guidance/uk-geospatial-data-standards-coordinate-reference-systems

for locations on the island of Ireland it is recommended to re-project the data from WGS84 into a more appropriate projection system such as
Irish National Grid TM75 (EPSG:29903) or preferably ITM (Irish Transverse Mercator EPSG:2157) 


Licencing :-
-------------
We do not intend to add any additional licence restrictions to the use of this data - however the licence of the contributing sources must be respected.

In terms of ability to commercially re-use this data, of the contributing data sources GPL3 appears to the best of our understanding to be the most restrictive, so we are publishing this data under GPL3 license terms.

This dataset is partially comprised of public sector information licensed under the Open Government Licence v3.0.

This is NOT an 'official' data set.
