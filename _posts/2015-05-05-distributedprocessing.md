---
layout: post
title: 'Distributed processing'
tags: ['tutorial','matlab','python','help']
summary: 'Enter data from several machines.'
---

DataJoint implements job management for distributed computation. Job management is used when the `parpopulate` method is used to populate any table. 

Upon the first invocation of `parpopulate` in a given schema, DataJoint will automatically create the job management table and its class `<package>.Jobs`. 

The job management table contains an entry for each `makeTuples` call that has been reserved for computed or that ended in error during the last execution (the error stack is saved). A successful completion of all population tasks will leave the job management table empty. 