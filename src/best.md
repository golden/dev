<a name=top>
<h1 align=center>
   <a href="https://github.com/golden/dev/blob/master/README.md#top">
     GOLD = a Gawk object layer
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

# Best

- [BestDiv()](#bestdiv--recursively-prune-worse-half-of-data) : recursively prune worse half of data
- [Support Code](#support-code--connections-from-best-to-tab) : connections from `Best` to `Tab`

```awk
@include "gold"
@include "tab"
```

Using random projections,
recursively divide the `rows` in a `Tab`le.

```awk
function Best(i,t,   cols,    r,rows) {
  Object(i)
  is(i,"Best")
  has(i,"best")
  has(i,"rest")
  i.min    = 2*length(t.rows)^MY.best.min
  i.enough = MY.best.enough / length(t.rows)
  i.cols   = cols ? cols : MY.best.cols
  for(r in t.rows) 
    if (rand() < i.enough) 
      rows[r]
  BestDiv(i,t,rows)
}
```
## BestDiv() : recursively prune worse half of data 

Random projection (project using cosine 
rule between two distant points).

```awk
function BestDiv(i,t,rows, 
              any,far,away,a,b,c,x,r,mid,d,best) {
  if (length(rows) < i.min) 
    return copy(rows, i.best)
  any  = anys(rows) 
  far  = BestFar(  i,t, any, rows)
  away = BestFar(  i,t, far, rows)
  c    = BestDist( i,t, far, away)
  for(r in rows) {
    a     = BestDist(i,t, r, far)
    b     = BestDist(i,t, r, away)
    x     = (a^2+c^2 - b^2) / (2*c)  # cosine rule)
    if (x > 1) x = 1
    if (x < 0) x = 0
    mid  += x/length(rows)
    d[r]  = x
  }
  if (TabDom(t,far,away)) 
        for(r in d) d[r] <= mid ? best[r]   : i.rest[r]
  else  for(r in d) d[r] <= mid ? i.rest[r] : best[r];
  BestDiv(i,t,best) 
}
```
## Support Code : connections from `Best` to `Tab`

Note that when we talk to the table `t`, we use the columns
`i.cols`.

```awk
function BestDist(i,t,x,y) {return TabDist(t,x,y, t.the[i.cols])}
function BestFar( i,t,x,a) {return TabFar( t,x,a, t.the[i.cols])}
```
