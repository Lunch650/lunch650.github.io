# Bugku CTF WriteUp

Bugku网站有比较全的[CTF题目](https://ctf.bugku.com/)。用来刷题练习并借此熟悉Kali的工具

## 0x01 Misc

1. 签到题

  没啥好说的，直接关注公众号。

2. 这是一张单纯的图片

  下载题目中提供的文件，是一张萌萌的图片。

  ![单纯的图片](../imgs/Bugku/Misc/gnome-shell-screenshot-W6U3XZ.png)

  kali中自带了binwalk,直接binwalk分析一波，没有异常。

  ![binwalk分析](../imgs/Bugku/Misc/gnome-shell-screenshot-IQO3XZ.png)

  考虑查看图片编码内容，kali没有比较方便的编辑器，到[官方下载](http://www.sweetscape.com/download/010editor/)010editor([使用教程](https://zhuanlan.zhihu.com/p/31195150))

  安装好之后打开图片，看到文件末尾有些奇怪的字符像html编码

  ![图片结尾处字符串](../imgs/Bugku/Misc/gnome-shell-screenshot-YWT0XZ.png)

  随手使用kali自带的burpsuite翻译，拿到flag
  ![burpsuite解码](../imgs/Bugku/Misc/gnome-shell-screenshot-VCLQXZ.png)  


3. 隐写

  下载题目提供图片，打开报错，kali自带的编辑器还特别贴心的告诉了你是IHDR部分的CRC校验不过。

  使用010editor打开，IHDR这部分包含了长度宽度的值，把高的值修改成和宽一致后再打开拿到flag。010editor真是神器，下  载特定格式文件的分析插件后，可以直接在下面框框内修改内容。以前都是需要在Edit->Insert/Overwrite/里面修改或更新十 六进制内容，修改前还要找修改的内容在哪个地方。

  ![修改长宽高](../imgs/Bugku/Misc/gnome-shell-screenshot-V3IQXZ.png)   

4. telnet

  下载了文件是一个pcap包，大致看了一下是一个telnet通讯的过程~~废话，题目都说了好吗~~

  跟踪一下TCP流(telnet协议也是TCP/IP中的一种协议)。

  ![跟踪TCP流](../imgs/Bugku/Misc/gnome-shell-screenshot-U5KPXZ.png)

  额。。   

5. 眼见非实

  下载题目提供压缩包，发现里面绝大多数是一些xml配置文件，翻到某个文件看到了flag。

  ![眼见非实](../imgs/Bugku/Misc/gnome-shell-screenshot-08O5XZ.png)

  对于这种题目，因为在之前的考试中也遇到过，是否应该考虑写一个遍历字符串的脚本来处理呢？
  // TODO

6. 啊哒

  下载题目提供压缩包，里面有一个图片

  ![啊哒图片](../imgs/Bugku/Misc/gnome-shell-screenshot-DGTTXZ.png)

  常规操作，binwalk走几步

  ![啊哒binwalk分析](../imgs/Bugku/Misc/gnome-shell-screenshot-CRR3XZ.png)  

  发现文件末尾有压缩包，使用010editor把末尾内容复制出来另存为zip格式，压缩包里面有flag文件。但是压缩包是加密的，本想暴力破解但是比较难猜，回头发现这个题目的第一张图，相机位置有可疑代码。解码后拿到解压密码，顺利解题。

  ![啊哒解压密码](../imgs/Bugku/Misc/gnome-shell-screenshot-TW2SXZ.png)

7. 又一张图片，还单纯吗

  下载题目图片，binwalk看一下，就知道是来搞事情的。

  ![还单纯binwalk分析](../imgs/Bugku/Misc/gnome-shell-screenshot-RESWXZ.png)

  看这个也不知道怎么下手，直接使用010editor。真的，这个软件的分析插件可以吹爆，太好用了，完全适合解决这类CTF题目。

  ![还单纯010分析](../imgs/Bugku/Misc/gnome-shell-screenshot-08C1XZ.png)  

  可以从图片中看到，最后一段是未知填充。其实这段未知填充和上一段结束接着的地方是FFD9FFD8。直接导出这段Unknow，另存为jpg，于是拿到Flag.

  ![还单纯Flag](../imgs/Bugku/Misc/gnome-shell-screenshot-32GWXZ.png)  


8. 猜

  下载图片，发现是女神，答案填写flag{liuyifei}

9. 宽带信息泄露

  经过查看其它答案，下载的文件应该是一个路由器保存的配置，需要使用RouterPassView软件，暂时身边没有windows系统的。以后再补。

10. 隐写2

  下载文件是一个图片
  ![隐写2文件](../imgs/Bugku/Misc/20190519122253-816x643.png)

  下载文件是一个图片
  ![隐写2文件](../imgs/Bugku/Misc/20190519122253-816x643.png)

  binwalk走一波

  ![隐写2第一次binwalk](../imgs/Bugku/Misc/gnome-shell-screenshot-8H7F2Z.png)

  看到文件里面似乎躲藏了东西。同时解锁了新技能，使用binwalk的命令`-e`分离文件内容

  `binwalk -e Welcome_.jpg`

  ![隐写2binwalk分离原图片](../imgs/Bugku/Misc/gnome-shell-screenshot-MNNG2Z.png)

  分离出了一个图片提示以及flag.rar文件

  ![隐写2查看提示](../imgs/Bugku/Misc/20190519122807-816x643.png)  

  同时binwalk查看flag文件内容

  ![隐写2查看flag文件内容](../imgs/Bugku/Misc/20190519122842-736x491.png)

  是一个zip文件，打开后发现是加密的。下载了解密软件rarcrack进行破解(记得把文件后缀从rar改成zip)。**这个软件是专门用来破解压缩文件的，想起来当年居然自己还要自己写脚本跑**

  ![隐写2暴力破解flag文件内容](../imgs/Bugku/Misc/gnome-shell-screenshot-A6QL2Z.png)

  解密后又是一张图片

  ![隐写2暴力破解flag文件内图片](../imgs/Bugku/Misc/gnome-shell-screenshot-ZB691Z.png)  

  使用binwalk看没什么问题。使用Linux自带的16进制编辑器查看文件16进制内容，发现末尾有奇怪。**使用了默认自带的16进制软件xxd**

  ![隐写2暴力破解flag文件内图片末尾](../imgs/Bugku/Misc/gnome-shell-screenshot-DMCH2Z.png)

  看来是一个base64加密的，解密后拿到flag.

11. 多种方法解决

  下载来是一个KEY.exe文件，使用binwalk无法分析。直接cat看看情况，发现是一个图片。

  ![多种方法解决查看文件内容](../imgs/Bugku/Misc/gnome-shell-screenshot-IK821Z.png)

  使用网上现成的base64转图片拿到二维码，扫描后得到flag。

12. 闪得好快

  打开题目是一个不停变换的gif二维码图片。

  ![闪得好快图片内容](../imgs/Bugku/Misc/masterGO.gif)

  需要分析二维码图片就需要下载一个gif分析工具，叫做stegsolve。因为这个工具kali里面没有，所以先执行以下操作进行安装。

  ```
  wget http://www.caesum.com/handbook/Stegsolve.jar -O stegsolve.jar

  chmod +x stegsolve.jar
  ```

  安装好以后可以看到执行命令的目录下面已经有stegsolve.jar文件了。这时候使用命令打开文件
  `java -jar stegsolve.jar`

  软件打开后首先加载文件，然后使用Analyse->frame Browser就可以一帧一帧的二维码图了。把这18张图片的内容都扫描出来，就是答案了。

13. 白哥的鸽子:

  下载来一张图片。binwalk看了一下没什么特别的，用xxd看看16进制，发现末尾有古怪。

  ![白哥的鸽子_图片内容](../imgs/Bugku/Misc/gnome-shell-screenshot-XQL81Z.png)

  JPEG图片开头是ffd8，末尾应该是ffd9.后面多出来一些内容，先把16进制转换成字符串

  ![白哥的鸽子_解码](../imgs/Bugku/Misc/gnome-shell-screenshot-W0M11Z.png)

  按经验判断是一个栅栏密码，上网解码得到flag

  ![白哥的鸽子_栅栏密码解密](../imgs/Bugku/Misc/gnome-shell-screenshot-RSTD2Z.png)

14. linux

  这个题目有几种解法。

  解法一：

  下载到文件后，用binwalk分析一波，看到好像是由几个文件组成的。于是使用binwalk -e的命令将文件都分解出来，得到一个flag文本。里面就是flag

  解法二:

  下载到文件后使用`strings flag | grep flag`找其中的flag内容。

15. 隐写3

  下载来是一个图片。打开后提示CRC校验码出错，考虑是宽高被改动过了无法显示。这边有个小提示，windows下会忽略CRC校验码错误，显示图片。但是linux下会因为CRC校验码出错无法显示。

  使用系统自带工具hexeditor打开，17~20字节00 00 02 A7是宽，21~24字节00 00 01 00是高，30~33字节6D 7C 71 35是CRC校验码。

  ![隐写3图片的16进制](../imgs/Bugku/Misc/gnome-shell-screenshot-D30H2Z.png)

  随便修改了一下高度，图片还是无法显示(如果是windows系统里面，修改就会正常显示了)。于是在网上找了现成的计算代码，运行后会重新生成一个正常的图片。
  ```
import zlib
import struct
#读文件
file = 'dabai.png'
fr = open(file,'rb').read()
data = bytearray(fr[12:29])
crc32key =  0x6D7C7135 #需要自己填写30～33的16进制校验码
#crc32key = eval(str(fr[29:33]).replace('\\x','').replace("b'",'0x').replace("'",''))
n = 4095 #理论上0xffffffff,但考虑到屏幕实际，0x0fff就差不多了
for w in range(n):#高和宽一起爆破
    width = bytearray(struct.pack('>i', w))#q为8字节，i为4字节，h为2字节
    for h in range(n):
        height = bytearray(struct.pack('>i', h))
        for x in range(4):
            data[x+4] = width[x]
            data[x+8] = height[x]
            #print(data)
        crc32result = zlib.crc32(data)
        if crc32result == crc32key:
            print(width,height)
            #写文件
            newpic = bytearray(fr)
            for x in range(4):
                newpic[x+16] = width[x]
                newpic[x+20] = height[x]
            fw = open(file+'.png','wb')#保存副本
            fw.write(newpic)
            fw.close
  ```
  ![隐写3的FLAG](../imgs/Bugku/Misc/gnome-shell-screenshot-22201Z.png)


## 0x02 WEB

1. web2

  查看网页源代码，搞定。

2. 计算器

  登陆框只能填入一个数，无法填入完整答案。查看网页源代码修改字数限制，完成。

3. Web基础GET

  浏览器地址栏构造相应内容，完成。

4. Web基础POST

  我在burpsuite里面构造了没有效果，自己用python写了requests的简单脚本执行就成功了。奇怪。

5. 矛盾

  给了一个算法
  ```
  $num=$_GET['num'];
  if(!is_numeric($num))
  {
    echo $num;
    if($num==1)
    echo 'flag{**********}';
  }
  ```
  看题目的意思是，需要输出flag，那么需要经过两个判断：

  第一个判断是is_numberic($num)，是要求$num不能是数字类型。

  第二个判断是if($num==1)这个判断是要求$num等于1.

  所以num的值又要是等于1又要不是数字类型。

  有意思的是，根据php的特性，如果是通过'=='来判断等式两边是否相等，那么两边是可以相同的值而不是相同类型，例如if(1==‘1’)是对的。而if(1==='1')是不对的。

  这道题可以把$num赋值为1开头的任意字符，例如1fdsfsdfsf，那么就能通过两个判断。
  //todo 这是为什么呢?

6. web3

  打开页面是一个无聊的无限弹窗,ctrl+u查看到源代码后解码,完毕.

7. 域名解析

  > 听说把 flag.baidu.com 解析到123.206.87.240 就能拿到flag

  直接修改/etc/hosts文件就行了

8. 你必须让他停下

  页面里面有段js是让网页不停的自动刷新.设置浏览器不允许js,然后手动刷新几次界面就看到flag.

9. 本地包含

  ```
  <?php
      include "flag.php";
      $a = @$_REQUEST['hello'];
      eval( "var_dump($a);");
      show_source(__FILE__);
  ?>
  ```
  实际上就是要构造内容传递到eval语句中，将'var_dump('这部分进行闭合。所以可以构造hello的值为);show_source(%27%flag.php27);//
  这个题目最主要的就是有一句eval，var_dump函数无所谓是什么，换成md5函数都是可以的。有点类似SQL注入。

10. 变量1

  ```
  flag In the variable !
  <?php  
  error_reporting(0);
  include "flag1.php";
  highlight_file(__file__);
  if(isset($_GET['args'])){
      $args = $_GET['args'];
      if(!preg_match("/^\w+$/",$args)){
          die("args error!");
        }
        eval("var_dump($$args);");
      }
  ```
  粗略看下来这道题目和上一道题目有点像，但是其中多了点花样。一是新加了正则表达式对输入的参数进行过滤，不能够加入特殊符号除字母。二是`eval("var_dump($$args);");`这一句其中的参数有两个$符号。$$args可以理解成$($args)，比如$args='flag',那么$$args=$flag。
  因此考虑到全局变量$GLOBAL中可能包含了flag的信息，因此构造成`?args=GLOBALS`

11. web5

  打开页面是一个输入框，提示是`JSPFUCK??????答案格式CTF{******}`，之前知道有一个JSFUCK，不知道还有JSPFUCK。。。直接F12看了下源代码，发现熟悉的`)[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([![]]+[][[]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(+![]+[![]]+([]+[])[([][(![]+[])[+[]]+([![]]+[][[]])[+!+`内容。但是由于这部分内容太多了，F12查看不完整，于是直接右键点击查看源代码拿到全部内容，放到consolo里面运行，拿到flag.

12. 头等舱

  打开页面什么也没有，看看头文件发现flag。

13. 网站被黑

  打开页面发现一个炫酷页面
  ![网站被黑webshell页面](../imgs/Bugku/Misc/gnome-shell-screenshot-IX8R3Z.png)
  说明这个网站已经被黑，页面提示webshell，考虑到存在大马页面，使用kali自带的dirb工具扫描其他页面(这个工具其实蛮好用的)。发现shell页面
  ![网站被黑shell页面](../imgs/Bugku/Web/gnome-shell-screenshot-Q6KP3Z.png)
  看来是需要爆破了，使用burpsuite一波弱口令破解拿到flag。

14. 管理员系统

  首先有一个登陆框，尝试登陆时提示IP受限。F12查看源代码还得到一个BASE64字符串，解码为test123.
  ![管理员系统](../imgs/Bugku/Web/gnome-shell-screenshot-GRLJ3Z.png)

  获得以上信息后首先解决IP受限问题，修改X-Forwarded-For为127.0.0.1，后尝试以test123,test123登陆，提示密码错误。于是把用户名修改为admin，密码为test123成功登陆。

15. Web4

  打开页面提示看源代码，发现两个js变量p1,p2.
  ![Web4](../imgs/Bugku/Web/gnome-shell-screenshot-KKWC3Z.png)
  利用浏览器的Console将两个变量使用unescape函数解析出来，组合成一组字符串。填入框中得到flag。

16. flag在index里

  根据查看首页、show.php、以及首页上的超链接，考虑到是一个文件包含漏洞。
  ![flag在index里1](../imgs/Bugku/Web/gnome-shell-screenshot-LZIB3Z.png)

  因此按照套路构造协议url为xxx:/post/?file=php://filter/read=convert.base64-encode/resource=./index.php，拿到index.php的base64码
  ![flag在index里2](../imgs/Bugku/Web/gnome-shell-screenshot-2XJS3Z.png)

  解码以后得到flag.

17. 输入密码查看flag

  一个简单的爆破题目，提示了密码是5个数字。不详细写了。

18. 点击一百万次

  提示查看js，发现要求变量clicks到达100000次才出现flag.于是在console中输入clicks=10000，再次点击中间图案得到flag.

19. 备份是个好习惯

  题目提示备份,使用dirb工具查找后缀名为.bak的文件,得到index.php.bak.分析页面内容(后面注释是我添加的)
  ```
  <?php
  include_once "flag.php";
  ini_set("display_errors", 0);
  //获得URL中包含?之后的字符串
  $str = strstr($_SERVER['REQUEST_URI'], '?');
  //去掉?
  $str = substr($str,1);
  //替换到字符串中的key。但是很明显的漏洞是可以通过构造kkeyey绕过。
  $str = str_replace('key','',$str);
  //将$str变量中key1=a&key2=b的内容转换为$key1=a;$key2=b;
  parse_str($str);
  echo md5($key1);

  echo md5($key2);
  //key1与key2的md5值要相等，但是key1和key2的值不相等
  if(md5($key1) == md5($key2) && $key1 !== $key2){
      echo $flag."取得flag";
    }
  ?>
  ```
  通过以上代码分析，构造url比较简单，而md5那个地方以前遇到过，是md5和sha1都存在的一个漏洞，因为这两个函数无法解析数组,md5(key1[])会返回false，因此可以通过构造key1[]=a&key2[]=b解决。
  最终构造URL:?kekeyy1[]=a&kkeyey2[]=b

20. 成绩单

  一道简单的注入题目。不详细写了。

21. 秋名山老司机

  一道似曾相似的题目。页面中给出了一个超长计算式，需要迅速计算并把结果post到页面中。并且页面有中英文两种版本。
  ![秋名山老司机](../imgs/Bugku/Web/gnome-shell-screenshot-3VIB3Z.png)

  ![秋名山老司机](../imgs/Bugku/Web/gnome-shell-screenshot-7CHG3Z.png)

  仔细观察，知道算式是放在一个div里面。于是写出脚本拿到flag：
  ```
  import requests
  from bs4 import BeautifulSoup

  url = 'http://123.206.87.240:8002/qiumingshan/'
  req =  requests.Session()
  r_page1 = req.get(url)
  soup = BeautifulSoup(r_page1.content,'lxml')
  result = eval(soup.body.div.text[:-3])
  print(result)
  result = {'value': result}
  r_page2 = req.post(url=url,data=result)
  print(r_page2.content)
  ```
  需要注意的是，接受页面和提交结果都应该放在一个Session下来做，所以用了`requests.Session()`

22. Web6

  从原页面头中得到一个flag参数，里面都是base64码

  ![Web6](../imgs/Bugku/Web/gnome-shell-screenshot-WELS3Z.png)

  解码以后又拿到一个base64码

  ![Web6](../imgs/Bugku/Web/gnome-shell-screenshot-ZMQN3Z.png)
  再解码是一串数字。因为base64码不是不变的，于是写脚本提交数字，拿到flag.
  ```
  import requests
  import base64

  url = 'http://123.206.87.240:8002/web6/'
  req =  requests.Session()
  r = req.get(url)
  page1_result = base64.b64decode(r.headers['flag'])
  payload = str(page1_result).split(':')[1]
  payload = base64.b64decode(payload)
  data = {'margin': payload}
  r2 = req.post(url=url,data=data)
  print(r2.content)
  ```

23. cookies欺骗

  呼，感觉者这几题都是需要写代码，好麻烦。
  页面打开就发现URL不平常，包含了两个参数分别是line和filename
  解析字符串a2V5cy50eHQ=发现是keys.txt的base64码。于是把index.php进行base64编码后替换filename的值，再对代表行数的line不停的赋值1，2，3，4。。。,就得到了index.php文件的源代码。

  ```
  error_reporting(0);
  $file=base64_decode(isset($_GET['filename'])?$_GET['filename']:"");
  $line=isset($_GET['line'])?intval($_GET['line']):0;
  if($file=='') header("location:index.php?line=&filename=a2V5cy50eHQ=");
  $file_list = array(
    '0' =>'keys.txt',
    '1' =>'index.php',
    );
    if(isset($_COOKIE['margin']) && $_COOKIE['margin']=='margin'){
      $file_list[2]='keys.php';
    }
    if(in_array($file, $file_list)){
      $fa = file($file);
      echo $fa[$line];
    }
  ?>
  ```
  看来是需要在COOKIE中加入margin。并且filename的文件名改成keys.php的base64码
  ![cookies欺骗](../imgs/Bugku/Web/gnome-shell-screenshot-3JS82Z.png)

24. never give up

  打开页面没什么内容，F12查看源代码得到一个页面地址1p.html
  ![never_give_up](../imgs/Bugku/Web/gnome-shell-screenshot-90RC3Z.png)  
  但是输入地址后浏览器就自动跳转了，使用burpsuite查看，得到1p.html中的一大段base64码.
  ![never_give_up](../imgs/Bugku/Web/gnome-shell-screenshot-SJJQ3Z.png)

  解密后就看到有点复杂的逻辑，但是不用管它。直接发现有个f4l2a3g.txt文件，访问后拿到flag
  ![never_give_up](../imgs/Bugku/Web/gnome-shell-screenshot-ZKZA3Z.png)

25. welcome to bugkuctf

  有意思的一道题，介绍了CTF题目中常用两个的PHP伪协议(为什么总是php??)。

  ![welcome_to_bugkuctf](../imgs/Bugku/Web/gnome-shell-screenshot-XMXG4Z.png)

  可以看到页面中给了这样的提示：
  ```
  <!--  
  $user = $_GET["txt"];  
  $file = $_GET["file"];  
  $pass = $_GET["password"];  

  if(isset($user)&&(file_get_contents($user,'r')==="welcome to the bugkuctf")){  
      echo "hello admin!<br>";  
      include($file); //hint.php  
      }else{  
        echo "you are not admin ! ";  
      }  
  -->
  ```
  提示内容很简单，if判断语句是要保证两个条件同时满足：一是URL中传递的txt值不能为空。二是我一开始认为txt值应该是一个文件名称，这样的话file_get_contents函数读取txt名称的文件，而文件中内容恰好是'welcome to the bugkuctf'。

  后来发现我对第二个条件的认识是错误的。我用dirb扫描没有找到其他文件的存在，也就是说不存在可以通过file_get_contents函数读取的文件。

  其实file_get_contents不仅仅可以读取文件，还可以通过一个伪协议file_get_contents(php://input)读取post里面的内容。

  ![welcome_to_bugkuctf](../imgs/Bugku/Web/gnome-shell-screenshot-Q0PY3Z.png)

  可以看到返回内容已经做出改变，if判断里面的条件我们已经满足(虽然这里还有一个小问题，就是现在页面返回的内容是hello friend，和注释中给我们的you are not admin不一致。说明index.php文件还有内容是注释里面没有说明的)。现在来看第二个要求，就是`include($file)`这句，他是要求包含hint.php文件。但如果只是构造file=hint.php不会直接给出hint.php源文件(为什么？),需要利用php://filter伪协议将hint.php文件内容转化为base64(也是CTF题目中最常用的方法)。

  于是构造`file=php://filter/read=convert.base64-encode/resource=hint.php`

  ![welcome_to_bugkuctf](../imgs/Bugku/Web/gnome-shell-screenshot-43O33Z.png)

  解码后：
  ```
  /*hint.php*/
  <?php  

  class Flag{//flag.php
      public $file;  
      public function __tostring(){  
          if(isset($this->file)){  
              echo file_get_contents($this->file);
			        echo "<br>";
		          return ("good");
                 }  
               }  
             }  
  ?>    
  ```
  还有点复杂的感觉。以上代码告知了这么一个情况:有个flag.php文件，里面有一个Flag类，`__tostring`方法说明当打印Flag实例的时候，会输出Flag实例中的$file变量值。

  先尝试直接打印flag内容，发现不行
  ![welcome_to_bugkuctf](../imgs/Bugku/Web/gnome-shell-screenshot-33EA4Z.png)

  还记得之前说过index.php文件还有内容我们没有发现吗，构造`file=php://filter/read=convert.base64-encode/resource=index.php`查看一下index.php文件内容。
  ```
  <?php  
  $txt = $_GET["txt"];  
  $file = $_GET["file"];  
  $password = $_GET["password"];  

  if(isset($txt)&&(file_get_contents($txt,'r')==="welcome to the bugkuctf")){  
      echo "hello friend!<br>";  
      if(preg_match("/flag/",$file)){
		      echo "ä¸è½ç°å¨å°±ç»ä½ flagå¦";
          exit();  
        }else{  
            include($file);   
            $password = unserialize($password);  
            echo $password;  
          }  
          }else{  
            echo "you are not the number of bugku ! ";  
          }  
  ?>
  ```
  很明显，第二个if函数说明了file参数不能包含flag字符。结合hint.php文件中的内容说明一下，$password应该是Flag实例序列化后的字符串，因此反序列化$password(就是将已经被序列化为$password字符串的对象复原)后，echo $password就会调用Flag类中的__toString方法，而__toString方法是打印类中的file属性值。目前就是需要打印出flag.php页面，所以我们把实例的file值等于'flag.php'

  第一步，构造Flag类，Flag类的file属性等于flag.php，并将其实例化
  ![welcome_to_bugkuctf](../imgs/Bugku/Web/gnome-shell-screenshot-J0J73Z.png)

  第二步，在index.php页面构造url为`?txt=php://input&file=hint.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}`

  ![welcome_to_bugkuctf](../imgs/Bugku/Web/gnome-shell-screenshot-TAID4Z.png)

  需要注意的是此时不需要对hint.php文件进行base64编码。因为编码的目的是为了展示页面源代码，现在的目的不用展示，是把Flag类的定义进行引用。

26. 字符？正则？

  又到了一年一度的“为什么我怎么总是学不会正则表达式？？为什么一学就忘”的无能狂怒时间。
  ![正则](../imgs/Bugku/Web/gnome-shell-screenshot-KUJ33Z.png)

  以前做过这道题，但是好像做错了。

  一个个来吧。

  1. 首先看到整个结构一头一尾是被`/格式/i`包裹的，这个i就代表了中间格式内容不区分大小写。
  2. key.* 前面key就是要求构造时候要以key开头，句号`.`代表除了换行符以外任意1个字符的匹配，星号`*`代表匹配0次或多次任意字符。所以构造`?id=keyaakey`
  3. .{4,7}key 前面的部分`.{4,7}`代表对`.`重复匹配4~7次。目前是`?id=keyaakeybbbbkey`
  4. :\\/.\\/(.* key) `\/`部分是指匹配`/`，`\`是用于转义。所以当前构造`?id=keyaakeybbbbkey:/c/dkey`
  5. [a-z][[:punct:]] 第一个是任意a-z英文字母，后面框框是指任意符号。最后构造的结果是：`?id=keyaakeybbbbkey:/c/dkeye-`，得到flag

27. 前女友

  根据源代码的提示看到code.txt内容。
  ![正则](../imgs/Bugku/Web/gnome-shell-screenshot-SE6C4Z.png)

  这个一看就知道是利用md5函数无法计算数组以及strcmp(参数1,参数2)(是比较两个参数字符串长度)中，无法比较数组的特点来构造。构造一下url参数拿到flag.

  `?v1[]=a&v2[]=b&v3[]=c`

28. login1

  页面提示`SQL约束攻击`，点进去看到是一个普通的登陆页面和注册页面。
  ![login1](../imgs/Bugku/Web/gnome-shell-screenshot-9BVJ4Z.png)

  尝试注册admin时候提示已经注册过.
  ![login1](../imgs/Bugku/Web/gnome-shell-screenshot-XMFN4Z.png)

  根据提示找了一下SQL约束攻击的大概意思。比如当前的条件是：我创建了一个表，表里面的username字段格式是varchar(25)，而且当前录入了`admin`进去。这时候如果我再注册admin，就会提示我admin已存在。但是如果我注册了一个用户`admin   (20个空格)a`那么数据库在判断的时候首先会认为`admin   (20个空格)a`和`admin`不是一样的内容，然后把`admin   (20个空格)a`前面25个字符截断后存入。这时候就能用新注册的那个密码覆盖掉以前的密码。就完成了所谓的`SQL约束攻击`。最终拿到flag.

29. 你从哪里来

  看题目就知道是一个考察referer用法的，比较简单。只是注意一下一般都要写成`http://xxx.com`这样的格式。

30. md5 collision

  题目上来就要求给一个a的值`please input a`。在URL后面构造?a=1后页面提示`false`。在网上找到这个题目页面的源代码(为什么给我的题目里面没有源代码？)

  ```
  <?php
  $md51 = md5('QNKCDZ0');
  $a = @$_GET['a'];
  $md52 = @md5($a);
  if(isset($a)){
    if ($a != 'QNKCDZO' && $md51 == $md52) {
      echo "nctf{*****************}";
      } else {
        echo "false!!!";
        }
      }
        else{echo "please input a";}
        ?>
  ```
  可以看到要求是a的值不能等于`QNKCDZ0`，但是`QNKCDZ0`的MD5值要和a的MD5值相等。这个题目和之前我们做的比较两个变量md5值得题目不一样的地方在于：之前的题目我们可以控制两个比较的变量，这个题目只能控制一个。于是我们先看看`md5('QNKCDZ0')`的值是`0e8304004519934940580242199033910`，好的有搞头了，因为php这个伟大的语言会把0e开头的哈希字符串认为是0，所以当md5函数计算出来的值是以0e开头的，那么PHP就会把它认为是0了(提示:MD5就是进行hash运算)。所以我们只需要把a的值构造成一个md5值开头为0e的字符串就行。网上找到`a=s878926199a`。拿到flag.

31. 程序员本地网站

  一个考察X-Forwarded-For的，就不详细写了。

32. 各种绕过

  ![各种绕过](../imgs/Bugku/Web/gnome-shell-screenshot-CPNN4Z.png)

  考验sha1函数数组漏洞以及post\get方式提交，没什么难度。但是有个问题，我使用burpsuite怎么提交都出不了flag，后来写了Python小脚本才出现的。看来有必要下载一个hackbar之类的工具。

33. web8

  ![web8](../imgs/Bugku/Web/gnome-shell-screenshot-KIVR4Z.png)

  这个题目有两个解法。

  第一个解法：根据题目提示有txt文件，扫描目录知道可以访问flag.txt。内容是flags。既然有文件，那么直接构造`ac=flags`,然后利用file_get_contents函数构造`fn=flag.txt`就能拿到flag.

  ![web8](../imgs/Bugku/Web/gnome-shell-screenshot-FVCV4Z.png)

  第二个解法：利用伪协议。原理是`file_get_contents()`函数不仅仅可以读取文件内容，还可以读取url以及伪协议内容。
  那么直接利用php://input这个伪协议获取post中传递的内容的特性。具体构造见下图：

  ![web8](../imgs/Bugku/Web/gnome-shell-screenshot-M5J24Z.png)

  奇怪的是，这个方法我用hackbar根本不出结果，用burp就有结果，晕了。

34. 细心

  ![细心](../imgs/Bugku/Web/gnome-shell-screenshot-ONFN4Z.png)

  细心看了一下应该是一个假的报错页面。于是用dirb扫描一下后台，发现另外一个页面

  ![细心](../imgs/Bugku/Web/gnome-shell-screenshot-Q76Z4Z.png)

  看到下面有对x进行比较，几次尝试以后将`x=admin`，拿到flag(这个题真没什么意思)

  ![细心](../imgs/Bugku/Web/gnome-shell-screenshot-38LP4Z.png)

35. 求getshell

  题目如下:
  ![求getshell](../imgs/Bugku/Web/gnome-shell-screenshot-LSOM4Z.png)

  看出来是一个上传题。

  我们先尝试上传一个php一句话文件。

  ![求getshell](../imgs/Bugku/Web/gnome-shell-screenshot-QPPR4Z.png)

  显示无法通过。那么修改一下Content-type内容，将其修改为图片格式。

  ![求getshell](../imgs/Bugku/Web/gnome-shell-screenshot-SVG34Z.png)

  还是不行。最后看了其他人的WriteUp，是需要修改几个地方：

  1. 第一个Content-Type内容，需要经过大小写变换。(网上说是为了绕过waf，无法证实)
  2. 第二个是文件后缀名,发现php5可以通过。
  3. 第三个是上传文件后判断的Content-Type内容，这个内容会随着你上传文件的后缀判断。

  ![求getshell](../imgs/Bugku/Web/gnome-shell-screenshot-4DXS4Z.png)

  也不知道哪个神仙做出来的。然后我在网上收集了一些上传方面的资料：

  一般说来，上传验证有几种套路:

  - 客户端JavaScript验证:
    这种的没啥难度。绕过方式是直接抓包修改就行。
  - 文件后缀验证:
    就是上图第2个验证，绕过方式是抓包修改,可以使用其他名称如php2,php3,php4,PhP等绕过后缀名黑名单检验。
  - Content-type验证:
    就是上图第3个验证，绕过方式是抓包修改。
  - 文件头验证:
    比如服务器要求上传的是gif格式的文件，他会验证上传内容的开头符不符合gif格式文件的开头，绕过方式是在Burpsuite中抓包修改，加入GIF格式头，例如:`GIF89a<?php phpinfo(); ?>`
  - 文件内容检测验证：
    这个就有点骚包了。验证会对上传内容进行验证，过滤点一些敏感函数，例如assert、eval等。这时候可以用两步操作：**第一步**上传内容为一句话的txt文件。**第二步**`<?php include('xxx.txt');?>`

36. INSERT INTO注入

  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-B8C24Z.png)

  不论是题目的提示还是自己对代码的审计，应该看得出来以下几个规则:

  1. 第一行`error_reporting(0)`代表不会报错，就算错误也会正常执行。所以注入的话应该是盲注。
  2. explode函数对逗号进行了过滤
  3. 倒数第二行没有对X-Forwarded-For的内容进行过滤，可以从这个地方尝试注入
  4. SQL语句是INSERT语句，插入就是插入没有对错之分，因此在对待这个盲注时无法使用布尔型盲注。

  根据以上条件，判断出应该是要进行时间盲注。首要考虑sleep函数往哪放，因为这个语句是一个INSERT语句，所以我先尝试将insert语句闭合，插入`1'; select sleep(5);#`。这时候语句就变成了`insert into table_name(column_name) value('1');(select sleep(5));#')`。但可能是通过PHP执行的原因，无法执行两条连续的语句。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-6NEJ4Z.png)

  我在Mysql中可以执行两条。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-LL1Y4Z.png)  

  所以只能在INSERT语句下想办法，先构造`1' and sleep(5) and '1'='1`。执行后果然是延时了。

  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-IMWR4Z.png)  
  这时候后台语句就变成了`insert into table_name(column_name) value('1' and sleep(5) and '1'='1')`。在MySQL下看，这段内容是插入了0.因为`SELECT SLEEP(5)`的查询结果就是0

  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-6Q7W4Z.png)

  还要注意的是，第一个字不能是英文。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-XSIR4Z.png)  

  现在已经构造好大致的payload框架，然后是按照需要在刚才的sleep()函数附近修改内容。常规操作是使用if(条件判断,内容为真时延迟sleep(n),内容为假时)，但由于题目条件第二条对逗号进行了过滤不能直接使用if函数，那么换个姿势使用`CASE WHEN 条件判断 THEN 延迟SLEEP(5) ELSE 1 END`

  1. 第一步是试探数据库的字符数，构造语句`1' and (SELECT CASE WHEN  length(database())=1 THEN SLEEP(5) ELSE 1 END) AND '1'='1`在burp里面跑一下(burp的设置见https://www.jianshu.com/p/5d34b3722128)
  其中length(database())的数值设置为变量。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-PD9K4Z.png)

  然后把页面timeout时间设置低一点，一般要低于我们sleep的时间。这样设置的原因是当出现可能触发sleep的时候结果会提示timeout。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-6Q6W4Z.png)  

  走两步：
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-4GT34Z.png)

  2. 第二步是按照顺序一个个猜测字符内容。`1' and (SELECT CASE WHEN  substr(database() from 1 for 1)='a' THEN SLEEP(5) ELSE 1 END) AND '1'='1`

  需要注意的是由于逗号被过滤了，因此`limit 1,1`这种是无法完成的，但是可以通过`from 1 for 1`实现替代。此外这个payload里面有两个地方需要进行替换，第一个是`from 1 for 1`的第一个1，第一步我们得到database()名称一共有5个字符，所以第一个1，应该是1-5。第二个是`'a'`应该遍历所有字符来测试。
  让我们看看这样两个变量是怎么通过burp操作的。

  因为有两个变量，所以设置攻击方式为cluster bomb
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-KX4Q4Z.png)  

  设置好两个变量的内容，第一个变量是从1到5。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-UQOM4Z.png)

  第二个变量是一些字符串集合，实际上截图里面的还不够完整，应该加上大写字母以及符号。
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-TJHJ4Z.png)  

  设置好以后开始攻击，最后查看timeout的反馈页面拿到结果：web15

  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-O5R34Z.png)    

  虽然达成目的了，但是这种方法的效率是非常低的，因为如果在对第一个字符的第一次测试中就知道结果是a,那么实际上是不用继续测试'bcdefg...'的。但是机器不知道我们拿到需要的结果还会继续测试。因此需要找找其他方法来完成这个猜测的过程。

  3. 继续按照套路猜测表名称，我们先试试所有表名称共有多少个字符:
  `1' and (select case when ((select length(group_concat(table_name)) from information_schema.tables where table_schema=database())=§1§) then sleep(5) else 1 end) and '1'='1`
  话说没有一个检查SQL语句的编辑器的话，在burp里面写这些内容真容易弄错.

  因为我们在语句中用了`group_concat(table_name)`,所以在不知道有多少个表的情况下，所有表名称加上group_concat函数自带的逗号共有14个字符。

  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-UBPP4Z.png)

  4. 一个个猜测表名称的字符内容。

  `1' and (select case when (select ('§a§'=substr((select group_concat(table_name) from information_schema.tables where table_schema=database()) from §1§ for 1))) then sleep(5) else 1 end)  and '1'='1`

  继续通过cluster bomb(一串炸弹)控制两个变量来完成猜测.
  ![INSERT_INTO注入](../imgs/Bugku/Web/gnome-shell-screenshot-6W524Z.png)

  现在得到两个表，一个叫做client_ip一个叫做flag.

  5. 猜测flag表里面字段名称

      我们先猜字段数有多少个

      `1' and (select case when ((select count(column_name) from information_schema.columns where table_name='flag')=1) then sleep(5) else 1 end) and '1'='1`

      答案是1个(截图略过)

      然后猜字段名称是不是叫做flag

      `1' and (select case when ((select column_name from information_schema.columns where table_name='flag')='flag') then sleep(5) else 1 end) and '1'='1`

      答案是的。

  6. 猜测字段内容

      先猜测里面有多少条数据。

      `1' and (select case when ((select count(*) from flag)=1) then sleep(5) else 1 end) and '1'='1`

      答案是一个。

      然后猜测数据长度
      `1' and (select case when （select 3333 from ((select length(flag) from flag)=§1§)） then sleep(5) else 1 end) and '1'='1`(不知道为什么这个语句在我自己的MySQL中可以执行，但是在这个题目中死活执行不出来)

      最后一个个猜测字符，不赘述了。

