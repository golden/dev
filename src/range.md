<a name=top>
<h1 align=center>
   <a href="https://github.com/golden/dev/blob/master/README.md#top">
     GOLD = the Gawk Object Layer
   </a>
</h1>
<p align=center>
   <a    href="https://github.com/golden/dev/blob/master/LICENSE.md#top">license</a>
   :: <a href="https://github.com/golden/dev/blob/master/INSTALL.md#top">install</a>
   :: <a href="https://github.com/golden/dev/blob/master/CONTRIBUTE.md#top">contribute</a>
   :: <a href="https://github.com/golden/dev/issues">issues</a>
   :: <a href="https://github.com/golden/dev/blob/master/CITATION.md#top">cite</a>
   :: <a href="https://github.com/golden/dev/blob/master/CONTACT.md#top">contact</a>
</p>
<p align=center>
   <img width=600 src="https://github.com/golden/dev/raw/master/etc/img/coins.png">
</p>
<p align=center>
   <img src="https://img.shields.io/badge/language-gawk-orange">
   <img src="https://img.shields.io/badge/purpose-ai,se-blueviolet">
   <img src="https://img.shields.io/badge/platform-mac,*nux-informational">
   <a href="https://travis-ci.org/github/golden/dev"> <img src="https://travis-ci.org/golden/dev.svg?branch=master"></a>
   <a href="https://doi.org/10.5281/zenodo.3887420"><img src="https://zenodo.org/badge/DOI/10.5281/zenodo.3887420.svg" alt="DOI"></a>
</p>

# Range

Bottom up discretizer.
In the following code, `Range` is about one range and `Ranges` is a manager of a list of ranges.

- [Range()](#range--constructor-for-one-range) : constructor for one range
    - [Update](#update) 
        - [RangeFile()](#rangefile--and-a-pair-of-number-and-symbol-to-a-range) : and a pair of number and symbol to a range
    - [Merging](#merging) 
        - [RangeMayMerge()](#rangemaymerge--can-we-combine-two-ranges) : can we combine two ranges
        - [RangeMerge()](#rangemerge--do-the-merge) : do the merge
- [Ranges()](#ranges--constructor-for-manager-of-a-list-of-ranges) : constructor for manager of a list of ranges
    - [Manage the Merging](#manage-the-merging) 
        - [RangesMerged()](#rangesmerged--repeatedly-merge-a-list-of-ranges-until-there-is-nothing-left-to-merge) : repeatedly merge a list of ranges (until there is nothing left to merge.
        - [RangesMerge()](#rangesmerge--try-to-merge-two-ranges-then-move-on-down-the-lst) : try to merge two ranges, then move on down the lst

This code works like this:

- Inputs a list of items, each of which `z` has fields `z.x` and `z.y`. 
- Divides the list into  16 bins (and if those bins are too small, then  it trues 8, then 4, then 2).
- While  a `MayMerge`  predicate likes adjacent bins, they are merged. 
- Returns the merged lists

For example, where's 16 bins of 215 numbers ranging from one to six merged into three 
`Range`s: 

- Bins  1  ,2,  3,  4,  5,  6,  7 were merged;
- Bins  8,  9, 10, 11, 12, 13, 14, 15 were merged;
- Bins 16, 17. 18, 19, 20 were merged

In this case, `MayMerge` scored each bin on how well they predicted for what we `want` (i.e. class `b`):

        1
        |  lo:   1.01757
        |  hi:   2.96538
        |  n:    91
        |  best: 28
        |  rest: 63
        |  want: b
        8
        |  lo:   2.96838
        |  hi:   5.37041
        |  n:    104
        |  best: 57
        |  rest: 47
        |  want: b
        16
        |  lo:   5.38099
        |  hi:   5.97559
        |  n:    20
        |  best: 20
        |  rest: 0
        |  want: b

## Range() : constructor for one range

```awk
@include "gold"

function Range(i, want, min,last) {
  i.lo   =  10^64
  i.hi   = -10^64
  i.last = last
  i.want = want
  i.best = 0
  i.rest = 0.00001
  i.n    = 0.00001
  i.min  = min
}
```
### Update

#### RangeFile() : and a pair of number and symbol to a range

```awk
function RangeFill(i,x,y) {
  x += 0
  if (x < i.lo) i.lo = x
  if (x > i.hi) i.hi = x
  i.best += y==i.want
  i.rest += y!=i.want
  return ++i.n >= i.min
}
```
### Merging

#### RangeMayMerge() : can we combine two ranges

```awk
function RangeMayMerge(i,j, 
                       bi,bj,b,ri,rj,r, si,sj,s) {
  #-- bests
  bi =  i.best; bj  = j.best; b  = bi+bj; 
  bi /= i.n;    bj /= j.n;    b /= (i.n+j.n)
  #-- rests
  ri =  i.rest; rj  = j.rest; r  = ri+rj; 
  ri /= i.n;    rj /= j.n;    r /= (i.n+j.n)
  #-- scores
  si = bi^2   / (bi + ri)
  sj = bj^2  /  (bj + rj)
  s  = b ^2 /   (b  + r)
  return (s >= si*1.05 || s >= sj*1.05) 
}
```
#### RangeMerge() : do the merge

```awk
function RangeMerge(i,j) {
  i.hi    = j.hi
  i.best += j.best
  i.rest += j.rest
  i.n    += j.n
}
```
## Ranges() : constructor for manager of a list of ranges

```awk
function Ranges(a,ok,b,  min,j,r) {
  for (j=16; j>=2; j /=2) 
    if ((min = ns int(length(a)/j)) >= 4) 
      break 
  if (min<4) min = 4
  has3(b, ++r ,"Range", ok,min)
  keysort(a,"x")
  for(j=1; j <= length(a); j++)  
    if ( RangeFill( b[r], a[j].x, a[j].y) )
      if (j < length(a) - min)
        if(a[j].x != a[j+1].x) 
          has3( b, ++r, "Range", ok, min,r-1);
  while (RangesMerged(b));
}
```
### Manage the Merging

#### RangesMerged() : repeatedly merge a list of ranges (until there is nothing left to merge.

```awk
function RangesMerged(a,   b4) {
  b4 = length(a)
  RangesMerge(a, length(a), 0)
  return b4 > length(a)
}
```
#### RangesMerge() : try to merge two ranges, then move on down the lst

```awk
function RangesMerge(a,y,z,    x,m) {
  if (y in a) { 
    x = a[y].last
    if (x && RangeMayMerge( a[x], a[y] )) {
        RangeMerge( a[x], a[y] )
        if(z) a[z].last = x
        delete a[y];
        RangesMerge(a,x,z)  
    } else
        RangesMerge(a,x,y) }
}
```
