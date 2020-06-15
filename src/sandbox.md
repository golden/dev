```awk
@include "lib"

BEGIN {
  main1( "1,a,1,a,1,b,1,a,1,a,1,b,1,a,1,a,1,a,1,a,1,a,2,a,2,a,"\
      "2,b,2,b,2,b,2,b,2,a,2,a,3,b,3,b,3,b,3,b,3,b,3,a,3,a,"\
      "3,a,3,b,4,a,4,a,4,a,4,a,4,b,4,a,4,a,4,b,5,b,5,b,5,b,"\
      "5,b,5,b,5,b,5,b")
}

function main(s) {
  s = s "," s "," s "," s "," s
  main( s ) 
  rogues()
}

function main(s,   n,j,xy,a,k,n16) {
  n = split(s,a,",")
  for(j=1;j<n;j+=2) {
    xy[++k].x = a[j]
    xy[  k].y = a[j+1]
  }
  keysort(xy,"x")
  oo(xy)
  n16 = int(n/16)
  div(xy)
}
```