37. 这是一个神奇的登陆框

    ![这是一个神奇的登陆框](../imgs/Bugku/Web/gnome-shell-screenshot-NAQM4Z.png)

    先admin/admin、123456等弱口令试试，提示try again。

    尝试用户名`1'%20or'1'='1'%23`，密码随意。结果还是提示try again，应该是属于正常范围。所以可能不是使用单引号闭合。

    ![这是一个神奇的登陆框](../imgs/Bugku/Web/gnome-shell-screenshot-U4RW4Z.png)

    单引号不行换成双引号试试

    ![这是一个神奇的登陆框](../imgs/Bugku/Web/gnome-shell-screenshot-934L4Z.png)    

    貌似有点反应了，要求我们使用AdMiNhEhEd账户登录

    我们按照常规的注入套路试试，payload：`1"%20order%20by%203%23`

    ![这是一个神奇的登陆框](../imgs/Bugku/Web/gnome-shell-screenshot-HOIQ4Z.png)

    然后剩下的就是常规注入，不赘述了。

    还有就是我拿到了AdMiNhEhEd账户的密码，登陆后出现一个这页面，也不知道干嘛的。感觉挺中二的，我也就没管它。

    ![这是一个神奇的登陆框](../imgs/Bugku/Web/gnome-shell-screenshot-4R1R4Z.png)

