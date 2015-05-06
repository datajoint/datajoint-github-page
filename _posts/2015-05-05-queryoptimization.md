---
layout: post
title: 'Query optimization'
tags: ['tutorial','matlab','python','help']
summary: 'How to get answers fast.'
---

Logically equivalent relational expressions may take very different times to execute. It takes some experience to learn to devise fast expressions.

For example,

```
A*B & 'condition on B'
```

often executes faster than 

```
A & (B & 'condition on B')
```

even though the first expression has more attributes. 


```
(A & 'condition on A')*B
```

often takes longer than 

```
A*B & 'condition on A'
```