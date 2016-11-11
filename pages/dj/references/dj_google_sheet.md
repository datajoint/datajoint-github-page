---
title:  'Hooking into a google spreadsheet'
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "How to feed data from google spread sheet into the database"
sidebar: dj_sidebar
permalink: dj_glossary.html
folder: dj/references
---



Lookup tables are useful to collect data that is usually only entered once or not updated very often. An example is a list of viruses used in opto-genetic experiments. The table is then used as primary foreign key in later experiments.

Datajoint-python makes it easy to define and enter values in a lookup table directly in the definition of the relation. When the class is created, datajoint-python checks for a property called `contents`. The only requirement for `contents` is that it must be iterable and yield single entries that can be inserted with `insert1`. This gives it a lot of flexibility. The example below fetches data from a google spreadsheet and enters it in the database. Existing values are ignored. Note that this is not a two-way synchronization between the two tables. It only makes sure that whatever is in the google spreadsheet is also in the database.

In order to make the example work, you need to follow the [instructions](http://gspread.readthedocs.org/en/latest/oauth2.html) how to sign into a google spreadsheet with oauth2. This will give you a `.json` file containing the credentials. You need to share the spreadsheet with the email address contained in this file.

```python
import json
import gspread
from oauth2client.client import SignedJwtAssertionCredentials
import datetime
import time

import datajoint as dj
schema = dj.schema('my_data_base', locals())

@schema
class Virus(dj.Lookup):
    definition = """
    #table of viruses

    virus_id                    : int          # unique id for each produced or purchased virus
    -----
    virus_name                  : varchar(255) # full virus name
    virus_gene                  : varchar(255) # gene name
    virus_opsin                 : varchar(255) # opsin version
    virus_type                  : varchar(255) # type of virus
    serotype = "N/A"            : varchar(255) # AAV serotype
    virus_promoter = "N/A"      : varchar(255) # viral promoter
    virus_isfloxed              : boolean      # true if expression is dependent on Cre
    virus_source                : varchar(255) # source of virus
    virus_lot                   : varchar(32)  # lot #
    virus_titer                 : float        # titer
    virus_notes=""              : varchar(4095)# free-text notes
    virus_ts=CURRENT_TIMESTAMP  : timestamp    # automatic

    """

    @property
    def contents(self):

        json_key = json.load(open('my_service_account.json'))
        scope = ['https://spreadsheets.google.com/feeds']

        credentials = SignedJwtAssertionCredentials(json_key['client_email'], \
        		bytes(json_key['private_key'], 'utf-8'), scope)
        gc = gspread.authorize(credentials)

        wks = gc.open("Datajoint Virus Lookup")
        yield from wks.sheet1.get_all_records()
```
