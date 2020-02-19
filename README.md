# LDAP
### Ref. http://smlboby.blogspot.com/2016/03/ms-sql-server-ad.html

## OPENROWSET
### Step1 Config OPENROWSET
```SQL
--Config advance options 1:OPEN / 0:CLOSE
sp_configure 'show advanced options',1
reconfigure
```

```SQL
--Config openrowset 1:OPEN / 0:CLOSE
sp_configure 'Ad Hoc Distributed Queries',1
reconfigure
```

### Step2 T-SQL
   [Account] : domain\account, ex. ABC\John
   [Password] : account password
   [Domain] : domain name, ex. ABC.com
   ※LDAP should be capital
```SQL
select * from openrowset(
   'ADsDSOObject' --Provider
   ,'';'[Account]';'[Password]' --Data Source;DomainAccount;Password
   ,'select sn,givenname,mail from ''LDAP://[Domain]'' where sn=''*'' '
)
```

## OPENQUERY
### Step1 add LinkServer
```SQL
EXEC master.dbo.sp_addlinkedserver @server = N'ADSI', @srvproduct=N'Active Directory Service Interfaces', @provider=N'ADSDSOObject', @datasrc=N'adsdatasource'
EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'ADSI',@useself=N'False',@locallogin=NULL,@rmtuser=N'Domain\AdminId',@rmtpassword='AdminPwd'
```

### Step2 T-SQL
```SQL
SELECT * FROM OpenQuery ( 
  ADSI, 
  'SELECT pager, displayName, telephoneNumber, sAMAccountName,mail, mobile, facsimileTelephoneNumber, department, physicalDeliveryOfficeName, givenname
  FROM  ''LDAP://Domain.com'' 
  WHERE objectClass =  ''User'' and samaccountname=''UserId''
  ') AS tblADSI
```

## AD Field Name
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


## Trobble Shooting

### Ref. https://stackoverflow.com/questions/28860601/cannot-set-password-with-directoryentry-invoke-when-user-is-created-in-ad-using
To allow an ASP.NET page to SET an AD Password on account creation, I had to run "Active Directory Users and Computers", right-click the domain, select "Delegate Control". This opens a wizard which will allow you to grant the account IIS_IUSRS permissions to make changes to AD.
