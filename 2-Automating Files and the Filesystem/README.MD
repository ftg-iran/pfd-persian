# فصل 2. خودکارسازی فایل ها و سیستم فایل #
یکی از قدرتمندترین ویژگی های پایتون توانایی آن در دستکاری متن و فایل ها است. در دنیای DevOps، شما به طور مداوم متن فایل‌ها را تجزیه، جستجو و تغییر می‌دهید، چه در حال جستجوی لاگ‌های برنامه یا انتشار فایل‌های پیکربندی باشید. فایل‌ها ابزاری برای نگهداری وضعیت داده ها، کد و پیکربندی شما هستند. آنها نحوه نگاه کردن به آنچه در گزارش‌ها رخ داده است و اینکه با پیکربندی چه اتفاقی بیافتد را کنترل می‌کنید. با پایتون می‌توانید فایل‌ها و متن‌هایی را که می‌توانید مکرراً استفاده کنید را ایجاد کنید، بخوانید و تغییر دهید. خودکارسازی این وظایف در واقع یکی از جنبه‌های DevOps مدرن است که آن را از مدیریت سیستم سنتی جدا می‌کند. به جای نگه داشتن مجموعه ای از دستور‌ها که باید به صورت دستی دنبال کنید، می‌توانید کد بنویسید. این شانس شما را برای از دست دادن مراحل (مراحل اجرای کامند مثلا، با ‌Bash) یا انجام نامرتب آنها کاهش می‌دهد. اگر مطمئن هستید که سیستم شما هر بار که آن را اجرا می‌کنید از همان مراحل استفاده می‌کند، می‌توانید درک و اطمینان بیشتری در این فرآیند داشته باشید.

## خواندن و نوشتن فایل ها ##

می‌توانید از تابع `open` برای ایجاد یک فایل آبجکت* استفاده کنید که بتواند فایل ها را بخواند و بنویسد. این به دو آرگومان نیاز دارد، مسیر فایل* و حالت* (mode به طور دلخواه دیفالتش* روی reading است). شما از mode برای نشان دادن اینکه آیا می‌خواهید یک فایل را بخوانید یا بنویسید و اینکه آیا ورودی متن است یا داده باینری‌ است، از mode استفاده می‌کنید. می‌توانید یک فایل متنی را با استفاده از  mode `r` برای خواندن محتویات آن باز کنید. فایل آبجکت دارای یک متد read است که محتویات فایل را به صورت رشته ای برمی‌گرداند:

```python
In [1]: file_path = 'bookofdreams.txt'
In [2]: open_file = open(file_path, 'r')
In [3]: text = open_file.read()
In [4]: len(text)
Out[4]: 476909

In [5]: text[56]
Out[5]: 's'

In [6]: open_file
Out[6]: <_io.TextIOWrapper name='bookofdreams.txt' mode='r' encoding='UTF-8'>

In [7]: open_file.close()
```
\
\
__یادداشــت__

بستن یک فایل پس از اتمام کار، عادت خوبی است. پایتون زمانی فایلی را می‌بندد که خارج از محدوده باشد، اما تا آن زمان فایل منابع را مصرف می‌کند و ممکن است از باز کردن آن توسط سایر فرآیندها جلوگیری کند.

همچنین می‌توانید با استفاده از متد `readlines` یک فایل را بخوانید. این روش فایل را می‌خواند و محتویات آن را بر روی کاراکترهای newline (`\n`) جدا می‌کند. این متد لیستی از رشته ها را برمی‌گرداند. هر رشته یک خط از متن اصلی است:

```python
In [8]: open_file = open(file_path, 'r')
In [9]: text = open_file.readlines()
In [10]: len(text)
Out[10]: 8796

In [11]: text[100]
Out[11]: 'science, when it admits the possibility of occasional hallucinations\n'

In [12]: open_file.close()
```
\
\
یک راه دم دستی و سریع برای باز کردن فایل‌ها استفاده عبارت `with` است. در این مورد نیازی به بستن یک فایل نیست. پایتون آن را می‌بندد و منبع فایل را در انتهای بلوک فرورفته منتشر می‌کند:

```python
In [13]: with open(file_path, 'r') as open_file:
    ...:     text = open_file.readlines()
    ...:

In [14]: text[101]
Out[14]: 'in the sane and healthy, also admits, of course, the existence of\n'

In [15]: open_file.closed
Out[15]: True
```

سیستم عامل های مختلف از کاراکترهای فرار (escaped characters) متفاوت برای نمایش انتهای خط استفاده می‌کنند. سیستم های یونیکس از `\n` و سیستم های ویندوز از `\r\n` استفاده می‌کنند. پایتون وقتی فایلی را به عنوان text باز می‌کنید، این موارد را به `\n` تبدیل می‌کند. اگر در حال باز کردن یک فایل باینری، مانند یک تصویر (jpeg.) هستید، اگر آن را به صورت متن باز کنید، احتمالاً داده ها را با این تبدیل خراب (corrupte) می‌کنید. با این حال، می‌توانید فایل‌های باینری را با افزودن `b` به mode بخوانید:

```python
In [15]: file_path = 'bookofdreamsghos00lang.pdf'
In [16]: with open(file_path, 'rb') as open_file:
    ...:     btext = open_file.read()
    ...:

In [17]: btext[0]
Out[17]: 37

In [18]: btext[:25]
Out[18]: b'%PDF-1.5\n%\xec\xf5\xf2\xe1\xe4\xef\xe3\xf5\xed\xe5\xee\xf4\n18'
```

با افزودن این، فایل بدون هیچ گونه تبدیل پایان خط (line-ending) باز می‌شود.
\
\
برای نوشتن در یک فایل، از حالت write استفاده کنید که به عنوان آرگومان `w` نمایش داده می‌شود. ابزار `direnv` برای راه اندازی و نصب خودکار برخی از محیط های توسعه استفاده می‌شود. می‌توانید متغیرهای محیطی و زمان اجرا برنامه را در فایلی با نام `.envrc` تعریف کنید`.direnv` از آن برای تنظیم این موارد استفاده می‌کند وقتی که وارد دایرکتوری با فایل می‌شوید. می‌توانید در چنین فایلی در پایتون، متغیر محیطی `STAGE` را روی `PROD` و `TABLE_ID` را روی `token-storage-1234` با استفاده از `open` با flag نوشتن (write) تنظیم کنید:

```python
In [19]: text = '''export STAGE=PROD
    ...: export TABLE_ID=token-storage-1234'''

In [20]: with open('.envrc', 'w') as opened_file:
    ...:     opened_file.write(text)
    ...:

In [21]: !cat .envrc
export STAGE=PROD
export TABLE_ID=token-storage-1234
```
\
\
__هشــــدار__

