---
title: "CrystalDiskInfo - interpretation of values"
date: 2024-02-27T20:22:00+01:00
draft: false
categories: [tech]
tags: [smart]
---

What are the interpretation of CrystalDiskInfo parameters? How to understand the health of a hard disk via reading SMART?

![CrystalDiskInfo parameters example](/images/crystaldiskinfo.png)


- RAW value (hex/dec)

  From the result of this attribute parameter that we learn about the current state of the disk.
  It shows the raw result, for example, for attribute 5 it is the number of reallocated sectors.

  - important note: change default hex to decimal values: `Function -> Advanced Feature -> Raw values -> 10 [DEC]`

- Threshold

  This is the lowest limit of values of various attributes.
  SMART compares attribute values with thresholds to identify failures.
  The number that determines the threshold is determined by the device manufacturer.
  The threshold is set at the factory by the device manufacturer and does not change. 

- Worst

  This is the worst recorded value of the attribute that has occurred so far (the lowest recorded value of *Value*).

- Current

  The value can be taken 1-100, but there are attributes that take other values.
  The device compares attribute values with thresholds.
  When attribute values are greater than thresholds, the device operates normally.
  When *Value* is below *Threshold*, it indicates a failure, although this is not a necessary condition.


source:
https://www.elektroda.pl/rtvforum/topic2543701.html

