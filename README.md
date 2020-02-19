# LDAP
### http://smlboby.blogspot.com/2016/03/ms-sql-server-ad.html
[OPENROWSET]
Step1 開啟 OPENROWSET 功能(已開啟則省略)
--開啟進階選項 1:開啟 / 0:關閉
sp_configure 'show advanced options',1
reconfigure

--開啟openrowset功能 1:開啟 / 0:關閉
sp_configure 'Ad Hoc Distributed Queries',1
reconfigure

Step2 T-SQL
   [Account] : domain\account, ex. ABC\John
   [Password] : account password
   [Domain] : domain name, ex. ABC.com
   ※LDAP必須大寫
select * from openrowset(
   'ADsDSOObject' --Provider
   ,'';'[Account]';'[Password]' --Data Source;DomainAccount;Password
   ,'select sn,givenname,mail from ''LDAP://[Domain]'' where sn=''*'' '
)

常用 AD 欄位
欄位名	說明
samaccountname / AD帳號
givenname / 名字
displayname / Fullname (姓+名)
department / 部門
title / 職稱
mail / Email
sn / 姓氏
company / 公司
location / 位置