38. 多次

  这个题目的难度很大,但是弄懂了也能学到很多东西.

  打开页面看到地址后面有`id=1`,估计是SQL注入。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-VE454Z.png)

  用我准备得一个payload去扫一圈，感觉怪怪的没有发现页面的闭合规律。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-UT7C5Z.png)

  1. 首先判断是不是关键词被屏蔽了。正好学到很关键的一招**异或注入**，拿这个题目举例。`id=1`的时候页面显示正常，我们就可以在后面添加一个异或判断`'^(0)^'`(真是欢乐)。现在要进行观察，当异或判断里面为真`'^(1)^'`以及为假`'^(0)^'`的时候，页面是什么反应。如果是判断里面为真`'^(1)^'`，页面也显示正常，那么我们就可以把里面的内容更换成`'^('and')^'`，当页面显示正常，就说明`and`这个关键词没有被屏蔽。这个做法类似于报错注入。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-SYZL5Z.png)

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-MR494Z.png)


  首先进行测试，发现`select`、`and`这些关键词被屏蔽了，大小写也无法绕过，于是试一下double形。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-WSUB5Z.png)

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-OLEL5Z.png)  

  用其他关键词跑一下，找出其他被屏蔽的关键词`and`,`or`,`select`,`union`。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-DDRF5Z.png)

  2. 然后因为使用burp爆破的效率实在是太低了，我们使用sqlmap跑，因为有关键词过滤，所以必须选定tamper文件来绕过。查询资料发现一个叫做nonrecursivereplacement.py的tamper可以实现我们需要的double型的payload，但是这个文件不知道什么原因目前的新版本已经不提供了，我们在github上面找到以前的文件复制到`/usr/share/sqlmap/tamper/`文件夹下面，但是这个时候还不能直接使用，需要自行进行修改：

  ```
  keywords = ("UNION", "SELECT", "INSERT", "UPDATE", "AND")
  #按照我们发现的过滤的关键词进行增删，如果不删除不需要的关键词，就会出现问题。
  #譬如页面并没有过滤FROM,而我们把FROM添加进这个列表中后，
  #payload中的FROM就会变成FRFROMOM,语句就无法正常执行。
  retVal = payload

  warnMsg = "currently only couple of keywords are being processed %s. " % str(keywords)
  warnMsg += "You can set it manually according to your needs"
  singleTimeWarnMessage(warnMsg)

  if payload:
      for keyword in keywords:
          _ = random.randint(1, len(keyword) - 1)
          retVal = re.sub(r"(?i)\b%s\b" % keyword, "%s%s%s" % (keyword[:_], keyword, keyword[_:]), retVal)
      retVal = retVal.replace("OR","OORR")
      #这一句比较关键，如果OR添加上面keywords中，他只会实现全匹配，那么
      #information_schema中存在or会被过滤，这一句放在这就可以将其变成
      #infoorrmation_schema绕过。
  return retVal

  ```
  通过对tamper文件的修改，总算可以利用sqlmap工具了，之前没有修改tamper文件的时候，是通过-V 3来分析payload语句需要怎么修改。

  3. 使用sqlmap工具就不详细谈，通过数据库我们又看到了还有一个页面Once_More.php，继续注入。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-WVJF5Z.png)

  用自己准备得payload测试一波，发现关键点。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-58934Z.png)

  可以看到，第5和第6在同一个payload格式下，因为and后面的对错不同，页面返回的内容大小也不同。明显是一个报错注入点。sqlmap跑的时候，注意添加--user-agent=random就可以绕过所谓的waf。

  ![多次](../imgs/Bugku/Web/gnome-shell-screenshot-R1NA5Z.png)  

