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

# Polymorphism

```awk
function add(i,x,    f) { f=i.ois "Add";   return @f(i,x)  }
function dec(i,x,    f) { f=i.ois "Dec";   return @f(i,x)  }
function show(i,     f) { f=i.ois "Show";  return @f(i)    }
function doing(i,    f) { f=i.ois "Doing"; return @f(i)    }
function score(i,    f) { f=i.ois "Score"; return @f(i)    }
function dist(i,x,y, f) { f=i.ois "Dist";  return @f(i,x,y)}
function merge(i,j,  f) { f=i.ois "Merge"; return @f(i,j) }
function filled(i,x,y, f) { f=i.ois "Filled"; return @f(i,x,y) }
function mayMerge(i,a,b,c,d,e,  f) { f=i.ois "MayMerge";  return @f(i,a,b,c,d,e) }
```
