# H2N issue — ID collision

## Briefly about H2N resource files
Resource files can be in any format as those presented in sub-dirs of `H2N/Config` folder.
Such as `.filter2, .filter2l, .fgroup2, .badge, .exst` and others.

Any resource files contain **name** and **unique ID** in the file's name.
For example, file with name `VPIP[-904665471].filter2l` has `ID: -904665471` and `name: VPIP`. 

Actually, those **name** and **ID** can be also found in the file content.
File content represents data serialized with `protobuf`.
On startup, H2N takes the name and ID from the file content,
not the **visible file name**.

So if you edited the name of the file in the Windows explorer,
the name and ID from the filename will not match name and ID from file content.
You will see just different names of the stats in H2N and Windows explorer.
That's not a big thing.  

### Nota Bene<br>

H2N allows you to encrypt files. After that process, files are **locked**, i.e. content is encrypted. 
If the file is locked we can't easily decrypt data inside the file.
The only way to check consistency of the data from locked files is the assumption
about the identical match of names and IDs in the filename and file content.

## Hash collision

Hash collision, to put it simply, is when identification numbers are the same for different objects.
Suppose we got `VPIP[-904665471].filter2l` and `PRF[-904665471].filter2l`.
Their ID **-904665471** is the same. Thus, H2N will see only one of these files.

## Some math
Resource files in H2N have ID as an integer numbers ranging from -2 147 483 648 to 2 147 483 647.
Total of 4 294 967 296 numbers. Pretty much for hundreds of thousands of files to be unique, yeah?

But what is the probability of this event:
**30K files were randomly generated and pasted to the other 50K randomly generated numbers?**

Let's make it easier. The probability that no collision occurred after inserting one file is 
`1 - 50000 / 4 294 967 2960 = 0.99998835`. In fact, we need to raise it to the power of 30K.
Then we got `0.99998835^30000 ~ 0.705`. There is a 30% probability that collision will occur.
The latter means that H2N will discard some
files without any warnings.

## Side-effects

The worst thing is that files with same ID are placed in one folder. 
Then, H2N will see during the session only one of that files. 
And if you edit and save changes to that file, H2N will delete others without any notification.
This won't happen if they are placed in separate folders.

## Results
If you work with a big amount(>10K) of resource files, you
may regularly face hash collision issues. Some resource files may be lost,
and replaced by other old versions, and erroneous data will be seen in HUDs or researches. 

It is undesirable behavioral on the part of H2N.
