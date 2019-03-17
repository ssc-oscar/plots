# Obtaining and plotting language and dependency trends


1. First extract relevant parts of the data (see grepNew.pbs and overview/deps/b2pkgs*)
1. Next calculate relevants stats
```
ver=N
LNGS="JS java PY C php Lisp Rust Scala rb Swift Go Lua Sql Cs R F jl Fml Cob"
for LA in $LNGS
do zcat c2ta$ver$LA.*.gz|awk -F\; '{print int($2/3600/24/365.25+1970)}' | lsort 10G | uniq -c > ${LA}$ver.trend
zcat c2ta$ver$LA.*.gz|awk -F\; '{print int($2/3600/24/365.25+1970)";"$3}' | lsort 10G | uniq | cut -d\; -f1 | uniq -c > ${LA}$ver.trendA
done
s="year";for LA in $LNGS; do s="$s;$LA";done; echo $s > cmts$ver.lng
for y in {2010..2018}; do s="$y";for LA in  $LNGS
do n=$(grep " $y$" ${LA}$ver.trend | awk '{print $1}'); s="$s;$n"; done; echo $s; done >> cmts$ver.lng
s="year";for LA in $LNGS; do s="$s;$LA";done; echo $s > cmts$ver.lng1
for y in {2010..2018}; do s="$y";for LA in $LNGS
do n=$(grep " $y$" ${LA}$ver.trendA | awk '{print $1}'); s="$s;$n"; done; echo $s; done >> cmts$ver.lng1
```
1. Now plot language trends
```
x=read.table("cmtsN.lng1",sep=";",header=T)
y=read.table("cmtsN.lng",sep=";",header=T)
png("AuthorsByLanguageN.png",width=1000,height=750);
matplot(x[,1],x[,-1],type="o",lwd=1:3,lty=1:7,col=1:5,log="y",pch="0123456789abcdefghi",xlab="Years",ylab="Authors");
N=dim(x)[2]-1;
legend(2016.1,1e04,legend=names(x)[-1],lty=1:7,col=1:5,lwd=1:3,pch="0123456789abcdefghi",bg="white")
dev.off()
png("PrdByLanguageN.png",width=1000,height=750);
matplot(x[,1],y[,-1]/x[,-1],type="o",lwd=1:3,lty=1:7,col=1:5,log="y",pch="0123456789abcdefghi",xlab="Years",ylab="Cmghi");
legend(2016.1,1000,legend=names(x)[-1],lwd=1:3,lty=1:7,col=1:5,pch="0123456789abcdefghi",bg="white")
dev.off();
```
1. Now plot most common dependencies:
```
# see R-code in overview/deps/plot.r
LNGS1="C Cs F Go JS PY R Rust Scala java jl pl rb"
for LA in $LNGS1; do scp -p $ver$LA.* da0:/data/play/plots; done
scp -p cmts$ver.lng1 cmts$ver.lng *N.trend *N.trendA da0:/data/play/plots/
```

