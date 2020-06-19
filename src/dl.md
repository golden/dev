```awk
@include "gold"

# grow the tree from the root

function  Dl(i,f,b) {
  Object(i)
  is(i, "Dl")
  i.best = best ? best : "true"
  Tab(t)
  TabRead(t,f)
  DlGrow(t)
}

function DlGrow(i,t,  col,best) {
  if (length(t.rows) > xxx) 
    return 1
  List(best)
  for(col in t.the.x)
    DiCol(i, t, col, best)
  keysort(best,  "scores")
  DiGrowSplit(i,t, best[ length(best) ])
}

function DiTree(i, header) {
  Object(i)
  has(i,"left", "Tab", header)
  has(i,"right","Tab", header)
}
function DiGrowSplit(i,t,range,   here,r) {
  DiTree(here, t.header)
  for(r in t.rows)
    if (RangeCovers(range, t.rows[r])) 
      TabRow(here.right, t.rows[r])
    else
      TabRow(here.left, t.rows[r]);
  DiGrow(i, here.left)
}

function DlCol(i,t,col,best,    r,x,f,a,ranges,f) {
   for(r in t.rows) {
    x = t.rows[r][col]
    if (x != "?") {
       a[r].x = x
       a[r].y = t.rows[r][t.the.klass] }
  }
  f= col in t.the.nums ?  "DlNums" : "DlSyms"
  @f(i,a,ranges) 
  keysort(ranges, "score")
  ranges[ length(ranges) ].col = col
  end2end(ranges, best) 
}

function DlNums(i,a,ranges) { Ranges(a, i.best, ranges) }

function DlSyms(i,a,ranges,   min,j,a,x,y) {
  for(j in a) {
    y = a[j].y
    if( ! (y in ranges))
      has(ranges,y,"Range", i.best, min);
    RangeFilled(ranges[y], a[j].x, y)
  }
  min = RangesSize(a)
  for(y in ranges) 
    if (ranges[y].n > min)
      RangeScore( ranges[y] )
}
```
