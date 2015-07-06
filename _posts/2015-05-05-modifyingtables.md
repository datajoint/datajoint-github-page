---
layout: post
title: 'Modifying tables'
tags: ['tutorial','matlab','python','help']
summary: 'How to modify tables'
---


Once a table has been declared and populated, it is still possible to change the structure of the table and its foreign keys. These changes are accomplished by the following methods:

## Matlab

```
  dj.Table/addAttribute
  dj.Table/alterAttribute
  dj.Table/dropAttribute

  dj.Table/addForeignKey
  dj.Table/dropForeignKey

  dj.Table/addIndex
  dj.Table/dropIndex

  dj.Table/setTableComment
```

For example, change the definition of the existing field `nframes` in table `tp.Align`

```
tp.Align.table.alterAttribute('nframes', 'nframes: smallint  # total number of frames')
```

Each of these functions will request user confirmation before automatically updating the table declaration in the class file.

## Python 

In Python these functions are not provided at the moment,  but we are working on it ...