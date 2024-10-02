
```sh

conda activate custom_kraken2

cp ../bacteria/md5checksums.sh ./

bash -x md5checksums.sh |tee md5checksums.log

mkdir -p md5checksums
for s in `cat manifest2.txt`
do
    s_basename=`basename $s .fna.gz`
    
    s_dirname=`dirname $s`

    wget ${s_dirname}/md5checksums.txt -O md5checksums/${s_basename}.md5checksums.txt

    grep ${s_basename}.fna.gz md5checksums/${s_basename}.md5checksums.txt >> md5checksums/all.md5checksums.txt
done
```


```sh
md5sum --quiet -c all.md5checksums.txt |tee ../md5sum_failed.list

```





```sh

rm md5checksums -r

mkdir -p md5checksums
for s in `cat manifest2.txt`
do
    s_basename=`basename $s .fna.gz`
    
    s_dirname=`dirname $s`

    echo "${s_dirname}/md5checksums.txt" >> aria2c_md5sum.list

    echo " out=md5checksums/${s_basename}.md5checksums.txt" >> aria2c_md5sum.list

done
```


```sh

##################

aria2c -i aria2c_md5sum.list |tee aria2c_md5sum.log


for s in `cat manifest2.txt`
do
    s_basename=`basename $s .fna.gz`
    if [ -f "md5checksums/${s_basename}.md5checksums.txt" ]; then
        grep ${s_basename}.fna.gz md5checksums/${s_basename}.md5checksums.txt >> md5checksums/all_aria2c.md5checksums.txt
    fi    
done

cd ./md5checksums

cp ./all_aria2c.md5checksums.txt ../all

cd ../all

md5sum --quiet -c all_aria2c.md5checksums.txt |tee ../all_aria2c_md5sum_failed.list

sed -i 's/\.\///' ../all_aria2c_md5sum_failed.list
sed -i 's/: FAILED//' ../all_aria2c_md5sum_failed.list

cat ../all_aria2c_md5sum_failed.list |xargs rm

ss=""
for s in `cat ../all_aria2c_md5sum_failed.list`
do
    ss="$ss|$s"
done
egrep "`echo $ss |sed 's/|//'`" ../manifest2.txt > ../aria2c_re-download.txt

aria2c -i ../aria2c_re-download.txt 

## viral 95992  X
## archaea 8841 X
## bacteria 192945
## fungi 10335 X



##################

conda activate custom_kraken2

cp ../bacteria/md5checksums.sh ./

bash -x md5checksums.sh |tee md5checksums.log


##################

md5sum --quiet -c all.md5checksums.txt |tee ../md5sum_failed.list

```