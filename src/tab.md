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

# Tab

- [Create](#create) 
    - [Tab()](#tab) 
- [Update](#update) 
    - [TabCol()](#tabcol--make-new-column) : make new column
    - [TabRead()](#tabread--read-a-new-table-from-disc) : read a new table from disc
- [Geometry](#geometry) 
    - [TabDist()](#tabdist--return-a-distance-between-rows) : return a distance between rows
    - [TabFar()](#tabfar--return-a-row2-far-away-from-row1) : return a row2 far away from row1
    - [TabAround()](#tabaround--find-distances-to-other-things) : find distances to other things
- [Optimization](#optimization) 
    - [TabDom()](#tabdom--checks-if-row1-dominant-row2) : checks if row1 dominant row2
- [Printing](#printing) 
    - [TabShow()](#tabshow--print-entire-table) : print entire table
    - [TabScore()](#tabscore--print-just-the-goal-columns) : print just the goal columns.

```awk
@include "gold"  # standard libraries
@include "num"  # knows "mu", standard deviations "sd"
@include "sym"  # knows "mode" and counts of "seen" symbols
@include "poly" # polymorphic functions
```
## Create
### Tab()

`Tab`les store raw data in `rows`  and summaries of those data in
`cols` (columns). For example `i.rows[r][c]` holds  data from row
`r` and columns `c`.

```awk
function Tab(i,  header,c) { 
  Object(i)
  is(i,  "Tab")
  has(i, "cols")
  has(i, "rows")
  has(i, "the") # stores indexes to particular subsets of cols 
  has(i, "header")
  TabCols(i, header)
}
```
The first row of data names the columns. Special symbols on those
names distinguish different kinds of columns. For example, in the
following data fragment...

    outlook, $temp,  <humid, wind,  !play        
    rainy,   68,     80,     FALSE, yes # comments
    sunny,   85,     85,     FALSE, no 
    ...

... then the columns are:

- `$temp` is a column of `num`bers; 
- `<humid` is a `goal` to be minimized; 
- `!play` is a `sym`bolic class;
-  and the rest of the columns hold `sym`bols.

Note that we say that the goals and classes are the `y` columns and
the others are the `x` columns.

## Update
### TabCol() : make new column

Define a new column whose name is `x` at position `c`.

```awk
function TabCols(i,a,      x,c,xy,what) { 
  for(c in a) {
    x = i.header[c]= a[c]
    # -------------------
    # are you a goal column?
    if  ( x ~ /[<>]/ ) i.the.goals[c]
    # -------------------------------
    # are you an independent "x" or dependent "y" column?
    xy   =  x ~ /[!<>]/  ? "y"   : "x"  
    i.the[xy][c]
    # ----------
    # are you the klass column? (there can only be one)
    if  ( x ~ /!/ ) i.the.klass = c
    # -----------------------------
    # are you a string "Sym" col or a numeric "Num" column?
    what =  x ~ /[\$<>]/ ? "Num" : "Sym"
    what == "Num" ? i.the.nums[c] : i.the.syms[c]
    # -------------------------------------------
    # make the new column (of type "what")
    hass(i.cols,c,what,x,c)
}}
```
### TabRead() : read a new table from disc

`Tab`les can be initialize from  comma separated 
value files via `tabRead`. If `f` is omitted, 
then this code reads from standard input.

```awk
function TabRead(i,f,    c,it) {
  Row(it,f)
  while (Rows(it)) {
    if (it.r == 0) 
      TabCols(i,it.cells)
    else 
      for(c in it.cells)
        i.rows[it.r][c] = add(i.cols[c], it.cells[c]) }
  close(f)
}
```
## Geometry

### TabDist() : return a distance between rows

Returns the distance between two rows, measured in terms of the
columns `cols`.

```awk
function TabDist(i,r1,r2,cols,  
                 c,d,inc,n,p) {
  p = MY.tab.p
  n = length(cols)+0.00001
  for(c in cols) {
    inc = dist(i.cols[c], i.rows[r1][c], i.rows[r2][c])
    d  += inc^p
  }
  return (d/n)^(1/p)
}
```
### TabFar() : return a row2 far away from row1

Search through `rows` to returns a row that is far away from row
`r1`.  This function restricts its computation of distance to just
the `cols` columns.  To avoid extreme outliers, we only go `MY.tab.far`
away from `r1` (e.g. no more than 90% towards the most distant
point).

```awk
function TabFar(i,r1,rows,cols,  a,n) {
  n = TabAround(i,r1,rows,cols,a)
  n = int(n*MY.tab.far)  
  return a[n].row
}
```
### TabAround() : find distances to other things

Sets `a` to a list of pairs `<row,dist>` from row `r1` to all other
`rows`. Sort the  `a` list by distance.  Note that `a[1]..a[k]`
hold the _k_ nearest neighbors.

```awk
function TabAround(i,r1,rows,cols,a,   n,r2) {
  for(r2 in rows) 
    if(r1 != r2) {
      a[r2].row = r2
      a[r2].dist = TabDist(i,r1,r2,cols) }
  return keysort(a,"dist")
}
```
## Optimization
### TabDom() : checks if row1 dominant row2

Given two rows _1,2_ with multiple goals, then
_1_ is better than _2_ if we lose less
moving from _2_ to _1_ than  vice versa.

 
```awk
function TabDom(i,r1,r2,   c,e,n,x,y,s1,s2) {   
  n = length(i.the.goals)
  for(c in i.the.goals) {
    x   = i.rows[r1][c]
    y   = i.rows[r2][c]
    x   = NumNorm(i.cols[c], x)
    y   = NumNorm(i.cols[c], y)
    s1 -= 2.72 ^ ( i.cols[c].w * (x - y)/n )
    s2 -= 2.72 ^ ( i.cols[c].w * (y - x)/n )
  }
  return s1/n < s2/n
}
```
Notes:

- `i.cols[c].w` is negative for things we want to minimize (and
  positive otherwise).
- In order to stress the difference between goals, we raise them
  to some power (here, _e=2.72_).
- But before that, in order to avoid real number expositions, we
  normalized the goal values.
- This domination function was propose by Zitler in 2005 for the
  IBEA optimization algorithm. Various authors have shown that this
  works much better for assessing multiple (n>2) goals that the more
  traditional binary domination predicate.

## Printing

### TabShow() : print entire table

```awk
function TabShow(i,     r) {
  print o(i.header)
  for(r in i.rows) print o(i.rows[r])
}
```
### TabScore() : print just the goal columns.

```awk
function TabScore(i,  c,s,sep) {
  for(c in i.the.goals) {
    s   = s sep score(i.cols[c])
    sep = ", " }
  return s
}
```
