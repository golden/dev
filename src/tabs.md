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

# Tabs

- [Tabs()](#tabs--create) : create
- [TabsK()](#tabsk--ensure-that-we-have-a-klass-k) : ensure that we have a klass `k`
- [TabsRead()](#tabsread--read-the-tables-from-disk) : read the tables from disk

Read a csv file into multiple tables:

- `i.all`   holds all data
- `i.tab[k]` just the rows for class `k`.

```awk
@include "tab"
```
## Tabs() : create

```awk
function Tabs(i) {
  has(i,"all",  "Tab")
  has(i,"tab")
}
```
## TabsK() : ensure that we have a table for class `k`

```awk
function TabsK(i,row,   k) {
  k = row[i.all.the.klass]
  if (!(k in i.tab)) 
    hass(i.tab, k, "Tab", i.all.header)
  return k
}
```
## TabsRead() : read the tables from disk

```awk
function TabsRead(i,f,  it,k,c,x) {
  Row(it, f)
  while(Rows(it)) {
    if (it.r == 0) 
      TabCols(i.all, it.cells)
    else {
      k = TabsK(i, it.cells)
      for(c in it.cells)  {
        x = it.cells[c]
        i.tab[k].rows[it.r][c] = add(i.tab[k].cols[c], x)
           i.all.rows[it.r][c] = add(   i.all.cols[c], x)}}}
  close(f)
}
```
