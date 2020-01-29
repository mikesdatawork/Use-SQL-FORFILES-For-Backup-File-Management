![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL & FORFILES For Backup File Management
**Post Date: July 19, 2018**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's yet another way to get your backup files (or any files) moved or deleted across multiple drives based on a duration (in number of days).

You can use Powershell, or Robocopy sure; but here is the same thing using TSQL. This was all placed into an Agent Job and tested.
Some might consider this as using multiple FORFILES or stacking FORFILES statements but it's a basic command shell operation packaged and executed from TSQL. Really helps smooth out your file automation processes.

There's a way this can be done with switches across command shell statements sure; but using a TSQL based solution and running that from XP_CMDSHELL means you can pull a list of paths from just about anywhere on any table, and create your own forfiles script from them.
In the example below you can see I simply created a table variable and inserted several FORFILES statements across different drives. 

There's no reason at all why this table variable can't be created based on what you might find under the BACKUP MEDIA FAMILY table and just using the physical device location and incorporating that path into the FORFILES script.

If you want to change the number of days to keep the file all you need to do is change the last 2 characters of the script from -0 to -30 or however long you intend to save keep the files on disk. The example below deletes the files immediately.</p>      

![ForFiles And SQL]( https://mikesdatawork.files.wordpress.com/2018/07/image001.png "Manage Files With ForFiles and SQL")
 


## SQL-Logic
```SQL
use master;
 
set nocount on
 
declare @forfiles table ([statements] varchar(255))
 
insert into @forfiles select '''FORFILES /p "K:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
insert into @forfiles select '''FORFILES /p "R:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
insert into @forfiles select '''FORFILES /p "T:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
insert into @forfiles select '''FORFILES /p "V:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
declare @command varchar(max)
 
set @command = ''
 
select @command = @command + 'exec master..xp_cmdshell ' + [statements] + char(10)
 
from @forfiles
 
exec (@command)
```

Using the same framework you can use just about any other command shell utility. In the example below I used the same logic to run some XCOPY commands to move the files around to other alternate backup folders.

```SQL
use master;
 
set nocount on
 
declare @xcopy table ([statement] varchar(255))
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "K:BACKUPS"'''
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "R:BACKUPS"'''
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "T:BACKUPS"'''
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "V:BACKUPS"'''
 
declare @command varchar(max)
 
set @command = ''
 
select @command = @command + 'exec master..xp_cmdshell ' + [statement] + char(10)
 
from @xcopy
 
exec (@command)
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)
    
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

