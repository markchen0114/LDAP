# LDAP
### Reference http://smlboby.blogspot.com/2016/03/ms-sql-server-ad.html

## OPENROWSET
### Step1 開啟 OPENROWSET 功能
```javascript
--開啟進階選項 1:開啟 / 0:關閉
sp_configure 'show advanced options',1
reconfigure
```

```javascript
--開啟openrowset功能 1:開啟 / 0:關閉
sp_configure 'Ad Hoc Distributed Queries',1
reconfigure
```

### Step2 T-SQL
   [Account] : domain\account, ex. ABC\John
   [Password] : account password
   [Domain] : domain name, ex. ABC.com
   ※LDAP必須大寫
```javascript
select * from openrowset(
   'ADsDSOObject' --Provider
   ,'';'[Account]';'[Password]' --Data Source;DomainAccount;Password
   ,'select sn,givenname,mail from ''LDAP://[Domain]'' where sn=''*'' '
)
```

## OPENQUERY
### Step1 新增LinkServer
```javascript
EXEC master.dbo.sp_addlinkedserver @server = N'ADSI', @srvproduct=N'Active Directory Service Interfaces', @provider=N'ADSDSOObject', @datasrc=N'adsdatasource'
EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'ADSI',@useself=N'False',@locallogin=NULL,@rmtuser=N'Domain\AdminId',@rmtpassword='AdminPwd'
```

### Step2 T-SQL
SELECT * FROM OpenQuery ( 
  ADSI, 
  'SELECT *
  FROM  ''LDAP://Domain.com'' 
  WHERE objectClass =  ''User'' and samaccountname=''UserId''
  ') AS tblADSI


## 常用 AD 欄位
| Feild Name      | Description                       |
| --------------- | --------------------------------- |
| samaccountname  | Account id                        |
| givenname       | Given name                        |
| displayname     | Fullname (Last Name + First Name) |
| department      | Department                        |
| title           | Title                             |
| mail            | Email                             |
| sn              | Last Name                         |
| company         | Company                           |
| location        | Location                          |