39. flag.php

  打开页面有个登陆框，
  ![flag.php](../imgs/Bugku/Web/gnome-shell-screenshot-YYCQ5Z.png)

  尝试了一下发现这个登陆框没什么作用，action后面没有跟页面地址。

  题目中有提示hint，修改URL为`/flagphp/?hint`,出现内容。
  ```
  <?php
  error_reporting(0);
  include_once("flag.php");
  $cookie = $_COOKIE['ISecer'];
  if(isset($_GET['hint'])){
    show_source(__FILE__);
  }
  elseif (unserialize($cookie) === "$KEY")
  {   
    echo "$flag";
  }
  else {
  ?>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Login</title>
    <link rel="stylesheet" href="admin.css" type="text/css">
    </head>
    <body>
    <br>
    <div class="container" align="center">
    <form method="POST" action="#">
      <p><input name="user" type="text" placeholder="Username"></p>
      <p><input name="password" type="password" placeholder="Password"></p>
      <p><input value="Login" type="button"/></p>
    </form>
    </div>
    </body>
    </html>
    <?php
  }
  $KEY='ISecer:www.isecer.com';
  ?>
  ```
  其中最关键的一步是`elseif (unserialize($cookie) === "$KEY")`。审计代码发现实际上`$KEY`是没有定义的，在PHP中，没有定义就使用的变量值是空字符串(世界上最伟大的PHP语言)。所以我们只需要将空字符串序列化后传递给Cookie，然后`unserialize($cookie)`就可以得到空字符串了。
  ```
  <?php
    $a="";
    print(serialize($a));
  ?>
  ```
  得到的值是`s:0:"";`,传入Cookie为:
  `Cookie: s:0:"";`(网上的writeup说需要将分号用url编码才行，实际上是不需要的。随便抓个包就知道实际上http头是允许出现分号的。)，最后拿到flag。

