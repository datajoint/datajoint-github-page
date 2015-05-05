---
layout: post
title: 'Fetching data'
tags: ['tutorial','matlab','python','help']
summary: 'How to retrieve data from the database.'
---

As discussed previously, [[base relvars]] and derived relvars only represent the data in the database but do not themselves contain any of it. The **fetch** operators retrieve the data represented by relvars into the MATLAB workspace.

The fetch operators always perform a relational [[projection]] and accept the same arguments as `dj.Relvar/pro`.

For more details, see 
```
>> doc dj.Relvar/fetch
>> doc dj.Relvar/fetch1
>> doc dj.Relvar/fetchn
```