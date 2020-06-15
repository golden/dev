<a name=top>
<h1 align=center>
   <a href="https://github.com/timm/shape/blob/master/README.md#top">
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
   <img width=600 src="https://github.com/timm/misc/blob/master/odd/etc/img/coins.png">
</p>
<p align=center>
   <img src="https://img.shields.io/badge/language-gawk-orange">
   <img src="https://img.shields.io/badge/purpose-ai,se-blueviolet">
   <img src="https://img.shields.io/badge/platform-mac,*nux-informational">
   <a href="https://travis-ci.org/github/golden/dev"> <img src="https://travis-ci.org/golden/dev.svg?branch=master"></a>
   <a href="https://doi.org/10.5281/zenodo.3887420"><img src="https://zenodo.org/badge/DOI/10.5281/zenodo.3887420.svg" alt="DOI"></a>
</p>

```awk
@include "ape"

BEGIN {  tests("libok","_list,_copy,_csv,_anys") }

function _list(f,    a) {
  copy(MY,a)
  ok(f, a.tab.p == 2)
}

function _copy(f,    a,b) {
  a.now.here.one   = 111
  a.now.here.two   = 112
  a.now.there.one  = 121
  a.now.there.two  = 122
  a.then.here.one  = 211
  a.then.here.two  = 212
  a.then.there.one = 221
  a.then.there.two = 222 
  copy(a, b)
  b.here.now.one = "here_now_one"
  ok(f, a.now.here.one == 111)
  ok(f, b.here.now.one == "here_now_one")
}

function _csv(f, it,n) {
  Row(it,"data/raw/weather" APE.dot "csv") 
  while( Rows(it) )
    n += length(it.cells) 
  ok(f, n == 60)
  ok(f, typeof(it.cells[1])=="string")
  ok(f, typeof(it.cells[2])=="strnum")
}
function _anys(f,   n,s,r,good,x) {
  n = 10
  while(n--) s[n+1]
  r = 1000
  while(r--) 
    good +=  within( nanys(s) ,1,10)
  ok(f, good==1000)
}
```
