# عملية إقلاع نظام ليونكس - 6 خطوات مشروحة بالتفصيل

![The Linux Booting Process - 6 Steps Described in Detail](https://cdn-media-2.freecodecamp.org/w1280/5f9c9cef740569d1a4ca34fc.jpg)
نظام التشغيل هو مجموعة برمجيات مسؤولة عن إدارة موارد الحاسب (Hardware Resources) و التحكم في الأجهزة الملحقة المتصلة بالحاسب (Peripherals) و توفير الخدمات الأساسية للبرمجيات الأخرى. في نظام تشغيل ليونكس (Linux) تتكون عملية الإقلاع (Booting) من 6 مراحل مختلفة.

### **1.نظام البيوس BIOS**
نظام بيوس (BIOS) و تعني نظام الإدخال\الإخراج الأساسي (Basic Input/Output System) و هو البرمجية الأولى داخل الحاسب التى تعمل و التي بدورها تقوم بتجهيز العتاد (Hardware) لتحميل نظام التشغيل. لبدأ تحميل نظام التشغيل يقوم نظام بيوس بتحميل و تشغيل سجل الإقلاع الرئيسي (Master Boot Record (MBR)).

عندما تقوم بتشغيل حاسوبك, يبدأ عمل نظام بيوس (BIOS) حيث يقوم ببعض الإختبارات/الفحوصات لكل الأقراص الصلبة (HDD) و/أو الأقراص الثابتة (SSD).

بعد ذلك يقوم نظام بيوس (BIOS) بالبحث عن محمل الإقلاع (Boot loader) الموجود في سجل الإقلاع الرئيسي (Master Boot Record (MBR)) ثم تحميله ثم تشغيله.
يمكن أيضا أن يتواجد MBR على وحدة (USB Stick) أو قرص مضغوط (CD-ROM) مثل أقراص التثبيت المباشر لليونكس (Linux Live Installation CD).

عندما يجد نظام (BIOS) محمل اللإقلاع (Boot loader) يقوم بتحميله إلى الذاكرة (RAM) ثم يقوم بتنفيذه , و بذلك يعطي نظام (BIOS) مسؤولية التحكم في النظام إلى محمل الإقلاع (Boot loader).

### **2. برمجية MBR**
إن MBR تعني سجل الإقلاع الرئيسي (Master Boot Record) و هو المسؤول عن تحميل و تشغيل محمل الإقلاع (GRUB Bootloader).

إن MBR يوجد في القطاع الأول (1st sector) من وحدة التخزين المستخدمة في عملية الإقلاع, التي غالبا تكون في `/dev/hda` أو  `/dev/sda` على حسب العتاد (Hardware).
أيضا يحتوي MBR على معلومات بخصوص GRUB (أو محمل الإقلاع LILO في الأنظمة القديمة جدا).

### **3. برمجية GRUB**
أحيانا يسمى بـ GNU GRUB و التي تعني GNU GRand Unified Bootloader , و هو محمل الإقلاع الشائع إستخدامه على أغلبية أنظمة ليونكس (Linux) الحديثة.

 إن شاشة بداية GRUB أو (GRUB Splash screen) غالبا ستكون أول ما تراه عندما يبدأ حاسوبك عملية الإقلاع (Booting). يمتلك GRUB قائمة بسيطة تعطيك بعض الخيارات , على سبيل المثال لو كان لديك العديد من الأنوية (Kernel Images) المثبتة على جهازك, يمكنك استخدام لوحة المفاتيح (Keyboard) لتختار أي نواة (Kernel) تريد أن تستخدمها لاستكمال عملية الإقلاع. الوضع الإفتراضي هو إختيار أحدث نواة (latest Kernel image).

 ستنتظر شاشة بدأ GRUB لبضع ثوان لتمكنك من تحديد خياراتك, ولكن في حالة لم تقم باختيار شيء , سيتم إختيار النواة الإفتراضية (default kernel).

 في العديد من الأنظمة يمكنك أن تجد ملف إعدادات GRUB في هذا المسار `/boot/grub/grub.conf`  أو  `/etc/grub.conf`.
 هنا مثال على محتوى ملف `grub.conf`:

```text
#boot=/dev/sda
default=0
timeout=5
splashimage=(hd0,0)/boot/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.18-194.el5PAE)
      root (hd0,0)
      kernel /boot/vmlinuz-2.6.18-194.el5PAE ro root=LABEL=/
      initrd /boot/initrd-2.6.18-194.el5PAE.img
```

### **4. النواة Kernel**
تعتبر النواة (kernel) هي قلب أي نظام تشغيل (مثلا نظام ليونكس). النواة تتحكم بشكل كامل في النظام.

في هذه المرحلة من عملية الإقلاع (Booting process) , النواة (kernel) التي إختارها GRUB ستقوم بعملية تجهيز (mounting) لنظام الملفات الرئيسي (root file system) الذي تم تعريفه داخل ملف الإعدادات `grub.conf` . بعد ذلك تقوم النواة (Kernel) بتنفيذ برنامج `/sbin/init` و وهو أول برنامج يعمل في النظام, يمكنك التأكد من ذلك عن ملاحظة الرقم التعريفي للعملية (Process ID) أو (PID) و ستجد أنه يمتلك القيمة `1`.

لتجهيز نظام الملفات الرئيسي (root file system) , تقوم النواة (Kernel) أولا باستخدام نظام ملفات مؤقت (temporary root file system) حتى تتمكن من إيجاد و تجهيز نظام الملفات الحقيقي (real root file system).

### **5. برنامج Init**
عند هذه النقطة من عملية الإقلاع, يقوم النظام بتشغيل برامج مايسمى بـ (برامح مستوى التشغيل (runlevel programs)) , سوف يقوم برنامج init بقراءة ملف  `/etc/inittab` حتى يقرر ما يعرف بـ (مستوى تشغيل ليونكس) (Linux runlevel).

أنظمة ليونكس الحديثة تستخدم برنامج systemd لاختيار مستوى التشغيل (runlevel) بدلا من نظام init القديم.
بحسب [TecMint](https://www.tecmint.com/change-runlevels-targets-in-systemd/), هذه هي مستويات التشغيل (runlevels) المتاحة في systemd:


| مستوى التشغيل | الوصف | اسم ملف مستوى التشغيل |
| -------- | -------- | -------- |
| مستوى 0     | يستخدم في حالة إغلاق الحاسب (poweroff)    | poweroff.target     |
| مستوى 1     | يستخدم في حالة الإنقاذ (rescue)     | rescue.target     |
| مستوى 3     | يستخدم في حالة التشغيل متعدد المستخدمين (multi-user)     | multi-user.target     |
| مستوى 5     | يستخدم في حالة التشغيل متعدد المستخدمين (multi-user) مع الواجهة الروسومية (graphical user interface)   | graphical.target     |
| مستوى 6     | يستخدم في حالة  إعادة التشغيل (reboot)     | reboot.target     |
| مستوى الطوارئ     | يستخدم في حالة الطوارئ (emergency)     | emergency.target     |

سيقوم systemd بتنفيذ برامج مستوى التشغيل (runlevel programs).

### **6. برامج مستوى التشغيل Runlevel programs**
على حسب توزيعة ليونكس (Linux distribution) المثبتة , ستجد إختلافات في الخدمات التي تعمل عند اللإقلاع, على سبيل المثال: من الممكن أن ترى `starting sendmail …. OK`

هذه الخدمات تعرف بـبرامج مستوى التشغيل (runlevel programs) , ويتم تنفيذها من مختلف المجلدات (directories) على حسب مستوى التشغيل (runlevel) , كل من ال 6 مستويات التشغيل المذكورة أعلاه لها المجلد (directory) الخاص بها:

-   Run level 0 –  `/etc/rc0.d/`
-   Run level 1 –  `/etc/rc1.d/`
-   Run level 2  –  `/etc/rc2.d/`
-   Run level 3  –  `/etc/rc3.d/`
-   Run level 4 –  `/etc/rc4.d/`
-   Run level 5 –  `/etc/rc5.d/`
-   Run level 6 –  `/etc/rc6.d/`

ملحوظة: أماكن المجلدات أعلاه تختلف من توزيعة ليونكس إلى توزيعة أخرى.

لو قمت بالنظر بأحد المجلدات أعلاه, ستجد برامج تبدأ بحرف `S` أو `K` وهي ل Startup أو Kill .
برامج البدء (Startup programs) تعمل عند بدأ تشغيل النظام (system startup), و برامج الإغلاق (kill programs) تعمل عند إغلاق النظام (system shutdown).
هذا هو كل شئ تحتاج أن تعرفه عن عملية إقلاع نظام ليونكس (Linux Booting process), لتعرف تفاضيل أكتر يمكنك قراءة المزيد عبر ويكي أرك [ArchWiki](https://wiki.archlinux.org/title/Arch_boot_process)
