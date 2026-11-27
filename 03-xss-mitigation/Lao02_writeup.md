
Stored XSS Prevention using OWASP AntiSamy

1. Root Cause 

មូលហេតុគឺ Java Controller រក្សាទុក User Input ទៅក្នុង Database ដោយផ្ទាល់ ដោយមិនបានពិនិត្យ HTML Content ជាមុន។

នៅក្នុង Code ដើម Function `saveNewComment()` បានបញ្ជូន `newComment` ទៅកាន់ `MyCommentDAO.addComment()` ដោយផ្ទាល់។ ប្រសិនបើ Comment មាន Malicious JavaScript វាអាចត្រូវបានរក្សាទុកនៅក្នុង Database ហើយអាចបង្កជា Stored XSS នៅពេល Website បង្ហាញ Comment នោះ។

2. Evidence 

នៅក្នុង Code ដើម Application រក្សាទុក User Input ដោយផ្ទាល់៖

```java
MyCommentDAO.addComment(threadID, userID, newComment);
```

ដើម្បីការពារ Stored XSS ខ្ញុំបានកែប្រែ Function `saveNewComment()` ដោយប្រើ OWASP AntiSamy និង Policy File `antisamy-slashdot.xml`។

Code ដែលខ្ញុំបានប្រើ៖

```java
Policy p = Policy.getInstance("antisamy-slashdot.xml");
AntiSamy as = new AntiSamy();
CleanResults cr = as.scan(newComment, p, AntiSamy.DOM);
MyCommentDAO.addComment(threadID, userID, cr.getCleanHTML());
```

បន្ទាប់ពីបញ្ចូល Code នេះ WebGoat បានទទួលយកដំណោះស្រាយ ហើយបង្ហាញថា Lab បានជោគជ័យ។

![alt text](<screenshots/Lap02 1.png>)
![alt text](<screenshots/Lap02 2.png>)
![alt text](<screenshots/Lap02 3.png>)

3. Why Your Payload Works 

ដំណោះស្រាយនេះដំណើរការដោយប្រើ OWASP AntiSamy ដើម្បីពិនិត្យ និងសម្អាត User Input មុនពេលរក្សាទុកទៅក្នុង Database។
ជំហាននៃការដំណើរការ៖

1. `Policy.getInstance("antisamy-slashdot.xml")` Load Policy File ដែលកំណត់ច្បាប់សម្រាប់ HTML Content ដែលអនុញ្ញាត។
2. `new AntiSamy()` បង្កើត AntiSamy Object។
3. `as.scan(newComment, p, AntiSamy.DOM)` ពិនិត្យ និងសម្អាត Comment ដោយអនុវត្តតាម Policy File។
4. `CleanResults cr` រក្សាទុកលទ្ធផលនៃការសម្អាត។
5. `cr.getCleanHTML()` ទាញយក HTML ដែលបានសម្អាត។
6. `MyCommentDAO.addComment()` រក្សាទុក Clean HTML ទៅក្នុង Database ជំនួសឱ្យ Original User Input។

ដូច្នេះ Malicious HTML ឬ JavaScript ដែលមិនត្រូវបានអនុញ្ញាតដោយ Policy អាចត្រូវបានលុប ឬកែប្រែ មុនពេលរក្សាទុក។


 4. Impact
ប្រសិនបើ Stored XSS មិនត្រូវបានការពារ Attacker អាចបញ្ចូល Malicious JavaScript ទៅក្នុង Comment ហើយរក្សាទុកវានៅក្នុង Database។

នៅពេល User ផ្សេងៗចូលមើល Comment នោះ Script អាចត្រូវបាន Execute នៅក្នុង Browser របស់ពួកគេ។ វាអាចត្រូវបានប្រើដើម្បីលួចព័ត៌មានដែលអាចចូលប្រើបានតាម Browser បង្ហាញ Fake Content ឬធ្វើសកម្មភាពជំនួស User ក្នុង Context របស់ Website។

 6. Prevention 

គួរប្រើ OWASP AntiSamy ដើម្បីពិនិត្យ និងសម្អាត User-Generated HTML មុនពេលរក្សាទុក ឬបង្ហាញវា ដោយប្រើ Policy File ដែលសមស្រប។ លើសពីនេះ គួរតែ Encode Output តាម HTML Context នៅពេលបង្ហាញ User Input ដើម្បីការពារ Browser ពីការប្រតិបត្តិ Malicious Script។
