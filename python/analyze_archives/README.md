# What is in RePEc?

One of the RePEc's main assets is a collection of 1,900 archives contributed by members from 93 countires (http://repec.org/)

So what is exactly in these archives?

## 1- ReDIF templates
Get a copy of ReDIF data using rsync (http://rsync.repec.org/)

```bash 
rsync -va --delete rsync://rsync.repec.org/RePEc-ReDIF/ RePEc
```
The above command might takes more than 24 huors (for the first time). If you get a time-out error, the notes at the end of this section.

List all files in the ReDIF data and their size

```bash
ls RePEc -lR --block-size=1kB | grep '^-'  > ls_ReDIF.txt
```

### Analyze the data (in Python)
read the data
```python
import pandas as pd
data_path='../../../../data/ls_ReFID.txt'
df=pd.read_table(data_path,header=None)
```
clean the data
```python
df=df[:-1]
df['split']=df[0].apply(lambda x: x.split())
df['size']=df[0].apply(lambda x: x.split()[4]).astype(int)
df['ext']=df.split.apply(lambda x : x[-1].split('.')[-1])
```
aggregate by extension
```python
dg=df[['ext','size']].groupby('ext').agg(['count','sum'])
dg['size']['sum']=(dg['size']['sum']/1024/1024).round(1)
dg.columns=['Numver of files','Total size of files (GB)']
dg.sort_values([('Total size of files (GB)')],ascending=False).head(10)
```

### Results
|	| Numver of files 	| Total size of files (GB) |
|---|:---: 	| :---: |
| ext 	|  	|  	|
| pdf 	| 27,559 	| 19.9 	|
| mov 	| 1 	| 4.0 	|
| rdf 	| 701,973 	| 3.4 	|
| redif 	| 30,644 	| 1.7 	|
| mp3 	| 2 	| 1.0 	|
| others | 25,062 | 2.6 | 

There are roughly 730,000 ReDIF template files with the extention of .rdf or .redif (~5 GB). In addition, there are 27,000 PDF files (~20 GB), a few media files (~5 GB), and other files (txt, doc, zip, etc - ~3 GB).

\* The one mov file for 4GB might result in a time-out error in rsync. You cac exclude the file with --exclude option. If you are only interested in the templates, use the following rsync command:

```bash
rsync -va --include="*/" --include="*.rdf" --include="*.redif" --exclude="*"  --delete rsync://rsync.repec.org/RePEc-ReDIF/ rdf
```

to be continued ... !
