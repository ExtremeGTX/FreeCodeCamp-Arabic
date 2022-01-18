# كيفية إعادة توجيه HTTP إلى HTTPS باستخدام htaccess
![](https://cdn-media-2.freecodecamp.org/w1280/5f9ca210740569d1a4ca5257.jpg)
بدأ كلا من كروم (Chrome) و فايرفوكس (Firefox) بإظهار تحذير "غير آمن" على مواقع الويب التي ليس لديها [شهادات SSL](https://www.instantssl.com/ssl.html) . بدون SSL , سيظهر موقعك على أنه غير آمن عندما يزوره المستخدمون. لذلك من الضروري إستخدام إتصال SSL مشفر من أجل الأمان و سهولة الوصول و/أو الإلتزام بتحقيق معايير [PCI](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). و بناءا عليه فإنه من المهم تحويل إتصالات HTTP إلى HTTPS.

![](https://cdn-media-1.freecodecamp.org/images/0*wUTFJrRSM2vh1H7v.jpg)

### ماهو SSL ؟
إن  SSL (Secure Sockets Layer) هو بروتوكول أمان يقوم بإنشاء إتصال مشفر بين خادم الويب (Web Server) و متصفح الويب (Web Browser).

إن إستخدام تقنية SSL تضمن أن جميع البيانات التي يتم تبادلها بين خادم الويب (Web Server) و متصفح الويب (Web Browser) تبقى مشفرة.

لعمل إتصال SSL سوف تحتاج إلى **شهادة SSL**. لعمل هذه الشهادة تحتاج لتحديد كافة المعلومات التي تعرف هوية موقعك و بناءً على هذه المعلومات سيتم إنشاء مفتاحي تشفير - مفتاح خاص (Private Key) و مفتاح عام (Public Key) .

[تعرف أكثر على: _لماذا SSL مهمة ؟_](https://www.sslrenewals.com/blog/why-is-ssl-important-benefits-of-using-ssl-certificate)

حتى تقوم بفرض إستخدام HTTPS على موقعك , يجب أن تقوم ببعض التعديلات على ملف **.htaccess**

قبل أن ننتقل إلى تحويل إتصال HTTP إلى HTTPS , سنتعرف على كيفية تعديل ملف .htaccess , إذا كنت تعرف كيف تقوم بذلك يمكنك تخطي هذا الجزء ثم متابعة الجزء الخاص بتحويل الإتصال.

### تحرير ملف .htaccess
يحتوي هذا الملف على العديد من التعليمات/التوجيهات التى تخبر الخادم (server) بكيفية التصرف في مواقف معينة و تؤثر أيضا بشكل مباشر في الطريقة التي يعمل بها موقعك.
من التوجيهات (Directives) الشائعة في ملف .htaccess :
-   Redirects (و تعني التحويلات)
-   Rewriting URLs (و تعني إعادة كتابة عنوان URL)

**طرق تحرير ملف .htaccess:**
1. تحرير الملف على جهازك الشخصي ثم رفعه إلى الخادم باستخدام FTP.
2. إستخدام وضع "التحرير" في برنامج ال FTP الذي يسمح لك بتحرير الملف بشكل مباشرة على الخادم.
3. إستخدام SSH و محرر نصوص.
4. إستخدام مدير الملفات File Manager في **cPanel** لتحرير الملف.

### تحرير ".htaccess" في مدير ملفات (File manager) cPanel
**ملحوظة:** قم بأخذ نسخة إحتياطية من موقعك قبل البدأ في التعديلات.
1. قم بتسجيل الدخول في cPanel
2. إذهب إلى Files > File Manager > Document Root for:
3. قم باختيار اسم النطاق (Domain Name) الذي تريد الوصول إليه.
4. إختر إظهار الملفات المخفية "Show Hidden Files (dotfiles)"
5. إضغط على "Go"
6. سيتم فتح نافذة أو تبويب جديد , إبحث عن ملف ".htaccess".
7. انقر بزر الماوس الأيمن على ملف .htaccess و إختر "Code Edit".
8. من الممكن أن يظهر لك نافذة لتسألك عن الترميز "encoding" , إضغط على "edit" للإستمرار.
9. قم بالتعديلات
10. إضغط على Save Changes عند الإنتهاء.
11. قم بتجربة موقعك و التأكد أن التعديلات تمت بالشكل الصحيح , في حالة حدوث مشكلة يمكنك استعادة الإصدار السابق والمحاولة مرة أخرى.
12. عندما تنتهي , إضغط على "Close" لإغلاق النافذة

### تحويل إتصال HTTP إلى HTTPS
#### 1. إعادة توجيه حركة بيانات الويب Web traffic
أضف ما يلي إلى ملف ".htaccess":
```
RewriteEngine On
RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://www.yourdomain.com/$1 [R,L]
```
#### 2. إعادة توجيه نطاق محدد فقط
للقيام بإعادة توجيه نطاق محدد لإستخدام HTTPS , أضف ما يلي:
```
RewriteEngine On
RewriteCond %{HTTP_HOST} ^yourdomain\.com [NC]
RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://www.yourdomain.com/$1 [R,L]
```
#### 3. إعادة توجيه مجلد محدد فقط
للقيام بإعادة توجيه مجلد محدد لإستخدام HTTPS , أضف ما يلي:
```
RewriteEngine On
RewriteCond %{SERVER_PORT} 80
RewriteCond %{REQUEST_URI} folder
RewriteRule ^(.*)$ https://www.yourdomain.com/folder/$1 [R,L]
```
ملحوظة: إستبدل _`“yourdomain”`_ بعنوان موقعك الحقيقي. أيضا في حالة ال"مجلد" إستبدل _`/folder`_ بإسم المجلد الفعلي.

إذا إستفدت من هذا الموضوع, قم بمشاركته مع الآخرين للتعرف أكثر على HTTPS.

![](https://cdn-media-1.freecodecamp.org/images/0*P6EKtlMMzyIXNRMw.png)