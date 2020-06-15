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
@include "poly"
@include "num"

BEGIN {  tests("numok","_num") }

function _num(f,   n,lst,s,i) {
   s=" 9 2 5 4 12 7 8 11 9 3" \
     " 7 4 12 5 4 10 9 6 9 4"
   split(s, lst, " ")
   Num(n)
   for(i in lst)
    add(n,lst[i])
   ok(f "-sd", int(100*n.sd)== 306)
   ok(f "-mu", n.mu ==  7)
}
```
