---
layout: post
title: 'Table tiers'
tags: ['tutorial','matlab','python','help']
summary: 'Specifying the type of table.'
---

Every table in a DataJoint schema is assigned one of the four tiers: `lookup`, `manual`, `imported`, or `computed`.  The tier helps establish the basic role of the data in the table and how valuable (difficult to replace) these data are. In Python, the tier is specified by inheriting from `dj.Manual`, `dj.Computed`, `dj.Lookup`, or `dj.Imported`. Additionally, one can mix in inheritance from `dj.Subordinate` to indicate that the table is a subtable. 

**Lookup** tables contain information that is involved in setting up the processing chain itself: filter settings, algorithm parameters, etc. Lookup tables are populated manually and their contents are copied when a new schema is set up.

**Manual** tables contain information about experiments that are entered manually or by other software outside of the DataJoint processing chain. Due to their high value and small size, manual tables are backed up most frequently (e.g. everyday or after every experiment).

**Imported** tables contain information that is computed automatically by the DataJoint processing chain but requires access to external files outside the database (e.g. raw recordings). Raw recordings are typically in large files that cannot be kept online indefinitely. Once the extraction algorithms have stabilized and the extracted data have been imported into the database, the raw recordings may be taken offline. Imported tables need to be backed up regularly, especially after the raw data files have been taken offline.

**Computed** tables are populated automatically with data obtained from other tables within the database. Computed tables do not typically need to be backed up at all unless the computation time far exceeds the effort of restoring from backups.
