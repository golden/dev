<a name=top>
<h1 align=center>
   <a href="https://github.com/timm/shape/blob/master/README.md#top">
     SHape = SHell tricks  for Any Program + documeEntation
   </a>
</h1>
<p align=center>
   <a    href="https://github.com/timm/shape/blob/master/LICENSE.md#top">license</a>
   :: <a href="https://github.com/timm/shape/blob/master/INSTALL.md#top">install</a>
   :: <a href="https://github.com/timm/shape/blob/master/CONTRIBUTE.md#top">contribute</a>
   :: <a href="https://github.com/timm/shape/issues">issues</a>
   :: <a href="https://github.com/timm/shape/blob/master/CITATION.md#top">cite</a>
   :: <a href="https://github.com/timm/shape/blob/master/CONTACT.md#top">contact</a>
</p>
<p align=center>
   <img width=600 src="https://github.com/timm/misc/blob/master/odd/etc/img/solidgallery.gif">
</p>
<p align=center>
   <img src="https://img.shields.io/badge/language-gawk-orange">
   <img src="https://img.shields.io/badge/purpose-ai,se-blueviolet">
   <img src="https://img.shields.io/badge/platform-mac,*nux-informational">
   <a href="https://travis-ci.org/github/timm/shape"> <img src="https://travis-ci.org/timm/shape.svg?branch=master"></a>
   <a href="https://doi.org/10.5281/zenodo.3887420"><img src="https://zenodo.org/badge/DOI/10.5281/zenodo.3887420.svg" alt="DOI"></a>
</p>

```awk
@include "tabs"

function Bore(i,f) {
  has(i,"tab","Tab")
  TabRead(i.tab, f)
  BoreRead(i,i.tab)
}
function BoreRead(i,t,   x,y) {
  y = t.the.klass
  for(x in t.the.nums)
    if (x in t.the.x)  
      BoreRead1(i,t,x,y)
}
function BoreRead1(i,t,x,y,  xval,a,r,k) {
  for(r in t.rows) {
    xval = t.rows[r][x] + 0
    if (xval != "?")  {
      a[r].x = xval
      a[r].y = t.rows[r][y]
  }}
  n = keysort(a,"x")
  min = n / 16
  has(syms,s=1,"Sym")
  for(j=1; j<=n; j++)  {
    add(syms[s], a
  }
}
```
function SymAdds(i,j,k,v) {
  copy(i,k)
  k.n += j.n
  k.lo = i.lo < j.lo ? i.lo : j.lo
  k.hi = i.hi > j.hi ? i.hi : j.hi
  for(v in j.seen)
    k.seen[v] += j.seen[v]
  for(v in k.seen) {
    if (k.seen[v] > k.most) {
     k.most = k.seen[v]
     k.mode = v
}}}
```awk
function SymBore(i,k, b,r) {
  b = k in i.seen ? i.seen[k] : 0
  b = b / i.n
  r = i.n - b
  r = r / i.n
  return b^2/(b+r)
}
```