مراقب باشید که روش نوشتن `pathlib` در صورتی که فایلی از قبل وجود داشته باشد، آن را بازنویسی (overwrite نوشته قبلی حذف و جدید جایگزین می‌شود) می‌کند.
\
\
تابع `open` اگر فایلی از قبل وجود نداشته باشد ایجاد می‌کند و اگر وجود داشته باشد overwrite می‌کند. اگر می‌خواهید محتویات موجود را حفظ کنید و فقط فایل را اضافه کنید، از علامت ضمیمه `a` استفاده کنید. این flag متن جدیدی را به انتهای فایل اضافه می‌کند و در عین حال محتوای اصلی را حفظ می‌کند. اگر محتوای غیر متنی مانند محتوای یک فایل *jpeg.* می‌نویسید، اگر از `w` یا flag استفاده کنید احتمالاً آن را خراب می‌کنید. این خرابی احتمالاً به این دلیل است که پایتون هنگام نوشتن داده‌های متنی، انتهای خطوط را به پایانه‌های platform-specific (*(نرم افزار)ـی که به طور خاص برای یک پلت فرم نرم افزاری نوشته شده است که لایه زیر را مسدود می کند*) تبدیل می‌کند. برای نوشتن داده های باینری، می‌توانید با خیال راحت از `wb` یا `ab` استفاده کنید.
\
\
__فصل 3__ به طور عمیق ‍`pathlib` را پوشش می دهد. دو ویژگی مفیدِ توابع سهولت برای خواندن و نوشتن فایل ها هستند. `pathlib` فایل آبجکت را در پشت صحنه مدیریت می کند. موارد زیر به شما امکان می دهد متن را از یک فایل بخوانید:

```python
In [38]: path = pathlib.Path("/Users/kbehrman/sp.config")

In [39]: path.write_text("LOG:DEBUG")
Out[39]: 9

In [40]: path = pathlib.Path("/Users/kbehrman/sp")
Out[41]: 8
```

خواندن و نوشتن با استفاده از توابع `read` و `write` بر روی فایل‌ آبجکت معمولاً برای متن بدون ساختار کافی است، اما اگر با داده‌های پیچیده‌تری سر و کار دارید چه؟ فرمت __Javascript Object Notation (JSON)__ به طور گسترده ای برای ذخیره داده های ساختار یافته ساده در سرویس های وب مدرن استفاده می شود. از دو ساختمان داده استفاده می‌کند: یکی mapping از جفت‌های key-value مشابه `dict` پایتون و یک دیگر فهرستی از آیتم‌ها تا حدودی شبیه به `list` پایتون است. این نوع داده ها را برای اعداد، رشته ها، *boolians* (که مقادیر True/Flase دارند) و *nulls* (مقادیر خالی) را تعریف می کند. سرویس وب __AWS Identity and Access Management (IAM)__ به شما امکان می دهد دسترسی به منابع AWS را کنترل کنید. از فایل های JSON برای شرح خط مشی‌های دسترسی استفاده می کند، مانند این فایل نمونه:

```json
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "service-prefix:action-name",
        "Resource": "*",
        "Condition": {
            "DateGreaterThan": {"aws:CurrentTime": "2017-07-01T00:00:00Z"},
            "DateLessThan": {"aws:CurrentTime": "2017-12-31T23:59:59Z"}
        }
    }
}
```
\
\
شما می‌توانید از methodهای استاندارد `read` یا `readlines`  فایل آبجکت برای دریافت داده‌ها از چنین فایلی استفاده کنید:

```python
In [8]: with open('service-policy.json', 'r') as opened_file:
   ...:     policy = opened_file.readlines()
   ...:
```
\
\
نتیجه بلافاصله قابل استفاده نخواهد بود، زیرا بسته به تمایل روش خواندن انتخابی شما، یک رشته یا لیستی از رشته ها خواهد بود:

```python
In [9]: print(policy)
['{\n',
 '    "Version": "2012-10-17",
\n',
 '    "Statement": {\n',
 '        "Effect": "Allow",
\n',
 '        "Action": "service-prefix:action-name",
\n',
 '        "Resource": "*",
\n',
 '        "Condition": {\n',
 '            "DateGreaterThan": {"aws:CurrentTime": "2017-07-01T00:00:00Z"},
\n',
 '            "DateLessThan": {"aws:CurrentTime": "2017-12-31T23:59:59Z"}\n',
 '        }\n',
 '    }\n',
 '}\n']
```
\
\
سپس باید این رشته (یا رشته ها) را به ساختارهای داده و انواعی که با اصلی مطابقت دارند تجزیه کنید، که ممکن است کار بسیار زیادی باشد. یک راه به مراتب بهتر استفاده از ماژول `json` است:

```python
In [10]: import json

In [11]: with open('service-policy.json', 'r') as opened_file:
    ...:     policy = json.load(opened_file)
    ...:
```
\
\
این ماژول فرمت JSON را برای شما تجزیه می کند و داده ها را در ساختار داده های مناسب با پایتون  برمی گرداند:

```python
In [13]: from pprint import pprint

In [14]: pprint(policy)
{'Statement': {'Action': 'service-prefix:action-name',
               'Condition': {'DateGreaterThan':
                                  {'aws:CurrentTime': '2017-07-01T00:00:00Z'},
                             'DateLessThan':
                                  {'aws:CurrentTime': '2017-12-31T23:59:59Z'}},
               'Effect': 'Allow',
               'Resource': '*'},
 'Version': '2012-10-17'}
```
\
\
__هشــــدار__

ماژول `pprint` به طور خودکار اشیاء پایتون را برای چاپ فرمت می کند. خروجی آن اغلب راحت‌تر خوانده می‌شود و روشی مفید برای مشاهده ساختارهای داده __تودرتو__ است.
\
\
اکنون می توانید از داده ها با ساختار فایل اصلی استفاده کنید. به عنوان مثال، در اینجا نحوه تغییر منبعی که این policy دسترسی آن را کنترل می کند در `S3` آمده است:

```python
In [15]: policy['Statement']['Resource'] = 'S3'

In [16]: pprint(policy)
{'Statement': {'Action': 'service-prefix:action-name',
               'Condition': {'DateGreaterThan':
                                {'aws:CurrentTime': '2017-07-01T00:00:00Z'},
                             'DateLessThan':
                                {'aws:CurrentTime': '2017-12-31T23:59:59Z'}},
               'Effect': 'Allow',
               'Resource': 'S3'},
 'Version': '2012-10-17'}
```
\
\
با استفاده از متد `json.dump` می توانید یک دیکشنری پایتون را به صورت فایل JSON بنویسید. به این ترتیب فایل policy را که به تازگی اصلاح کرده اید به روز می کنید:

