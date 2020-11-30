# Filling gaps in TROPOS archive after delivery


**Note:** the actual archive is at

```/vols/altair/datasets/eumcst/msevi\_pzs/l15.hrit/(year)```

and

```/vols/altair/datasets/eumcst/msevi\_rss/l15.hrit/(year)```

The filling process assumes that you have copied and extracted data from
tape or HTTP into a unique directory under:

```/vols/altair/datasets/eumcst/incoming/umarf/http/(year)```

Now the following tasks need to be completed:

1.  Add data to archive
2.  Update segment masks
3.  Re-create gap info

For each of these steps, you need to log onto server altair (see Step 4
of II.a)

In addition, you need to set the path to include the Anaconda python
environment by using the following command:

```export PATH=/vols/talos/local/anaconda/bin:\$PATH```

**Note:** GNU Screen

As the steps take significant time, it is convenient to run sessions in
GNU Screen, so you can log out of the computer and later resume
sessions. You may look up
[*http://nathan.chantrell.net/linux/an-introduction-to-screen/*](http://nathan.chantrell.net/linux/an-introduction-to-screen/) for an introduction on GNU Screen.

### 1. Adding data to the archive

```fmcast\_ms15\_update.py \$DIR1 \... \$DIRN &```

Example:

```cd /vols/altair/datasets/eumcst/incoming/umarf/http/2016```

```nohup fmcast\_ms15\_update.py /vols/altair/datasets/eumcst/incoming/umarf/http/2016/\* &```

This command traverses each directory tree. For each tarfile containing
HRITs, it checks whether a corresponding tarfile is already in the
archive, and whether it is complete. If it is incomplete, the missing
HRIT files are added otherwise a new tarfile is created in the archive.

Afterwards you will have to clear the directory:

```rm \*.tar```

### 2. Updating the segment masks

This doesn\'t have to be done every time. It is enough to do it after a
significant chunk of data is added.

Issue the command to update the segment mask e.g. for the year 2013 and
the Rapid-Scan-Service (\'rss\', for Prime-Service use \'pzs\'):

```fmcast\_ms15\_segmask.py -A -y 2013 -s \'rss\'```

You can also specify the date interval for a month (here: Jan 2013):

```fmcast\_ms15\_segmask.py -A -m 2013-01 -s \'rss\'```

A specific date (here: 1^st^ Jan 2013):

```fmcast\_ms15\_segmask.py -A -d 2013-01-01 -s \'rss\'```

A date period (equivalent to -m 2013-01):

```fmcast\_ms15\_segmask.py -A -d 2013-01-01,2013-02-01 -s \'rss\'```

**Note:** this step is quite time-consuming, as it obtains the list of
files from each tarfile. Using nohup is recommended.

### 3. Obtaining statistics/gap information**

To find gaps for a year, do the following:

```cat /vols/altair/datasets/eumcst/msevi\_rss/meta/segmasks/2013/??/\*.segmask \| sort \| \\ fmcast\_ms15\_gaps.py -y 2013 \> gaps-2013-rss.txt```

This will produce output as follows:
```
2014-01-01 10:20 2014-01-02 10:45 rss 294

2014-01-14 09:00 2014-02-13 09:00 rss 8641
```
To split the file in large gaps, use the following command:

```cat gaps-2013-rss.txt \| awk \'{if(\$6\>=10)print}\' \| less \> gaps-2013-rss-long.txt```

Small gaps can be viewed with:

```cat gaps-2013-rss.txt \| awk \'{if(\$6\<10)print}\' \| less \> gaps-2013-rss-short.txt```

To create PDFs to print out and put in the sat-archiving file, use:

```enscript -p gaps-2013-pzs-long.ps gaps-2013-pzs-long.txt```

ps2pdf gaps-2013-pzs-long.ps gaps-2013-pzs-long.pdf

Afterwards you can remove the ps-files

It\'s also possible to obtain summary statistics for a year:

```cat /vols/altair/datasets/eumcst/msevi\_rss/meta/segmasks/2013/??/\*.segmask \| sort \| \\ fmcast\_ms15\_stats.py -y 2013```

This will produce output as follows:
```
\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

\# Yearly reception statistics: 2013

\# Date \#Files \[%\] \#Slots \[%\] \#Compl. \[%\]

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
2013-01-01 12672 100.00 288 100.00 288 100.00

\...

2013-12-31 12660 99.91 288 100.00 284 98.61

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

Combined 3951734 85.44 89859 85.48 89375 85.02

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
```
