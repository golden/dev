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

# sym

## Create

```awk
#include "ape"
```
### Sym()

```awk
function Sym(i,txt,pos) { 
  Object(i)
  is(i, "Sym")
  i.pos  = pos
  i.txt  = txt
  i.mode = ""
  i.most = 0
  has(i,"seen") 
}
```
## Update.

### SymAdd()

```awk
function SymAdd(i,v,  tmp) {
  if (v == "?") return v
  i.n++
  tmp = ++i.seen[v]
  if (tmp > i.most) { i.most = tmp; i.mode = v }
  return v 
}
  
function SymDec(i,v,  tmp) {
  if (v == "?") return v
  if (v in i.seen) {
    tmp = i.seen[v]
    if (tmp > 0) {
      i.n--
      i.seen[v]-- }}
  return v 
}
function SymMerge(i,j,k,   s,z) {
  k.n = i.n + j.n
  for(s in i.seen) { k.seen[s] += i.seen[s] }
  for(s in j.seen) { k.seen[s] += j.seen[s] }
  for(s in k.seen) { z          = k.seen[s]
                     if (z > k.most) {
                       k.most = z
                       k.mode = s }}
}
```
### SymDist()

Distance between 2 symbols.

```awk
function SymDist(i,x,y,    z) {
  if (x=="?" && y=="?") return 1
  return x != y 
}
```
## Var and Mid

```awk      
function SymMid(i) { return i.mode }  

function SymVar(i,  e,p,x,tmp) {
  for (x in i.seen) {
    tmp = i.seen[x]
    if (tmp > 0) {
      p = tmp/i.n
      e -= p*log(p)/log(2) }}
  return e
}
```
```awk
function SymScore(i) { return i.txt "=" SymMid(i) }
```