```python
In [17]: with open('service-policy.json', 'w') as opened_file:
    ...:     policy = json.dump(policy, opened_file)
    ...:
```
\
\
زبان دیگری که معمولا در فایل های پیکربندی استفاده می شود *YAML* ("YAML Ain't Markup Language") است. این یک سوپرمجموعه از JSON است، اما فرمت فشرده‌تری دارد و از فضای خالی (white space) مشابه نحوه استفاده پایتون از آن استفاده می‌کند.
\
\
__Ansible__ ابزاری است که برای خودکارسازی پیکربندی، مدیریت و استقرار (deploy) نرم افزار استفاده می شود. Ansible از فایل‌هایی استفاده می‌کند که به آنها *playbooks* گفته می‌شود تا اقداماتی را که می‌خواهید خودکار کنید، تعریف کند. این playbookها از فرمت YAML استفاده می‌کنند:

```yaml
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  ...
```

پرکاربردترین کتابخانه برای تجزیه فایل های YAML در پایتون PyYAML است. این در کتابخانه استاندارد پایتون نیست، اما می توانید آن را با استفاده از `pip` نصب کنید:

```bash
$ pip install PyYAML
```

پس از نصب، می‌توانید از PyYAML برای وارد کردن (import) و صادر کردن (export) داده‌های YAML مانند همان کاری که با JSON انجام دادید، استفاده کنید:

```python
In [18]: import yaml

In [19]: with open('verify-apache.yml', 'r') as opened_file:
    ...:     verify_apache = yaml.safe_load(opened_file)
    ...:
```

داده ها به عنوان ساختارهای داده آشنا پایتون بارگیری (load) می شوند (`list`ـی حاوی یک  `dict`):

```python
In [20]: pprint(verify_apache)
[{'handlers': [{'name': 'restart apache',
                'service': {'name': 'httpd', 'state': 'restarted'}}],
  'hosts': 'webservers',
  'remote_user': 'root',
  'tasks': [{'name': 'ensure apache is at the latest version',
             'yum': {'name': 'httpd', 'state': 'latest'}},
            {'name': 'write the apache config file',
             'notify': ['restart apache'],
             'template': {'dest': '/etc/httpd.conf', 'src': '/srv/httpd.j2'}},
            {'name': 'ensure apache is running',
             'service': {'name': 'httpd', 'state': 'started'}}],
  'vars': {'http_port': 80, 'max_clients': 200}}]
```

همچنین می توانید داده های پایتون را در یک فایل با فرمت YAML ذخیره کنید:

```python
In [22]: with open('verify-apache.yml', 'w') as opened_file:
    ...:     yaml.dump(verify_apache, opened_file)
    ...:
```

زبان دیگری که به طور گسترده برای نمایش داده های ساختار یافته استفاده می شود، زبان __Extensible Markup Language (XML)__ (ترجمه تحت‌الّفظی: *زبان نشانه گذاری توسعه پذیر*) است. این شامل اسناد سلسله مراتبی (hierarchical) از عناصر (elementها) تگ‌زده‌‌شده است. از لحاظ تاریخی، بسیاری از سیستم های وب از XML برای انتقال داده ها استفاده می کردند. یکی از این کاربردها برای فیدهای (feeds) __Real Simple Syndication (RSS)__ است. فیدهای RSS برای ردیابی و اطلاع کاربران از به روز رسانی وب سایت ها استفاده می شود و برای ردیابی انتشار مقالات از منابع مختلف استفاده می شود. فیدهای RSS از صفحات با فرمت XML استفاده می کنند. پایتون کتابخانه `xml` را برای کارکردن با اسناد XML ارائه می دهد. این ساختار سلسله مراتبی اسناد XML را به یک ساختار داده درخت مانند نگاشت (map) می کند. گره‌های (nodes) درخت elementها هستند، و از رابطه والد-فرزند برای مدل سازی سلسله مراتب استفاده می شود. node والد بالا به عنوان عنصر ریشه (root) نامیده می شود. برای تجزیه یک سند RSS XML و دریافت ریشه آن:

```python
In [1]: import xml.etree.ElementTree as ET
In [2]: tree = ET.parse('http_feeds.feedburner.com_oreilly_radar_atom.xml')

In [3]: root = tree.getroot()

In [4]: root
Out[4]: <Element '{http://www.w3.org/2005/Atom}feed' at 0x11292c958>
```

می‌توانید با چرخیدن (iterating) حول گره‌های فرزند (child nodes)  درخت در آن قدم بزنید:

```python
In [5]: for child in root:
   ...:     print(child.tag, child.attrib)
   ...:
{http://www.w3.org/2005/Atom}title {}
{http://www.w3.org/2005/Atom}id {}
{http://www.w3.org/2005/Atom}updated {}
{http://www.w3.org/2005/Atom}subtitle {}
{http://www.w3.org/2005/Atom}link {'href': 'https://www.oreilly.com'}
{http://www.w3.org/2005/Atom}link {'rel': 'hub',
                                   'href': 'http://pubsubhubbub.appspot.com/'}
{http://www.w3.org/2003/01/geo/wgs84_pos#}long {}
{http://rssnamespace.org/feedburner/ext/1.0}emailServiceId {}
...
```

به یاد داشته باشید که XML امکان namespacing  می‌دهد  (استفاده از تگ‌ها برای گروه بندی داده ها). XML تگ هایی را با namespaceهای محصور شده در براکت‌ها (`{}`) اضافه می کند. اگر ساختار سلسله مراتب را می دانید، می توانید عناصر را با استفاده از مسیرهایی که مطعلق به آن‌هاست را جستجو کنید. شما می توانید یک dictionary ارائه دهید که namespaceها را به سهولت تعریف می کند:

```python
In [108]: ns = {'default':'http://www.w3.org/2005/Atom'}
In [106]: authors = root.findall("default:entry/default:author/default:name", ns)

In [107]: for author in authors:
     ...:     print(author.text)
     ...:
Nat Torkington
VM Brasseur
Adam Jacob
Roger Magoulas
Pete Skomoroch
Adrian Cockcroft
Ben Lorica
Nat Torkington
Alison McCauley
Tiffani Bell
Arun Gupta
```

ممکن است متوجه شوید که با داده های ذخیره شده به عنوان مقادیر جدا شده با کاما (comma-separated values)  سروکار دارید که __CVS__ نامیده می‌شوند. این قالب برای داده های spreadsheet (یک سند الکترونیکی که در آن داده ها در ردیف ها و ستون های یک شبکه مرتب شده اند و می توان آنها را دستکاری کرد و در محاسبات استفاده کرد.) رایج است. می توانید از ماژول `csv` پایتون برای خواندن آسان این موارد استفاده کنید:

