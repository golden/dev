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

# Num

- [Num()](#num--creation) : creation
- [Updates](#updates) 
    - [NuumAdd()](#nuumadd--add-new-item) : add new item
    - [NumSd()](#numsd-update-standard-deviation) : update standard deviation
    - [NuumDec()](#nuumdec--remove-item) : remove item
- [Reports](#reports) 
    - [NumMid(),  NumVar()](#nummid--numvar) 
    - [NumNorm()](#numnorm--normalize-a-number-01-minmax) : normalize a number 0..1 min..max
    - [NumScore()](#numscore--report-name-and-mid) : report name and mid
    - [NumDist()](#numdist--distance-between-2-numbers) : distance between 2 numbers

```awk
@include "gold"
```

## Num() : constructor

```awk
function Num(i,txt,pos) {
  Object(i)
  is(i, "Num")
  i.pos = pos
  i.txt = txt
  i.w   = (txt ~ /</) ? -1 : 1
  i.n   = i.mu = i.m2 = i.sd = 0
  i.lo  = 10^32
  i.hi  = -1*i.lo
}
```

## Updates

### NuumAdd() : add new item

```awk
function NumAdd(i,x,    d) {
  if (x=="?") return x
  x = x+0 # ensure we have a number
  i.n++
  i.lo  = x < i.lo ? x : i.lo
  i.hi  = x > i.hi ? x : i.hi
  d     = x - i.mu
  i.mu += d/i.n
  i.m2 += d*(x - i.mu)
  NumSd(i)
  return x
}
```
### NumSd(): update standard deviation

Called as a side-effect of `NumAdd` and `NumDec`.

```awk
function NumSd(i) {
  if (i.m2 < 0) return 0
  if (i.n  < 2) return 0
  i.sd = (i.m2/(i.n - 1))^0.5
  return i.sd
}
```

### NuumDec() : remove item

```awk
function NumDec(i,x,     d) {
  if (x == "?") return x
  if (i.n < 1 ) return x
  i.n--
  d     = x - i.mu
  i.mu -= d / i.n
  i.m2 -= d * (x - i.mu)
  i.sd  = NumVar(i)
  return x
}
```
## Reports

###  NumMid(),  NumVar()

```awk
function NumMid(i) { return i.mu }
function NumVar(i) { return i.sd }
```
###  NumNorm() : normalize a number 0..1 min..max

```awk
function NumNorm(i,x) {
  if (x ~ /\?/) return x
  return (x - i.lo)/(i.hi - i.lo + 10^-32)
}
```
###  NumScore() : report name and mid

```awk
function NumScore(i) {
  return i.txt "=" NumMid(i)
}
```
### NumDist() : distance between 2 numbers

This algorithm comes from David Aha's Instance-based Reasoning paper, AIJ, 1991.
When values are unknown, assume the worst and maximize the distance between them.

Returns 0..1

```awk
function NumDist(i,x,y) {
  if (x=="?" && y=="?") return 1
  if (x=="?") { y=NumNorm(i,y); x=y<0.5?1:0; return abs(x-y)}
  if (y=="?") { x=NumNorm(i,y); y=x<0.5?1:0; return abs(x-y)}
  x = NumNorm(i,x)
  y = NumNorm(i,y)
  return abs(x - y) 
}
```

