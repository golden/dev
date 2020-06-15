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
@include "gold"
@include "tab"

BEGIN {tests("tabok","_tab,_rows")} 

function _tab(f,    i) {
  Tab(i)
  TabRead(i,"data/raw/auto93" GOLD.dot "csv") 
  ok(f,length(i.rows)==398)
  ok(f,"number" == typeof(i.rows[1][2]))
  ok(f ":3", i.cols[1].seen[3] == 4)
  ok(f ":8", i.cols[1].seen[8] == 103)
}

function _rows(f,    m,n,i,j,k,some,r1,r2,a,dom) {
  Tab(i)
  TabRead(i,"data/raw/auto93" GOLD.dot "csv") 
  m = 256
  while(m-- > 0)
     some[ anys(i.rows) ]
  for(r1 in some)
    for(r2 in some)
      if(r1>r2) {
        dom[r1].r1 = r1 
        dom[r1].dom += TabDom(i,r1,r2)
  }
  n= keysort(dom,"dom")
  for(j=1; j<=10;j++)
    print o(i.rows[ dom[j].r1 ] ) "," dom[j].dom
  print ""
  for(j=n-10;j<=n;j++)
    print o(i.rows[ dom[j].r1 ] ) "," dom[j].dom

}
```
