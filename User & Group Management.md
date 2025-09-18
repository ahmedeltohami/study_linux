لسيناريو:
الشركة عندها 3 تيمز: dev, ops, و tester. المطلوب منك كـ DevOps Engineer إنك:

تعمل جروب لكل تيم.

تعمل يوزر لكل شخص:

dev1 ينضم لجروب dev

ops1 ينضم لجروب ops

tester1 ينضم لجروب tester

اعمل يوزر جديد اسمه deploy (ده System User خاص بالتطبيقات).

خلي جروب dev هو الأونر بتاع مجلد src.

خلي جروب ops هو الأونر بتاع مجلد configs.

خلي جروب tester هو الأونر بتاع مجلد logs.

اعمل list بالأوامر دي علشان نراجع:

ls -ld backend/* (يبين الـ ownership بتاع كل مجلد).

cat /etc/passwd | grep -E "dev1|ops1|tester1|deploy" (يبين المستخدمين اللي عملتهم).

cat /etc/group | grep -E "dev|ops|tester" (يبين الجروبات).

1️⃣ إنشاء الجروبات

    sudo groupadd dev
    sudo groupadd ops
    sudo groupadd tester
    🔎 شرح:

groupadd → بيعمل جروب جديد.

dev / ops / tester → أسماء الجروبات (واحد لكل team).
➡️ دلوقتي كل تيم هيبقى عنده جروب مستقل يتجمع فيه الأعضاء.

------
2️⃣ إنشاء المستخدمين وربطهم بالجروبات

    sudo useradd -m -s /bin/bash -G dev dev1
    sudo passwd dev1


🔎 شرح:

useradd → بيعمل يوزر جديد.

-m → يعمل له home directory (مثلاً /home/dev1).

-s /bin/bash → الـ login shell بتاعه هيكون bash (يعني يقدر يدخل ويكتب أوامر).

-G dev → يخلي اليوزر dev1 عضو في جروب dev.

dev1 → اسم اليوزر.

بعد كده passwd dev1 بتحدد له باسورد.

نكرر نفس الخطوات للباقي:


    sudo useradd -m -s /bin/bash -G ops ops1
    sudo passwd ops1

    sudo useradd -m -s /bin/bash -G tester tester1
    sudo passwd tester1


➡️ كده بقى عندنا:

dev1 في جروب dev

ops1 في جروب ops

tester1 في جروب tester
----
3️⃣ إنشاء System User للتطبيق

    sudo useradd -r -s /sbin/nologin deploy


🔎 شرح:

-r → يعمل system user (للتطبيقات أو الخدمات مش للبشر).

-s /sbin/nologin → يمنع أي حد يدخل باليوزر ده (لأمان أكتر).

deploy → اسمه.
➡️ الهدف: نخلي السيرفر يشغل التطبيق باليوزر deploy بدل ما نستخدم root أو يوزر عادي.
----
4️⃣ توزيع ملكية المجلدات على التيمات

    sudo chown :dev /home/ahmed/projects/backend/src
    sudo chown :ops /home/ahmed/projects/backend/configs
    sudo chown :tester /home/ahmed/projects/backend/logs


🔎 شرح:

chown → يغير ملكية الملفات/المجلدات.

:dev → يخلي الجروب owner هو dev (من غير ما يغير الـ user owner).

/home/ahmed/projects/backend/src → المسار بتاع المجلد.
➡️ النتيجة:

src ملك لجروب dev.

configs ملك لجروب ops.

logs ملك لجروب tester.
---
5️⃣ مراجعة النتائج
أ) عرض المجلدات وملكيتها

    ls -ld /home/ahmed/projects/backend/*


🔎 شرح:

ls -ld → يعرض تفاصيل المجلد نفسه (مش الملفات اللي جواه).

النتيجة المتوقعة:
 
    drwxr-xr-x 2 ahmed dev     26 Aug 18 00:28 src
    drwxr-xr-x 2 ahmed ops     22 Aug 18 00:29 configs
    drwxr-xr-x 2 ahmed tester  26 Aug 18 00:36 logs


➡️ باين إن الـ group owner اتغير صح لكل مجلد.

ب) التأكد من المستخدمين

    cat /etc/passwd | grep -E "dev1|ops1|tester1|deploy"


🔎 شرح:

/etc/passwd → ملف فيه كل المستخدمين.

grep -E → يدور على أكتر من كلمة (Regular Expression).

"dev1|ops1|tester1|deploy" → عرض السطور اللي تخص المستخدمين دي.

النتيجة المتوقعة:


    dev1:x:1001:1002::/home/dev1:/bin/bash
    ops1:x:1002:1003::/home/ops1:/bin/bash
    tester1:x:1003:1004::/home/tester1:/bin/bash
    deploy:x:999:999::/home/deploy:/sbin/nologin

ج) التأكد من الجروبات

    cat /etc/group | grep -E "dev|ops|tester"


🔎 شرح:

/etc/group → ملف فيه بيانات كل الجروبات.

النتيجة المتوقعة:


    dev:x:1002:dev1
    ops:x:1003:ops1
    tester:x:1004:tester1


➡️ كل جروب موجود واليوزر بتاعه عضو فيه.

💡 النتيجة النهائية

التيمات اتقسمت بجروبات.

كل عضو في مكانه.

المجلدات ملك للتيم المناسب.

عندنا system user (deploy) لتشغيل التطبيق بشكل آمن.
