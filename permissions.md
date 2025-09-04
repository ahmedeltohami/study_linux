🔐 الصلاحيات

1-خلي مجلد /projects/frontend يقرأ ويكتب فيه dev بس.

2-خلي ops يقرأ logs من /var/log لكن مايقدرش يمسحها.

3-خلي tester يقدر يشغل سكربت معين فقط باستخدام sudo.


خلي مجلد /projects/frontend يقرأ ويكتب فيه dev بس
الخطوات:

# غيّر الملكية للمجلد وخليه ملك للـ user dev
   
    sudo chown dev:dev /projects/frontend

# خلي الصلاحيات 700 → dev يقرأ ويكتب ويدخل، غيره لأ
    sudo chmod 700 /projects/frontend
النتيجة:

dev يقدر يدخل ويقرأ ويكتب.

أي يوزر تاني (حتى ops أو tester) مش هيقدر.
للتأكد:

    ls -ld /projects/frontend
هتلاقي:

    drwx------ 2 dev dev 4096 Sep  4 14:12 /projects/frontend
_______________________________________________
خلي ops يقرأ logs من /var/log لكن مايقدرش يمسحها

هنا نستخدم Group + صلاحيات القراءة فقط.

الخطوات:

نعمل جروب خاص للقراءة من الـ logs:
   
    sudo groupadd logreaders

نضيف ops للجروب:
    
    sudo usermod -aG logreaders ops

نغيّر group لمجلد /var/log ونخليه ملك لجروب logreaders:

    sudo chown root:logreaders /var/log

ندي صلاحيات قراءة بس للجروب:

     sudo chmod 750 /var/log

النتيجة:

root يقدر يعمل كل حاجة.

أي حد في جروب logreaders (يعني ops) يقدر يقرأ الملفات ويدخل المجلد.

مفيش صلاحيات كتابة → مش هيقدر يمسح.
___________________________________________________
خلي tester يقدر يشغّل سكربت معين فقط باستخدام sudo
الخطوات:

نفترض عندنا سكربت اسمه /usr/local/bin/test-script.sh.
(ممكن تعمله كده للتجربة):

     echo 'echo "Hello from test script"' | sudo tee /usr/local/bin/test-script.sh
     sudo chmod +x /usr/local/bin/test-script.sh

افتح إعدادات sudo:

    sudo visudo

أضف السطر ده في الآخر:
    
    tester ALL=(ALL) NOPASSWD: /usr/local/bin/test-script.sh

النتيجة:

tester
يقدر يشغّل بس السكربت ده بـ 
sudo:

    
    sudo /usr/local/bin/test-script.sh
لو جرّب يشغل أي حاجة تانية بـ sudo → مش هيشتغل.
