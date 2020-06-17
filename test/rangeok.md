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
@include "range"

BEGIN { tests("rangeok","_range1,_range2,_range3,_range4,_range5") }

function _range1(f) {
  _range1a("1,a,1,a,1,b,1,a,1,a,1,b,1,a,1,a,1,a,1,a,1,a,2,a,2,a,"\
           "2,b,2,b,2,b,2,b,2,a,2,a,3,b,3,b,3,b,3,b,3,b,3,a,3,a,"\
           "3,a,3,b,4,a,4,a,4,a,4,a,4,b,4,a,4,a,4,b,5,b,5,b,5,b,"\
           "5,b,5,b,5,b,5,b")
}

function _range2(f) {
  _range2a("10,a,11,a,12,a,13,b,14,b,15,b,16,b,17,b,18,b,19,b,20,b,21,b,20,a,"\
           "23,a,24,a,25,a,26,a,27,a,28,a,30,a,31,a,32,a,33,a,34,a,35,a,36,a,"\
           "37,a,38,a,40,b,41,b,42,b,43,b,"\
           "44,b,45,b,46,b,47,b,50,b,51,b,52,b,"\
           "53,b,54,b,55,b,56,b")
}
function _range3(f) {
  _range2a("10,a,11,a,12,a,13,a,14,a,15,a,16,a,17,a,18,a,19,a,20,a,21,a,20,a,"\
           "23,a,24,a,25,a,26,a,27,a,28,a,30,a,31,a,32,a,33,a,34,a,35,a,36,a,"\
           "37,a,38,a,40,a,41,a,42,a,43,a,"\
           "44,a,45,a,46,a,47,a,50,a,51,a,52,a,"\
           "53,a,54,a,55,a,56,a")
}
function _range4(f) {
  _range2a("10,a,11,a,12,a,13,a,14,a,15,a,16,a,17,a,18,a,19,a,20,a,21,a,20,a,"\
           "23,a,24,a,25,a,26,a,27,a,28,a,30,a,31,a,32,a,33,a,34,a,35,a,36,a,"\
           "37,b,38,b,40,b,41,b,42,b,43,b,"\
           "44,b,45,b,46,b,47,b,50,b,51,b,52,b,"\
           "53,b,54,b,55,b,56,b")
}
function _range5(f) {
  _range2a("10,a,11,a,12,a,13,a,14,a,15,a,16,a,17,a,18,a,19,a,20,a,21,a,20,a,"\
           "23,a,24,a,25,a,26,a,27,a,28,a,30,a,31,a,32,a,33,b,34,b,35,b,36,b,"\
           "37,b,38,b,40,b,41,b,42,b,43,b,44,b,45,a,46,a,47,a,50,a,51,a,52,a,"\
           "53,a,54,a,55,a,56,a")
}

function _range1a(s,   n,j,xy,a,k,rs,x) {
  s = s "," s "," s "," s "," s
  n = split(s,a,",")
  for(j=1;j<n;j+=2) 
   if ( a[j] != "x" ) {
      xy[++k].x = a[j] + rand()
      xy[  k].y = a[j+1]
  }
  Ranges(xy, "b", rs)
  print "\n========================="
  oo(rs)
}
function _range2a(s,   n,j,xy,a,k,rs,x) {
  s = s #"," s "," s "," s "," s
  n = split(s,a,",")
  for(j=1;j<n;j+=2) 
   if ( a[j] != "x" ) {
      xy[++k].x = a[j] 
      xy[  k].y = a[j+1]
  }
  Ranges(xy, "b", rs)
  print "\n========================="
  oo(rs)
}
```
