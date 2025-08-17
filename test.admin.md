تمرين:

أنشئ 3 مستخدمين (dev, ops, tester).

اعمل مجلد مشترك بينهم ووزّع الصلاحيات.

شغّل خدمة nginx وتأكد إنها تشتغل مع systemctl.


. إنشاء المستخدمين 

    sudo useradd dev

    sudo useradd ops

    sudo useradd tester


ممكن كمان تعين باسورد لكل واحد: 

    sudo passwd dev

    sudo passwd ops

    sudo passwd tester

2. إنشاء مجلد مشترك بينهم

نعمل مجلد وليكن /shared:

    sudo mkdir /shared

    نعمل جروب جديد علشان يكون مشترك بينهم:

    sudo groupadd project-team

    نضيف المستخدمين للجروب:

    sudo usermod -aG project-team dev

    sudo usermod -aG project-team ops

    sudo usermod -aG project-team tester

    نخلي المجلد ملك للجروب:

    sudo chown :project-team /shared

    ندي صلاحيات بحيث أي عضو في الجروب يقرأ ويكتب:

    sudo chmod 770 /shared

(ده معناه: المالك + الجروب عندهم read/write/execute، والباقي مفيش صلاحيات).
3. تشغيل خدمة Nginx
تثبيت Nginx (لو مش موجود):

Ubuntu/Debian:

    sudo apt update

    sudo apt install nginx -y

CentOS/RHEL:

    sudo yum install epel-release -y

    sudo yum install nginx -y

تشغيل الخدمة:

    sudo systemctl start nginx

تأكد إنها شغالة:

    sudo systemctl status nginx

خليها تشتغل أوتوماتيك مع الإقلاع:

    sudo systemctl enable nginx

4. اختبار الخدمة

افتح المتصفح على السيرفر:

http://localhost

أو لو سيرفر على شبكة:
http://<server-ip

هتلاقي صفحة Nginx الافتراضية.
__________________________________________________________
في لينكس عندك كذا طريقة للتبديل بين المستخدمين، هشرحلك أهمهم:
🔹 1. باستخدام su (switch user)

لو انت دلوقتي عامل login كمستخدم مثلاً ahmed وعايز تدخل على حساب dev:

    su - dev

    بعد ما تدخل الباسورد بتاع dev، هتتحول لجلسة جديدة تخصه بالكامل.

    علامة - معناها إنه هياخد environment variables بتاعة dev (زي كأنك سجلت دخول جديد).

للخروج والرجوع للمستخدم الأصلي:

    exit


🔹 2. باستخدام sudo -i -u

لو المستخدم الحالي عنده صلاحيات sudo (مثلاً انت root أو مستخدم في sudoers):

    sudo -i -u dev

ده هيفتح shell كأنك داخل dev من الأول.


🔹 3. فتح جلسة جديدة من غير ما تسيب القديمة

لو مش عايز تخرج من المستخدم الحالي، ممكن تعمل:


    su dev
_____________________________________________________________________________________________
بدل ما تعمل كل واحد لوحده، تقدر تكتب في سطر واحد:


    sudo groupadd project-team

    sudo useradd -m -G project-team dev

    sudo useradd -m -G project-team ops

    sudo useradd -m -G project-team tester

    هنا عملنا الجروب project-team

    وضفنا كل مستخدم للجروب مباشرة وقت الإنشاء (-G project-team)

    الخيار -m بيعمل للمستخدم مجلد home تلقائي.

🔹 تعديل جروب أكتر من مستخدم مرة واحدة

لو كانوا موجودين أصلاً وعايز تضيفهم للجروب دفعة واحدة:

    sudo usermod -aG project-team dev ops tester

🔹 عمل المجلد المشترك وتوزيع الصلاحيات بسرعة

    sudo mkdir /shared

    sudo chown :project-team /shared

    sudo chmod 2770 /shared

⚡ ملاحظات:

    2770 بدل 770 → الرقم 2 ده معناه تشغيل setgid على المجلد، وده بيخلي أي ملف جديد يتعمل جوه /shared يبقى تلقائيًا بنفس الجروب project-team، بدل ما يورث جروب صاحب الملف. (حاجة عملية جدًا).


🔹 إنشاء مستخدمين بكود واحد (loop)

لو عندك أسماء كتير ومش عايز تكتب كل مرة:


    for user in dev ops tester; do

    sudo useradd -m -G project-team $user
    
    echo "$user:1234" | sudo chpasswd   # يدي باسورد افتراضي 1234
    done


🔹 أسرع طريقة للتبديل بين المستخدمين

بدل ما تكتب su - user كل مرة، تقدر تكتب alias في .bashrc بتاعك:
مثلاً:

    alias sdev='sudo -i -u dev'

    alias sops='sudo -i -u ops'

    alias stester='sudo -i -u tester'

وتعمل:

    source ~/.bashrc

بعدها بمجرد تكتب:

    sdev

تدخل على dev على طول 😎