40. sql注入2

  打开页面是一个登陆框
  ![flag.php](../imgs/Bugku/Web/gnome-shell-screenshot-Y6JB6Z.png)

  随便弱口令输入则提示`password error`，尝试使用`admin' or '1'='1`这类的万能密码登录则页面返回了`illegal character`提示有非法字符。于是我用sqlmap软件data文件夹中的keywords.txt文件中的关键字进行了遍历，发现`and`、`or`、`like`之类的关键字都被过滤掉了，大小写、double型、`/**/`之类的抗过滤也没有成功。过滤的这些关键词里面最关键的还是`or`，因为`information_schema`这个表就无法查询了。

  题目中有提示`!,!=,=,+,-,^,%`这些符号这些没有被过滤。再尝试异或注入，首先构造`admin'^(0)^'`，页面返回`password error`，但是当我们构造`admin'^(1)^'`时，页面返回`username error`.ok，可以使用异或注入。

  首先可以判断出数据库名称长度`admin'^(length(database())=3)^'`,然后根据盲注的原理，要一个个判断出数据库字符,一般说来是使用`substr/mid/left`函数构造`substr(database(),1,1)='a'`，`mid(database(),1,1)='a'`，`left(database(),1)='a'`之类的语句来猜测内容。现在逗号`,`是被过滤的，尝试`substr(database() from 1 for 1)`,但是`for`也被过滤了。