```python
In [16]: import csv
In [17]: file_path = '/Users/kbehrman/Downloads/registered_user_count_ytd.csv'

In [18]: with open(file_path, newline='') as csv_file:
    ...:     off_reader = csv.reader(csv_file, delimiter=',')
    ...:     for _ in range(5):
    ...:         print(next(off_reader))
    ...:
['Date', 'PreviousUserCount', 'UserCountTotal', 'UserCountDay']
['2014-01-02', '61', '5336', '5275']
['2014-01-03', '42', '5378', '5336']
['2014-01-04', '26', '5404', '5378']
['2014-01-05', '65', '5469', '5404']
```

آبجکت خواننده `csv` می‌چرخد حول *csv.* فایل هر خط در یک زمان و به شما امکان می دهد داده ها را یک ردیف و در یک زمان پردازش کنید. پردازش یک فایل به این روش به ویژه برای فایل‌های *csv.* بزرگی که نمی‌خواهید یک‌باره در حافظه بخوانید مفید است. البته، اگر نیاز به انجام محاسبات چند ردیف در ستون‌ها دارید و فایل بیش از حد بزرگ نیست، باید آن را به یکباره load کنید.
\
\
بسته Pandas یک پایه اصلی در دنیای علم داده است. این شامل یک ساختار داده، `pandas.DataFrame` است که مانند یک جدول داده، شبیه به یک spreadsheet بسیار قدرتمند عمل می کند. اگر داده‌های جدول مانندی دارید که می‌خواهید تجزیه و تحلیل آماری روی آنها انجام دهید یا می‌خواهید آن‌ها را با ردیف‌ها و ستون‌ها دستکاری کنید، DataFrames ابزاریست برای شما. این یک کتابخانه third-party (شخص ثالث) است، بنابراین باید آن را با `pip` نصب کنید. شما می توانید از روش های مختلفی برای load داده ها در DataFrames استفاده کنید. یکی از رایج ترین آنها از یک فایل *csv.* است:

```python
In [54]: import pandas as pd

In [55]: df = pd.read_csv('sample-data.csv')

In [56]: type(df)
Out[56]: pandas.core.frame.DataFrame
```

می توانید با استفاده از متد `head` به ردیف های بالای DataFrame خود نگاهی بیندازید:

```python
In [57]: df.head(3)
Out[57]:
   Attributes     open       high        low      close     volume
0     Symbols        F          F          F          F          F
1        date      NaN        NaN        NaN        NaN        NaN
2  2018-01-02  11.3007    11.4271    11.2827    11.4271   20773320
```

شما می توانید با استفاده از متد `describe` بینش آماری بدست آورید:

```python
In [58]: df.describe()
Out[58]:
        Attributes    open      high    low     close     volume
count          357     356       356    356       356        356
unique         357     290       288    297       288        356
top     2018-10-18  10.402    8.3363   10.2    9.8111   36298597
freq             1       5         4      3         4          1
```

همچنین، می‌توانید یک ستون از داده‌ها را با استفاده از نام آن در square brackets (براکت مربعی شکل `{}`) مشاهده کنید:

```python
In [59]: df['close']
Out[59]:
0            F
1          NaN
2      11.4271
3      11.5174
4      11.7159
        ...
352       9.83
353       9.78
354       9.71
355       9.74
356       9.52
Name: close, Length: 357, dtype: object
```

در Pandas متدهای بسیار بیشتری برای تجزیه و تحلیل و دستکاری داده های جدول مانند دارند و کتاب های زیادی در مورد استفاده از آن وجود دارد. این ابزاری است که در صورت نیاز به تجزیه و تحلیل داده ها باید از آن آگاه باشید.



## استفاده از Regular Expressionها برای سرچ کردن در Text ##

سرور HTTP آپاچی یک وب سرور منبع باز است که به طور گسترده برای ارائه محتوای وب استفاده می شود. وب سرور را می توان برای ذخیره فایل‌های log در فرمت های مختلف پیکربندی کرد. یکی از فرمت‌های پرکاربرد، __Common Log Format (CLF)__ است. انواع ابزارهای تحلیل لاگ می توانند این قالب را درک کنند. در زیر چیدمان (layout) این قالب را مشاهده می کنید:

```
<IP Address> <Client Id> <User Id> <Time> <Request> <Status> <Size>
```

آنچه در زیر می آید نمونه ای از یک لاگ در این قالب است:

```
127.0.0.1 - swills [13/Nov/2019:14:43:30 -0800] "GET /assets/234 HTTP/1.0" 200 2326
```

__فصل 1__ شما را با regular expressions و ماژول Python `re` آشنا کرد، بنابراین بیایید از آن برای استخراج (pull) اطلاعات از یک log در common log format استفاده کنیم. یکی از ترفندهای ساخت regular expressions این است که آن را در sectionها (بخش‌ها) انجام دهید. انجام این کار شما را قادر می سازد تا هر subexpression (زیر عبارت) را بدون پیچیدگی‌های debugging در کل عبارت، انجام دهید. می‌توانید با استفاده از گروه‌های نام‌گذاری شده یک regular expression ایجاد کنید تا آدرس IP را از یک خط بیرون بکشید:

```python
In[1]: line = '127.0.0.1 - rj [13/Nov/2019:14:34:30 -0000] "GET HTTP/1.0" 200'

In [2]: re.search(r'(?P<IP>\d+\.\d+\.\d+\.\d+)', line)
Out[2]: <re.Match object; span=(0, 9), match='127.0.0.1'>

In [3]: m = re.search(r'(?P<IP>\d+\.\d+\.\d+\.\d+)', line)

In [4]: m.group('IP')
Out[4]: '127.0.0.1'
```

همچنین می توانید یک regular expression برای دریافت زمان ایجاد کنید:

```python
In [5]: r = r'\[(?P<Time>\d\d/\w{3}/\d{4}:\d{2}:\d{2}:\d{2})\]'

In [6]: m = re.search(r, line)

In [7]: m.group('Time')
Out[7]: '13/Nov/2019:14:43:30'
```

می توانید چندین element را بگیرید، همانطور که در اینجا انجام شده است:  IP، user، time و request:

```python
In [8]:  r = r'(?P<IP>\d+\.\d+\.\d+\.\d+)'

In [9]: r += r' - (?P<User>\w+) '

In [10]: r += r'\[(?P<Time>\d\d/\w{3}/\d{4}:\d{2}:\d{2}:\d{2})\]'

In [11]: r += r' (?P<Request>".+")'

In [12]:  m = re.search(r, line)

In [13]: m.group('IP')
Out[13]: '127.0.0.1'

In [14]: m.group('User')
Out[14]: 'rj'

In [15]: m.group('Time')
Out[15]: '13/Nov/2019:14:43:30'

In [16]: m.group('Request')
Out[16]: '"GET HTTP/1.0"'
```

