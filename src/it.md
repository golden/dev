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

# Interators

- [Input](#input) 
    - [csv()](#csv--iterate-over-rows-in-a-file) : iterate over rows in a file
    - [Row()](#row--for-all-rows-ignore-some-columns) : for all rows, ignore some columns

```awk
@include "gold"
@include "poly"
```

## Input

### csv() : iterate over rows in a file

Iterator.

Standard GAWK can read simple comma seperated files. 
But what about CSV files with:

- comments, that should be stripped away?
- blank lines, that should be skipped?
- spurious white space, that should be deleted?
- records that break over multiple lines?

For example, here is a csv file where the first record is really
`name,age,shoesize`, there are comments and new lines and white
space to ignore, and the last record is split over multiple lines.

        name,     # clients name
        age,      # posint, 0..120
        shoesize
        #-------------------------
 
        tim,   21,  12
        susan, 22,  2132
        sarah,
               14,  101

This `csv` function can handle all that. Note that the second `file`
argument is optional and, if omitted, this code will read from
standard input.

```awk   
function csv(a,file,     b4, status,line) {
  file   = file ? file : "-"           # [1]
  status = getline < file
  if (status<0) {   
    print "#E> Missing file ["file"]"  # [2]
    exit 1 
  }
  if (status==0) {
    close(file) 
    return 0
  }                                    # [3]
  line = b4 $0                         # [4]
  gsub(/([ \t]*|#.*$)/, "", line)      # [5]
  if (!line)       
    return csv(a,file, line)           # [6]
  if (line ~ /,$/) 
    return csv(a,file, line)           # [4]
  split(line, a, ",")                  # [7]
  return 1
}
```
Notes:

1. Defaults to standard input
2. Complains on missing input
3. At end of file, close this stream
4. If line ends in ",", combine this line with the next.
   Else....    
5. Kill whitespace and comments
6. Skip blank lines
7. Split line on "," into the array "a"

Example usage:

     function lintcsv(file,    row,n,want) 
        while(csv(row,file)) 
          if (++n == 1) 
            want = length(row) # row1 defines what we `want`
          else if (want != length(row)) 
            print "#E> row " n " wrong number of fields" 
     }

### Row() : for all rows, ignore some columns

Iterator.

Read a csv file `f`, 

- Set `i.cells` to all columns that _do not_ start with `?`.
- Coerce all number strings to numners
- Set `i.r` to the current row number (and first row as `i.r=0`)

```awk
function Row(i,file) {
  Object(i)
  is(i,"Row")
  i.file = file
  has(i,"use")
  has(i,"cells")
  i.r = -1
}
function RowDoing(i,   c,tmp,n,x,y) {
  if (!csv(tmp,i.file)) 
    return 0
  if (!length(i.cells)) 
    for(c in tmp) 
      if (tmp[c] !~ /\?/)
        i.use[c] = ++n;
  i.r++
  for(c in i.use)  {
    x = tmp[c]
    y = x+0
    i.cells[ i.use[c] ] = x==y ? y : x
  }
  return 1
}
```
Note: `i` needs to be initialized before
each call to `Rows` e.g.

     Row(it, "data" GOLD.dot "csv")
     while( Doing(it) ) {
       print it.cells[1]
   
