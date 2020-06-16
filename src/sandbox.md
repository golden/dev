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

function worker(s,   n,j,xy,a,k,rs) {
  n = split(s,a,",")
  for(j=1;j<n;j+=2) 
   if ( a[j] != "x" ) {
      xy[++k].x = a[j]+ rand()
      xy[  k].y = a[j+1]
  }
  ranges(xy, "b", rs)
  oo(rs)
}
function Range(i, want, min) {
  i.lo =  10^64
  i.hi = -10^64
  i.n  = 0
  i.want = want
  has(i,"here")
  i.min = min
}
function RangeFill(i,x,y) {
  x += 0
  if (x < i.lo) i.lo = x
  if (x > i.hi) i.hi = x
  i.here[ y==i.want ]++
  return ++i.n >= i.min
}
function ranges(a,ok,b,  min,j,r) {
  for (j=16; j>=2; j /=2) 
    if ((min = int(length(a)/j)) >= 4) 
      break 
  if (min<4) min = 4
  hasss(b, (r=1) ,"Range", ok,min)
  keysort(a,"x")
  for(j=1; j <= length(a); j++)  
    if ( RangeFill( b[r], a[j].x, a[j].y) )
      if (j < length(a) - min)
        if(a[j].x != a[j+1].x) 
          hasss( b, (r=r+1), "Range", ok, min)
}
function score(ranges,j,k,     n,b,r) {
  for(i=j;i<=j;i++) {
    n += ranges[i].n
    b += ranges[i].here[1]
    r += ranges[i].here[0]
  }
  b = b /n
  r = r /n
  return b*b / (b+r) 
}
```
