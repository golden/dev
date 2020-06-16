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

Updates

```awk
function NumAdd(i,x,    d) {
  if (x=="?") return x
  x += 0 # coerce to string
  i.n++
  i.lo  = x < i.lo ? x : i.lo
  i.hi  = x > i.hi ? x : i.hi
  d     = x - i.mu
  i.mu += d/i.n
  i.m2 += d*(x - i.mu)
  NumSd(i)
  return x
}
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
function NumSd(i) {
  if (i.m2 < 0) return 0
  if (i.n  < 2) return 0
  i.sd = (i.m2/(i.n - 1))^0.5
  return i.sd
}

function NumMid(i) { return i.mu }
function NumVar(i) { return i.sd }
```

```awk
function NumDist(i,x,y) {
  if (x=="?" && y=="?") return 1
  if (x=="?") { y=NumNorm(i,y); x=y<0.5?1:0; return abs(x-y)}
  if (y=="?") { x=NumNorm(i,y); y=x<0.5?1:0; return abs(x-y)}
  x = NumNorm(i,x)
  y = NumNorm(i,y)
  return abs(x - y) 
}

function NumNorm(i,x) {
  if (x ~ /\?/) return x
  return (x - i.lo)/(i.hi - i.lo + 10^-32)
}

function NumScore(i) {
  return i.txt "=" NumMid(i)
}
```
Discretization (cut two Gaussians, four ways).

```awk
function Num4Cuts(i,j,x,    a,b,c,d) {
  a = 1/(2*i.sd^2)      - 1/(2*j.sd^2)
  b = j.mu/(j.sd^2)     - i.mu/(i.sd^2)
  c = i.mu^2/(2*i.sd^2) - j.mu^2/(2*j.sd^2) - log(j.sd/i.sd)
  d = b^2 - 4 * a * c
  x[1]= (-b - sqrt(d))/(2*a)
  x[2]= i.mu
  x[3]= (-b + sqrt(d))/(2*a)
  x[4]= j.mu
  asort(x)
}
```
