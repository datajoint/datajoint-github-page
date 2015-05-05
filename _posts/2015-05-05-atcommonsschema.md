---
layout: post
title: 'Tolias lab commons schema'
tags: ['schema', 'matlab']
summary: 'Schemata for neuroscience experiments'
author: 'Tolias lab at Baylor College of Medicine'
---

# Download

You can download the schemas from [github repository](https://github.com/atlab/commons).

# Description

The [github repository](https://github.com/atlab/commons) contains the public portions of DataJoint schemas for basic processing data 
from neuroscience experiments that are shared between several members of Andreas Tolias' lab.

Each schema may contain additional classes that are not included in this public repository
but may show up in some ERDs (entity relationship diagrams).

# Schemas and ERDs


+common
-----
Basic information about experiments
![alt text](https://raw.github.com/atlab/commons/master/schemas/+common/erd.png "ERD")

+psy
------
Visual stimuli 
![alt text](https://raw.github.com/atlab/commons/master/schemas/+psy/erd.png "ERD")

+tp
-----
Two-photon imaging using scanimage
![alt text](https://raw.github.com/atlab/commons/master/schemas/+tp/erd.png "ERD")

+opt
----
Optical imaging of intrinsic signals
![alt text](https://raw.github.com/atlab/commons/master/schemas/+opt/erd.png "ERD")

+mp
----
Multipatch experiments
![alt text](https://raw.github.com/atlab/commons/master/schemas/+mp/erd.png "ERD")

Libraries
======
+ne7 

Visual Stimulus
======
The visual stimulus works with the +psy schema.
