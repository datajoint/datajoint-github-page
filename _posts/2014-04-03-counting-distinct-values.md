---
layout: post
title: "Counting distinct values"
description: ""
category: tips 
tags: [matlab,example]
---
{% include JB/setup %}
Here is how you cound count the number of distinct values in a relation.

Let's work with table `psy.Session` that defines sessions of psychophysics experiments and table `psy.Grating` that defines drifting gratings presented to the subject during that session with attributes `direction` and `spatial_freq`.  Furthermore, two-photon recordings can be performed during some sessions and synchronized using table <code>tp.Sync</code>.

Below are query examples that count distinct values of attributes of gratings per session.

**Find the number of unique grating directions and spatial frequencies per stimulus session**

```
pro(psy.Session, psy.Grating, ...
  'count(distinct direction)->ndirections', ...
  'count(distinct spatial_freq)->n_spat_freqs')
```

__Find all two-photon synchronized sessions with at least 8 grating directions__

```
pro(tp.Sync, psy.Grating, 'count(distinct direction)->n') & 'n>=8'
```

<code>count(distinct ...)</code> can also be applied to count unique combinations of attribute values:

**Find the average number spatial frequencies per direction in each session**

```
pro(psy.Session, psy.Grating, ...
 'count(distinct spatial_freq, direction)/count(direction)->avgndir')
```

Such queries are particularly useful for creating precise [popRels](https://github.com/datajoint/datajoint-matlab/wiki/Populating-computed-data). 