تجزیه یک تک خط از فایل لاگ جالب است اما چندان مفید نیست. گرچه، می توانید از این regular expression به عنوان مبنایی برای طراحی یکی از آن‌ها برای استخراج اطلاعات از کل لاگ استفاده کنید. فرض کنید می‌خواهید تمام آدرس‌های IP را برای درخواست‌های `GET` که در 8 نوامبر 2019 اتفاق افتاد، pull کنید (استخراج کنید). با استفاده از عبارت قبلی، تغییراتی را بر اساس مشخصات درخواست خود انجام می‌دهید:

```python
In [62]: r = r'(?P<IP>\d+\.\d+\.\d+\.\d+)'
In [63]: r += r'- (?P<User>\w+)'
In [64]: r += r'\[(?P<Time>08/Nov/\d{4}:\d{2}:\d{2}:\d{2} [-+]\d{4})\]'
In [65]: r += r' (?P<Request>"GET .+")'
```

از متد `finditer` برای پردازش گزارش استفاده کنید و IP آدرس‌های منطبق با خطوط را چاپ کنید:

```python
In [66]: matched = re.finditer(r, access_log)

In [67]: for m in matched:
    ...:     print(m.group('IP'))
    ...:
127.0.0.1
342.3.2.33
```

کارهای زیادی می توانید با regular expressionها و انواع متون انجام دهید. اگر آنها شما را نترسانند، شما آنها را یکی از قدرتمندترین ابزارها در برخورد با متن خواهید یافت.

## سر و کله زدن با فایل های بزرگ ##

مواقعی وجود دارد که باید فایل های بسیار بزرگ را پردازش کنید. اگر فایل ها حاوی داده هایی هستند که می توان آنها را در یک زمان در هر خط پردازش کرد، کار با پایتون آسان است. به جای بارگیری کل فایل در حافظه همانطور که تاکنون انجام داده اید، می توانید یک خط را در یک زمان بخوانید، خط را پردازش کنید و سپس به خط بعدی بروید. خطوط به طور خودکار توسط آشغال جمع‌کن (Garbage Collection) پایتون از حافظه حذف می شوند و حافظه را آزاد می کنند.
\
\
__یادداشــت__

پایتون به طور خودکار حافظه مورد نظر را اختصاص و آزاد می کند. Garbage collection یکی از ابزارهای انجام این کار است. جمع آوری زباله پایتون را می توان با استفاده از بسته `gc` کنترل کرد، اگرچه به ندرت به این مورد نیاز است.
\
\
این واقعیت که سیستم‌عامل‌ها از انتهای خط متناوب (alternate line endings) استفاده می‌کنند، می‌تواند هنگام خواندن یک فایل ایجاد شده در سیستم‌عامل دیگری دردسرساز باشد. فایل های ایجاد شده توسط ویندوز علاوه بر `\n` دارای کاراکترهای `\r` نیز هستند. اینها به عنوان بخشی از متن در یک سیستم مبتنی بر لینوکس نشان داده می شوند. اگر فایل بزرگی دارید و می‌خواهید انتهای خطوط را متناسب با سیستم‌عامل فعلی خود اصلاح کنید، می‌توانید فایل را باز کنید، هر بار یک خط را بخوانید و آن را در یک فایل جدید ذخیره کنید. __پایتون ترجمه پایان خط را برای شما انجام می دهد__:

```python
In [23]: with open('big-data.txt', 'r') as source_file:
    ...:     with open('big-data-corrected.txt', 'w') as target_file:
    ...:         for line in source_file:
    ...:             target_file.write(line)
    ...:
```

توجه داشته باشید که می توانید دستورات `with` را تو در تو کنید (nest)  تا دو فایل را همزمان باز کنید و در یک خط از فایل آبجکت منبع حلقه بزنید. شما می توانید یک تابع مولد (generator function) برای مدیریت این کار تعریف کنید، به خصوص اگر نیاز دارید چندین فایل را در یک خط در یک زمان تجزیه کنید:

```python
In [46]: def line_reader(file_path):
    ...:     with open(file_path, 'r') as source_file:
    ...:         for line in source_file:
    ...:             yield line
    ...:

In [47]: reader = line_reader('big-data.txt')

In [48]: with open('big-data-corrected.txt', 'w') as target_file:
    ...:     for line in reader:
    ...:         target_file.write(line)
    ...:
```

اگر مانند یک فایل باینری بزرگ، از انتهای خطوط به عنوان وسیله ای برای تجزیه داده های خود استفاده نکنید، می توانید داده های خود را به صورت تکه ای بخوانید. شما تعداد بایت های خوانده شده در هر تکه را به متد `read` فایل آبجکت ارسال می کنید. وقتی چیزی برای خواندن باقی نماند، عبارت (expression) یک رشته خالی برمی گرداند:

```python
In [27]: with open('bb141548a754113e.jpg', 'rb') as source_file:
    ...:     while True:
    ...:         chunk = source_file.read(1024)
    ...:         if chunk:
    ...:             process_data(chunk)
    ...:         else:
    ...:             break
    ...:
```

## رمزگذاری متن ##

برای اطمینان از امنیت، به مراتب نیاز به رمزگذاری متن دارید. علاوه بر `hashlib` که پکیج built-in پایتون است، یک پکیج third-party پرکاربرد به نام `cryptography` وجود دارد. بیایید نگاهی به هر دو بیاندازیم.

### هشینگ با Hashlib ###

برای ایمن بودن، رمزهای عبور کاربر باید به صورت رمزگذاری شده ذخیره شوند. یک روش معمول برای رسیدگی به این موضوع استفاده از یک تابع یک طرفه برای رمزگذاری پسوورد و تبدیل آن به یک رشته بیت است که مهندسی معکوس آن بسیار سخت است. توابعی که این کار را انجام می دهند _hash functions_ نامیده می شوند. علاوه بر پنهان کردن رمزهای عبور، توابع هش تضمین می کند که اسناد ارسال شده از طریق وب در مدت زمان انتقال بدون تغییر باقی می مانند. تابع هش را روی سند اجرا می کنید و نتیجه را به همراه سند ارسال می کنید. سپس گیرنده می‌تواند تأیید کند که وقتی سند را هش می‌کند، مقدار یکسان است. `hashlib` شامل الگوریتم های ایمن برای انجام این کار است، از جمله _SHA1_، _SHA224_، _SHA384_، _SHA512_، و _RSA’s MD5_. به این صورت است که با استفاده از الگوریتم MD5 رمز عبور را هش می کنید:

