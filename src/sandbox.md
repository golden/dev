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

```awk
@include "gold"

BEGIN {
  main( "1,a,1,a,1,b,1,a,1,a,1,b,1,a,1,a,1,a,1,a,1,a,2,a,2,a,"\
         "2,b,2,b,2,b,2,b,2,a,2,a,3,b,3,b,3,b,3,b,3,b,3,a,3,a,"\
         "3,a,3,b,4,a,4,a,4,a,4,a,4,b,4,a,4,a,4,b,5,b,5,b,5,b,"\
         "5,b,5,b,5,b,5,b")
}

function main(s) {
  s = s "," s "," s "," s "," s
  worker( s ) 
  rogues()
}

function worker(s,   n,j,xy,a,k,rs,x) {
  n = split(s,a,",")
  for(j=1;j<n;j+=2) 
   if ( a[j] != "x" ) {
      xy[++k].x = a[j] + rand()
      xy[  k].y = a[j+1]
  }
  print(length(xy))
  Ranges(xy, "b", rs)
  oo(rs)
}
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
function RangeMerge(i,j) {
  i.hi    = j.hi
  i.best += j.best
  i.rest += j.rest
  i.n    += j.n
}
function RangeFill(i,x,y) {
  x += 0
  if (x < i.lo) i.lo = x
  if (x > i.hi) i.hi = x
  i.best += y==i.want
  i.rest += y!=i.want
  return ++i.n >= i.min
}
function Ranges(a,ok,b,  min,j,r) {
  for (j=16; j>=2; j /=2) 
    if ((min = int(length(a)/j)) >= 4) 
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

function RangesMerged(a,   b4) {
  b4 = length(a)
  RangesMerge(a, length(a), 0)
  return b4 > length(a)
}

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
