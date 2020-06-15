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
   <img width=600 src="https://github.com/golden/dev/blob/masteretc/img/coins.png">
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
@include "sym"

function Xy(i) {
  i.xlo =  10^64
  i.xhi = -10^64
  has(i,"y","Sym")
}
function XyAdd(i,x,y) {
  if (x > i.xhi) i.xhi = x
  if (x < i.xlo) i.xlo = x
  SymAdd(i.y, y)
}
function XyB2r(i,k,     b,r) {
  b = k in i.y.seen ? i.y.seen[k] : 0
  r = i.y.n - b 
  b = b  / i.y.n
  r = r /  i.y.n
  return b^2/(b+r)
}
function XyMerge(i,j,k) {
  Xy(k)
  SymMerge(i.y, j.y, k.y)
  k.xlo = i.xlo < j.xlo ? i.xlo : j.xlo
  k.xhi = i.xhi > j.xlo ? i.xhi : j.xhi
}
function XyCombine(i,j,b,k,   old1,old2,new) {
  XyMerge(i, j, k)
  old1 = XyB2r(i, b)
  old2 = XyB2r(j, b)
  new  = XyB2r(k, b) * My.range.same
  return (new >= old1) && (new >= old2)
}

```