```python
In [62]: import hashlib

In [63]: secret = "This is the password or document text"

In [64]: bsecret = secret.encode()

In [65]: m = hashlib.md5()

In [66]: m.update(bsecret)

In [67]: m.digest()
Out[67]: b' \xf5\x06\xe6\xfc\x1c\xbe\x86\xddj\x96C\x10\x0f5E'
```

توجه داشته باشید که اگر رمز عبور یا سند شما یک رشته است، باید با استفاده از متد `encode` آن را به یک رشته باینری تبدیل کنید.

### رمزگزاری با cryptography ###

کتابخانه `cryptography` یک انتخاب محبوب برای رسیدگی به مشکلات رمزگذاری در پایتون است. این یک بسته third-party است، بنابراین باید آن را با `pip` نصب کنید. _رمزگذاری کلید متقارن یا Symmetric key encryption_ گروهی از الگوریتم های رمزگذاری مبتنی بر کلیدهای مشترک (shared keys) است. این الگوریتم ها شامل الگوریتم رمزگذاری پیشرفته (AES: Advanced Encryption Algorithm)، Blowfish، Data Encryption Standard (DES)، Serpent و Twofish هستند. یک shared key مشابه رمز عبوری است که برای رمزگذاری و رمزگشایی متن استفاده می شود. این واقعیت که هم سازنده و هم خواننده یک فایل رمزگذاری شده نیاز به اشتراک گذاری کلید دارند، در مقایسه با رمزگذاری کلید _نامتقارن_ (asymmetric)، که بعداً به آن خواهیم پرداخت، یک اشکال است. با این حال، رمزگذاری کلید متقارن سریعتر و ساده تر است و بنابراین برای رمزگذاری فایل های بزرگ مناسب است. Fernet پیاده سازی الگوریتم محبوب AES است. ابتدا باید یک کلید ایجاد کنید:

```python
In [1]: from cryptography.fernet import Fernet

In [2]: key = Fernet.generate_key()

In [3]: key
Out[3]: b'q-fEOs2JIRINDR8toMG7zhQvVhvf5BRPx3mj5Atk5B8='
```

شما باید این کلید را ایمن ذخیره کنید، زیرا برای رمزگشایی به آن نیاز دارید. به خاطر داشته باشید که هر کسی که به آن دسترسی دارد می تواند فایل های شما را نیز رمزگشایی کند. اگر می خواهید کلید را در یک فایل ذخیره کنید، از نوع داده باینری استفاده کنید. مرحله بعدی رمزگذاری داده ها با استفاده از شی `Fernet` است:

```python
In [4]: f = Fernet(key)

In [5]: message = b"Secrets go here"


In [6]: encrypted = f.encrypt(message)

In [7]: encrypted
Out[7]: b'gAAAAABdPyg4 ... plhkpVkC8ezOHaOLIA=='
```

می توانید داده ها را با استفاده از یک شی `Fernet` که با همان کلید ایجاد شده است رمزگشایی کنید:

```python
In [1]: f = Fernet(key)

In [2]: f.decrypt(encrypted)
Out[2]: b'Secrets go here'
```

رمزگذاری کلید نامتقارن از یک جفت کلید، یکی عمومی و دیگری خصوصی استفاده می کند. کلید عمومی به گونه ای طراحی شده است که به طور گسترده به اشتراک گذاشته شود، در حالی که یک تک کاربر، کلید خصوصی را نگه می دارد. تنها راهی که می توانید پیام هایی را که با استفاده از کلید عمومی رمزگذاری شده اند رمزگشایی کنید، استفاده از کلید خصوصی است. این سبک از رمزگذاری به طور گسترده برای انتقال اطلاعات محرمانه هم در شبکه های محلی و هم در سراسر اینترنت استفاده می شود. یکی از الگوریتم‌های کلید نامتقارن بسیار محبوب، Rivest-Shamir-Adleman (RSA) است که به طور گسترده برای ارتباط در سراسر شبکه‌ها استفاده می‌شود. کتابخانه cryptography توانایی ایجاد جفت کلید عمومی/خصوصی را ارائه می دهد:

```python
In [1]: from cryptography.hazmat.backends import default_backend

In [2]: from cryptography.hazmat.primitives.asymmetric import rsa

In [3]: private_key = rsa.generate_private_key(public_exponent=65537,
                                               key_size=4096,
                                               backend=default_backend())

In [4]: private_key
Out[4]: <cryptography.hazmat.backends.openssl.rsa._RSAPrivateKey at 0x10d377c18>

In [5]: public_key = private_key.public_key

In [6]: public_key = private_key.public_key()

In [7]: public_key
Out[7]: <cryptography.hazmat.backends.openssl.rsa._RSAPublicKey at 0x10da642b0>
```

سپس می توانید از کلید عمومی برای رمزگذاری استفاده کنید:

```python
In [13]: decrypted = private_key.decrypt(encrypted,
    ...:    padding.OAEP(mgf=padding.MGF1(algorithm=hashes.SHA256()),
    ...:    algorithm=hashes.SHA256(),
    ...:    label=None))

In [14]: decrypted
Out[14]: b'More secrets go here'
```

## ماژول os ##

ماژول `os` یکی از پرکاربردترین ماژول های پایتون است. این ماژول بسیاری از system call های سطح پایین سیستم‌عامل را مدیریت می‌کند و تلاش می‌کند تا یک رابط ثابت در بین چندین سیستم عامل ارائه دهد، که اگر فکر می‌کنید برنامه شما ممکن است بر روی سیستم‌های مبتنی بر ویندوز و یونیکس اجرا شود، مهم است. برخی از ویژگی‌های خاص عملیاتی (`os.O_TEXT` برای ویندوز و `os.O_CLOEXEC` در لینوکس) را ارائه می‌کند که در همه پلتفرم‌ها در دسترس نیستند. فقط در صورتی از این موارد استفاده کنید که مطمئن هستید برنامه شما نیازی به قابل حمل(portable) بودن در سراسر سیستم عامل ندارد. مثال 2-1 برخی از مفیدترین روش های اضافی ماژول `os` را نشان می دهد.
\
\
__مثال ۱.۲. ماژول‌های بیشتر از os__

```python
In [1]: os.listdir('.') 1
Out[1]: ['__init__.py', 'os_path_example.py']

In [2]: os.rename('_crud_handler', 'crud_handler') 2

In [3]: os.chmod('my_script.py', 0o777) 3

In [4]: os.mkdir('/tmp/holding') 4

In [5]: os.makedirs('/Users/kbehrman/tmp/scripts/devops') 5

In [6]: os.remove('my_script.py') 6

In [7]: os.rmdir('/tmp/holding') 7

In [8]: os.removedirs('/Users/kbehrman/tmp/scripts/devops') 8

In [9]: os.stat('crud_handler') 9
Out[9]: os.stat_result(st_mode=16877,
                       st_ino=4359290300,
                       st_dev=16777220,
                       st_nlink=18,
                       st_uid=501,
                       st_gid=20,
                       st_size=576,
                       st_atime=1544115987,
                       st_mtime=1541955837,
                       st_ctime=1567266289)
```

