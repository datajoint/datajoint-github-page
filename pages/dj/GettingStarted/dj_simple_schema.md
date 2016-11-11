---
title: Simple Schema  
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "An introduction to schemas."
sidebar: dj_sidebar
permalink: dj_simple_schema.html
folder: dj/GettingStarted
---
```
import datajoint as dj
schema = dj.schema('subjects', locals())

@schema
class Subjects(dj.Manual):
    definition = """
    # Basic subject info

    subject_id                   : int                     # id number
    ---
    first=""                     : varchar(20)             # first name
    last=""                      : varchar(20)             # last name
    sex="unknown"                : enum('M','F','unknown') # animal's sex
    subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic
    """

@schema
class Trials(dj.Manual):
    definition = """
    # info about trials

    -> Subjects
    trial_id                   : int          # trial id
    ---
    outcome                    : int           # result of experiment

    notes=""                   : varchar(4096) # other comments
    trial_ts=CURRENT_TIMESTAMP : timestamp    # automatic
    """

```
