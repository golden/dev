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

- [Range(i:Range, "want", $min, $left?)](#rangeirange-want-min-left--constructor-for-one-range) : constructor for one range
    - [Update](#update) : 
        - [RangeFilled()](#rangefilled--add-a-pair-of-number-and-symbol-to-a-range) : add a pair of number and symbol to a range
    - [Merging](#merging) : 
        - [RangeMayMerge()](#rangemaymerge--can-we-combine-two-ranges) : can we combine two ranges
        - [RangeMerge()](#rangemerge--do-the-merge) : do the merge
- [Manage a list of `Range`s](#manage-a-list-of-ranges) : 
    - [Ranges()](#ranges--constructor-for-manager-of-a-list-of-ranges) : constructor for manager of a list of ranges
    - [RangesMerge()](#rangesmerge--try-to-merge-two-ranges-then-move-on-down-the-list) : try to merge two ranges, then move on down the list


This code works like this:

- Inputs a list of items, each of which `z` has fields `z.x` and `z.y`. 
- Divides the list into  16 bins (and if those bins are too small, then  it trues 8, then 4, then 2).
- While  a `MayMerge`  predicate likes adjacent bins, they are merged. 
- Returns the merged lists

For example, where's 16 bins of 215 numbers ranging from one to six merged into three 
`Range`s: 

- Bins  1, 2,  3 were merged;
- Bins  4, 5, 6, through 14 were merged;
- Bins 16, 17, 18, 19, 20 were merged

In this case, `MayMerge` scored each bin on how well they predicted for what we `want` (i.e. class `b`):

        1
        |  lo:    1.01757
        |  hi:    1.7581
        |  min:   13
        |  n:     39
        |  best:   6
        |  rest:  33
        |  want:   b
        4
        |  lo:     1.77888
        |  hi:     5.03532
        |  min:   13
        |  n:    143
        |  best:  66
        |  rest:  77
        |  want:   b
        15
        |  lo:     5.04087
        |  hi:     5.97559
        |  min:   13
        |  n:     33
        |  best:  33
        |  rest:   0
        |  want:   b

## Range(i:Range, "want", $min, $left?) : constructor for one range

```awk
@include "gold"
@include "poly"

function Range(i, want, min,left) {
  Object(i)
  is(i, "Range")
  i.lo   =  10^64
  i.hi   = -10^64
  i.left = left
  i.want = want
  i.best = 0
  i.rest = 0.00001
  i.n    = 0.00001
  i.min  = min
}
```
### Update

#### RangeFilled() : add a pair of number and symbol to a range

Return true if there is enough in this range to
justify starting a new range.

```awk
function RangeFilled(i,x,y,     z) {
  x = x+0
  if (x < i.lo) i.lo = x
  if (x > i.hi) i.hi = x
  i.best += (y == i.want)
  i.rest += (y != i.want)
  i.n++
  return i.n >= i.min
}
```
### Merging

#### RangeMaybe() : can we combine two ranges

```awk
function RangeMaybe(i,j,      s,b,r) {
  b = (i.best + j.best) / i.bests
  r = (i.rest + j.rest) / i.rests
  s = b^2/(b+r)
  return (s >= RangeScore(i) && s >= RangeScore(j))
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

function RangeScore(i,    z,b,r) {
  z = 0.001
  b = i.best/ (i.bests+z)
  r = i.rest/ (i.rests+z)
  z = b^2/(b+r)
  i.score = z
  return z
}
```
## Manage a list of `Range`s

### Ranges() : constructor for manager of a list of ranges

- Divides the list into  16 bins (and if those bins are too 
  small, then  it trues 8, then 4, then 2).
- Then sort the divisions.
- Then make one `Range` per bin.
- Then trying merging the ranges.

```awk
function Ranges(a,ok,ranges,klass,  
                min,j,r,best,rest,now) {
  klass = klass ? klass : "Range"
  #--- decide how to divide up the numbers
  for (j=16; j>=2; j /=2) 
    if ((min = int(length(a)/j)) >= 4) 
      break 
  if (min<4) min = 4
  #--- sort the divisions
  keysort(a,"x")
  #--- make one Range per bin
  has3(ranges, ++r ,klass, ok,min)
  for(j=1; j <= length(a); j++)   {
    best += a[j].y == ok
    rest += a[j].y != ok
    if ( filled( ranges[r], a[j].x, a[j].y) )
      if (j < length(a) - min)
        if(a[j].x != a[j+1].x) 
          has3( ranges, ++r, klass, ok, min,r-1)
  }
  for(r in ranges) {
    ranges[r].bests = best + 0.0001
    ranges[r].rests = rest + 0.0001
  }
  #--- Trying merging what you can.
  while (r> now) {
    r  = length(ranges)
    now = RangesMerge(ranges, r)
  }
  keysort(ranges, "score")
}
```
### RangesMerge() : try to merge two ranges, then move on down the list

This code starts at the end the list, 
then loop to the left.
In the following:

- `a` are the ranges 
- `one,two,three` are integers that index into `a`. 
- So (e.g.) `one` is _not_ a range;
  - But `a[one]` is.each step of the loop:

The invariant here are:

- We look at  three consecutive ranges `one.two,three`;
- We check to  see if we can merge `two` into `one`. 
  If so, then:
  -  `three.left` should point to `one`
  - And we should delete `two`.
- Note two special cases: 
  - At the start of the loop, `two` is the current item and `three` is nil.
  - At the end of the loop, `two` falls
    off the left-hand-side of the list
    (which is the termiantion condition).
 
```awk
function RangesMerge(a,two,three,    one,m) {
  if (two in a)  {
    one = a[two].left
    RangeScore( a[two] )
    if (one && maybe( a[one], a[two])) {
       merge( a[one], a[two] )
       if(three) 
         a[three].left = one
       delete a[two];
       two = three
    } 
    RangesMerge(a, one, two)  
  }
  return length(a)
}
```
### Ranges4Col(): low-level utility to extract cols

```awk
function Ranges4Col(t,b,x,y,out,     a,x1,r,ranges) {
  List(a)
  for(r in t.rows) {
    x1 = t.rows[r][x]
    if (x1 != "?") {
       a[r].x = x1
       a[r].y = t.rows[r][y] }}
  Ranges(a,b,ranges)
  ranges[length(ranges)].col = x
  endAdd(ranges, out)
}
```