۱. فهرست محتویات یک دایرکتوری.\
۲. تغییر نام فایل یا دایرکتوری.\
۳. تنظیمات مجوز (permission) یک فایل یا فهرست را تغییر دهید.\
۴. یک دایرکتوری ایجاد کنید.\
۵. به صورت بازگشتی (recursively) یک مسیر دایرکتوری ایجاد کنید.\
۶. یک فایل را حذف کنید.\
۷. حذف یک دایرکتوری.\
۸. درختی از دایرکتوری ها را حذف کنید، از فهرست برگ شروع کنید و تا درخت کار کنید. عملیات با اولین دایرکتوری غیر خالی متوقف می شود.\
۹. آمار مربوط به فایل یا فهرست را دریافت کنید. این آمار شامل `st_mode`، نوع فایل و مجوزها، و `st_atime`، زمان آخرین دسترسی به آیتم است.

## مدیریت فایل ها و دایرکتوری ها با استفاده از os.path ##

در پایتون، می توانید از رشته ها (باینری یا غیره) برای نمایش مسیرها استفاده کنید. ماژول `os.path` مجموعه ای از متدهای مرتبط با مسیر را برای ایجاد و دستکاری مسیرها به عنوان رشته ارائه می دهد. همانطور که قبلا ذکر شد، ماژول `os` سعی می کند رفتارهای Cross-platform (قابل استفاده بر روی انواع کامپیوتر‌ها) را ارائه دهد، و زیرماژول `os.path` نیز از این قاعده مستثنی نیست. این ماژول مسیرها را بر اساس سیستم عامل فعلی تفسیر می کند و از اسلش های رو به جلو برای جدا کردن دایرکتوری ها در سیستم های یونیکس مانند و اسلش های عقب در ویندوز استفاده می کند. برنامه شما می تواند مسیرهایی را بسازد که روی سیستم فعلی کار می کنند، هر کدام که باشد. توانایی تقسیم و پیوستن آسان مسیرها احتمالاً پرکاربردترین قابلیت `os.path` است. سه روش مورد استفاده برای تقسیم مسیرها عبارتند از `split`، `basename` و `dirname`:

```python
In [1]: import os

In [2]: cur_dir = os.getcwd() 1

In [3]: cur_dir
Out[3]: '/Users/kbehrman/Google-Drive/projects/python-devops/samples/chapter4'

In [4]: os.path.split(cur_dir) 2
Out[4]: ('/Users/kbehrman/Google-Drive/projects/python-devops/samples',
         'chapter4')

In [5]: os.path.dirname(cur_dir) 3
Out[5]: '/Users/kbehrman/Google-Drive/projects/python-devops/samples'

In [6]: os.path.basename(cur_dir) 4
Out[6]: 'chapter4'
```

۱. دایرکتوری فعلی را دریافت کنید.\
۲. `os.path.split` مسیر در سطح برگ را از مسیر والد جدا می کند.\
۳. `os.path.dirname` مسیر والد را برمی گرداند.\
۴. `os.path.basename` نام برگ را برمی گرداند.

شما به راحتی می توانید از `os.path.dirname` برای بالا رفتن از درخت دایرکتوری استفاده کنید:

```python
In [7]: while os.path.basename(cur_dir):
   ...:     cur_dir = os.path.dirname(cur_dir)
   ...:     print(cur_dir)
   ...:
/Users/kbehrman/projects/python-devops/samples
/Users/kbehrman/projects/python-devops
/Users/kbehrman/projects
/Users/kbehrman
/Users
/
```

استفاده از فایل‌ها برای پیکربندی یک برنامه در زمان اجرا یک روش رایج است. فایل‌ها در سیستم‌های شبه یونیکس بر اساس قرارداد به عنوان فایل‌های dotfiles که به _rc_ ختم می‌شوند نام‌گذاری می‌شوند. فایل _vimrc._ که در VIM و _bashrc._ در Bash دو نمونه رایج هستند. شما می توانید این فایل ها را در مکان های مختلف ذخیره کنید. اغلب برنامه ها سلسله مراتبی از مکان ها را برای بررسی تعریف می کنند. به عنوان مثال، ابزار شما ممکن است ابتدا به دنبال یک متغیر محیطی (environment variable) باشد که تعیین می کند از کدام فایل _rc_ استفاده شود، و در غیاب آن، دایرکتوری کاری و سپس دایرکتوری home کاربر را بررسی کنید. در مثال ۲.۲ ما سعی می کنیم یک فایل _rc_ را در این مکان ها قرار دهیم. ما از متغیر `file` استفاده می کنیم که پایتون به طور خودکار هنگام اجرای کد پایتون از یک فایل تنظیم می کند. این متغیر با یک مسیر نسبت به دایرکتوری کاری فعلی پر شده است، نه یک مسیر مطلق یا کامل. پایتون به طور خودکار مسیرها را گسترش نمی دهد، همانطور که در سیستم های مشابه یونیکس رایج است، بنابراین قبل از اینکه از آن برای ساخت مسیر برای بررسی فایل _rc_ خود استفاده کنیم، باید این مسیر را گسترش دهیم. به طور مشابه، پایتون به طور خودکار environment variables را در مسیرها گسترش نمی دهد، بنابراین ما باید به طور صریح این متغیرها را گسترش دهیم.
\
\
__مثال ۲.۲،‌ متد find_rc__

```python
def find_rc(rc_name=".examplerc"):

    # Check for Env variable
    var_name = "EXAMPLERC_DIR"
    if var_name in os.environ: 1
        var_path = os.path.join(f"${var_name}", rc_name) 2
        config_path = os.path.expandvars(var_path) 3
        print(f"Checking {config_path}")
        if os.path.exists(config_path): 4
            return config_path

    # Check the current working directory
    config_path = os.path.join(os.getcwd(), rc_name)  5
    print(f"Checking {config_path}")
    if os.path.exists(config_path):
        return config_path

    # Check user home directory
    home_dir = os.path.expanduser("~/")  6
    config_path = os.path.join(home_dir, rc_name)
    print(f"Checking {config_path}")
    if os.path.exists(config_path):
        return config_path

    # Check Directory of This File
    file_path = os.path.abspath(__file__) 7
    parent_path = os.path.dirname(file_path) 8
    config_path = os.path.join(parent_path, rc_name)
    print(f"Checking {config_path}")
    if os.path.exists(config_path):
        return config_path

    print(f"File {rc_name} has not been found")
```

