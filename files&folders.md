🐧 تاسكات Linux Admin 1 عملية
📂 الملفات والمجلدات

1-اعمل مجلد اسمه /projects وجواه مجلدين frontend و backend.


2-انسخ ملفات الـ .conf من /etc وحط نسخة منها في /projects/backend/backup.


3-ابحث في /var/log عن أي ملف يحتوي كلمة error وانسخهم في ملف اسمه errors.txt.



🎯 المهمة الأولى

اعمل مجلد اسمه /projects وجواه مجلدين: frontend و backend.

الحل:# أنشئ مجلد projects

    mkdir /projects

# أنشئ مجلد frontend جواه
    mkdir /projects/frontend

# أنشئ مجلد backend جواه
    mkdir /projects/backend

لو عايز تعملهم كلهم في أمر واحد: 

    mkdir -p /projects/frontend /projects/backend

للتأكد: 

    ls -l /projects
__________________________________
🎯 المهمة الثانية

انسخ ملفات الـ .conf من مجلد /etc وحط نسخة منها في /projects/backend/backup.

الخطوات:

الأول نعمل مجلد اسمه backup جوه backend:

    mkdir /projects/backend/backup
دلوقتي ننسخ أي ملف نهايته .conf من /etc ونحطه هناك:
     
    cp /etc/*.conf /projects/backend/backup/
هنا إحنا نسخنا بس الملفات اللي موجودة مباشرة في /etc، مش اللي جوا مجلداته الداخلية.
خليناها كده بسيطة مبدئيًا.

للتأكد:

    ls /projects/backend/backup
هيظهرلك ملفات كتير جدًا زي:

    adduser.conf  debconf.conf  host.conf  sysctl.conf  ...

__________________________________

🎯 المهمة الثالثة

ابحث في /var/log عن أي ملف فيه كلمة error وخلي النتائج كلها في ملف اسمه errors.txt.

الخطوات البسيطة:

نستعمل أمر grep للبحث عن كلمة error داخل كل الملفات في /var/log.
ونعمل تحويل للنتيجة إلى ملف errors.txt جوه /projects:

    grep -R "error" /var/log > /projects/errors.txt
   
    
    
    grep = يبحث عن نص.
    -R = recursive (يدخل جوا المجلدات).
    "error" = الكلمة اللي بندوّر عليها.
     /var/log = المكان اللي نبحث فيه.
    > /projects/errors.txt = نحفظ النتيجة في ملف.

للتأكد: 

     ls /projects
     cat /projects/errors.txt | head -n 20

هتلاقي أسطر شبه دي:

    /var/log/syslog:Sep  4 12:01:23 server systemd[1]: error reading message
    /var/log/auth.log:Sep  4 12:02:01 server sshd[1234]: authentication error
...
