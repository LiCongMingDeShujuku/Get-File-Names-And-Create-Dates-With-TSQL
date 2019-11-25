![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 使用TSQL获取文件名并创建日期
#### Get File Names And Create Dates With TSQL
**发布-日期: 2018年02月05日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这是一些返回指定文件夹中文件的创建日期和文件名的快速逻辑。比如说我要找forder `C：\ SQLIMPORTS`。最初创建它是为了在自动ETL过程中使用。可以使用小优化，效果很好。


## English
Here’s some quick logic that will return both the Create Date, and File Name of your files in the designated folder. In this case I’m looking in forder `C:\SQLIMPORTS`. This was originally created to use in an automated ETL process. Could use alittle optimization, but works great.



---
## Logic
```SQL
-- get simple file list
-- 获取简单的文件列表
declare @path       varchar(255) = 'C:\SQLIMPORTS\'
declare @files      table ([subdirectory] varchar(255), [depth] int, [file] int)
insert into     @files exec master..xp_dirtree @path, 1, 1; delete from @files where [file] = 0 
 
if object_id('tempdb..#import_folder') is not null
drop table      #import_folder
create table        #import_folder  ([file_info] nvarchar(255))
declare         @file_list  table ([file_name] nvarchar(255))
 
-- get file meta data
-- 获取文件元数据
Insert into     #import_folder exec xp_cmdshell 'dir C:\SQLIMPORTS\*.*'
delete from     #import_folder where [file_info] is null 
delete from     #import_folder where [file_info] like '%dir%' 
delete from     #import_folder where [file_info] like '%volume%' 
delete from     #import_folder where [file_info] like '%bytes%'
 
-- combine meta data with file name
-- 将元数据与文件名组合
if object_id('tempdb..#files_found') is not null
drop table      #files_found
create table        #files_found ([create_date] datetime, [file_name] nvarchar(255))
 
declare @compile_list       varchar(max)
set @compile_list       = ''
select  @compile_list       = @compile_list + 
'insert into #files_found  ([create_date], [file_name]) select cast(left([file_info], 20) as datetime), ''' + [subdirectory] + ''' from #import_folder where [file_info] like ''%' + [subdirectory] + '%'';' + char(10)
from    @files
exec    (@compile_list)
 
select
    [create_date]
,   [file_name]
,   'file_type'     = upper(right([file_name], 3)) 
from
    #files_found order by [create_date] desc


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