۱. بررسی کنید که آیا متغیر محیط در محیط فعلی وجود دارد یا خیر.\
۲. از `join` برای ساخت مسیری با نام متغیر محیطی استفاده کنید. این چیزی شبیه `$EXAMPLERC_DIR/.examplerc` خواهد بود.\
۳. متغیر محیطی را بسط دهید تا مقدار آن را در مسیر وارد کنید.\
۴. بررسی کنید که آیا فایل وجود دارد یا خیر.\
۵. یک مسیر با استفاده از دایرکتوری کاری فعلی بسازید.\
۶. از تابع `expanduser` برای گرفتن مسیر دایرکتوری home مطعلق به  user استفاده کنید.\
۷. مسیر نسبی (relative) ذخیره شده در `file` را به یک مسیر مطلق گسترش دهید.\
۸. از `dirname` برای دریافت مسیر دایرکتوری حاوی فایل فعلی استفاده کنید.\
__مثال ۲.۳. os_path_walk.py__

```python
#!/usr/bin/env python

import fire
import os

def walk_path(parent_path):
    print(f"Checking: {parent_path}")
    childs = os.listdir(parent_path) 1

    for child in childs:
        child_path = os.path.join(parent_path, child) 2
        if os.path.isfile(child_path): 3
            last_access = os.path.getatime(child_path) 4
            size = os.path.getsize(child_path) 5
            print(f"File: {child_path}")
            print(f"\tlast accessed: {last_access}")
            print(f"\tsize: {size}")
        elif os.path.isdir(child_path): 6
            walk_path(child_path) 7

if __name__ == '__main__':
    fire.Fire()
```

۱. os.listdir محتویات یک دایرکتوری را برمی گرداند.\
۲. این یک شی `pathlib.Path` مناسب برای سیستم عامل فعلی ایجاد می کند.\
۳. بررسی کنید که آیا مسیر یک فایل را نشان می دهد یا خیر.\
۴. آخرین باری که به فایل دسترسی داشتید را دریافت کنید.\
۵. اندازه فایل را دریافت کنید.\
۶. بررسی کنید که آیا مسیر نشان دهنده یک دایرکتوری است.\
۷. درخت را از این فهرست به پایین بررسی کنید.\
\
می‌توانید از اسکریپتی مانند این برای شناسایی فایل‌ها یا فایل‌های بزرگی که به آن‌ها دسترسی نداشته‌اند، استفاده کنید و سپس آن‌ها را گزارش دهید، انتقال دهید یا حذف کنید.

## قدم زدن روی Directory Trees به وسیله os.walk ##

ماژول `os` یک تابع راحت برای راه قدم زدن روی شاخه‌های دایرکتوری به نام `os.walk` ارائه می دهد. این تابع یک generator را برمی گرداند که به نوبه خود یک تاپل برای هر تکرار برمی گرداند. تاپل از مسیر فعلی، فهرستی از دایرکتوری ها و فهرستی از فایل ها تشکیل شده است. در مثال ۴.۲ ما تابع `walk_path` خود را از مثال ۳.۲ برای استفاده از `os.walk` بازنویسی می کنیم. همانطور که در این مثال می بینید، با `os.walk` نیازی نیست که مسیرهای فایل را آزمایش کنید یا تابع را با هر زیرشاخه فراخوانی کنید.
\
\
__مثال ۲.۴. بازنویسی walk_path__

```python
def walk_path(parent_path):
    for parent_path, directories, files in os.walk(parent_path):
        print(f"Checking: {parent_path}")
        for file_name in files:
            file_path = os.path.join(parent_path, file_name)
            last_access = os.path.getatime(file_path)
            size = os.path.getsize(file_path)
            print(f"File: {file_path}")
            print(f"\tlast accessed: {last_access}")
            print(f"\tsize: {size}")
```

## مسیر‌ها به عنوان شئ با Pathlib ##

کتابخانه `pathlib` مسیرها را به عنوان اشیا به جای رشته نشان می دهد. در مثال ۲.۵، مثال ۲.۲ را با استفاده از `pathlib` به جای `os.path` بازنویسی می کنیم.
\
\
__مثال ۲.۵. بازنویسی find_rc__

```python
def find_rc(rc_name=".examplerc"):

    # Check for Env variable
    var_name = "EXAMPLERC_DIR"
    example_dir = os.environ.get(var_name) 1
    if example_dir:
        dir_path = pathlib.Path(example_dir) 2
        config_path = dir_path / rc_name 3
        print(f"Checking {config_path}")
        if config_path.exists(): 4
            return config_path.as_postix() 5

    # Check the current working directory
    config_path = pathlib.Path.cwd() / rc_name 6
    print(f"Checking {config_path}")
    if config_path.exists():
        return config_path.as_postix()

    # Check user home directory
    config_path = pathlib.Path.home() / rc_name 7
    print(f"Checking {config_path}")
    if config_path.exists():
        return config_path.as_postix()

    # Check Directory of This File
    file_path = pathlib.Path(__file__).resolve() 8
    parent_path = file_path.parent 9
    config_path = parent_path / rc_name
    print(f"Checking {config_path}")
    if config_path.exists():
        return config_path.as_postix()

    print(f"File {rc_name} has not been found")
```

۱. از زمان نوشتن، `pathlib` متغیرهای محیطی را گسترش نمی‌دهد. در عوض شما مقدار متغیر را از `os.environ` می گیرید.\
۲. این یک شی `pathlib.Path` مناسب برای سیستم عامل فعلی ایجاد می کند.\
۳. شما می توانید اشیاء `pathlib.Path` جدید را با دنبال کردن یک مسیر والد با اسلش های جلو و رشته ها بسازید.\
۴. شی `pathlib.Path` خود یک متد `exists` دارد.\
۵. برای برگرداندن مسیر به صورت رشته، `as_postix` را فراخوانی کنید. بسته به مورد استفاده شما، می توانید خود شی `pathlib.Path` را برگردانید.\
۶. متد کلاس `pathlib.Path.cwd` یک شی `pathlib.Path` را برای دایرکتوری کاری فعلی برمی گرداند. این شی بلافاصله در اینجا برای ایجاد `config_path` با پیوستن آن به رشته `rc_name` استفاده می شود.\
۷. متد کلاس `pathlib.Path.home` یک شی `pathlib.Path` را برای دایرکتوری home کاربر فعلی برمی گرداند.\
۸. یک شی `pathlib.Path` با استفاده از مسیر نسبی ذخیره شده در فایل ایجاد کنید و سپس متد `resolve` آن را فراخوانی کنید تا مسیر مطلق را بدست آورید.\
۹. این یک شی `pathlib.Path` والد را مستقیماً از خود شی برمی‌گرداند.

