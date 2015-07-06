---
layout: post
title: 'Fetching data'
tags: ['tutorial','matlab','python','help']
summary: 'How to retrieve data from the database'
---

As discussed previously, [base relvars]({% post_url 2015-05-05-baserelvars %}) and derived relvars only represent the data in the database but do not themselves contain any of it. The `fetch` operators retrieve the data represented by relvars into the Matlab/Python workspace.

The fetch operators always perform a relational [projection]({% post_url 2015-05-05-projection %}) and accept the same arguments as the projection of the corresponding relvar.

## Matlab

For more details, see 

``` matlab
>> doc dj.Relvar/pro
>> doc dj.Relvar/fetch
>> doc dj.Relvar/fetch1
>> doc dj.Relvar/fetchn
```

## Python

DataJoint Relations in Python provide the member functions `fetch1` and `fetch`. These return numpy structured arrays by default, but can also return a list of dictionaries. Fetching into pandas DataFrames is planned, but not implemented yet. 