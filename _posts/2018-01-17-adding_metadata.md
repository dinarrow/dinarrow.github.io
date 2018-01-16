---
theme: jekyll-theme-hacker
layout: default
title: Adding Value Metadata
date: 2018-01-17  00:00:00
---

#  Adding Value Metadata

## Problem 0: Representing null ( no value) without a sentinel value

While it is often possible to represent no value using a sentinel value (0,-1) what happens when the sentinel value is required?

The solution is an addition value containing meta information for the other values ( in a class, database etc.)

## Structure of metadata value

To represents nulls on the obvious structure is a bit field with 1 bit per value. However unless data size is an issue it is simpler to use 1 byte per value.

So the meta value can be a char array of length n where n is the number of values represented.

## Expanding types of metadata

The byte per value means that up to 8 types of binary meta information can be stored in the structure. This however may be restricted if for example ASCI compatability is required (base64 = 6 types?).

To expand the meta representation to an unlimited number of types we can either apply the simple rule:-

if( length of metadata > no of values) then the additional bytes are meta-metadate ( version?) and the structure is not the simple 1 byte per value

or

use the UTF-8 method of using special byte values to indicate that additional bytes are required per value.

## Best Solution

A combination of the two options above. Start with the simple 1 byte per value representation as it should be sufficient for most cases and is easy to manipulate, process and read. If a larger representation is required the add an additional byte to indicate another representation is used and use the value of the addtional byte to indicate the coding scheme. This is infinitely expandable provided the last version of the previous coding is available to indicate a new coding.

