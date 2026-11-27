
 Lab: Identify Potential for DOM-Based XSS
 1. Root Cause 

DOM-Based Cross-Site Scripting (DOM-Based XSS) ជាVulnerability ដែលកើតឡើងនៅពេលJavaScriptនៅក្នុង Browser យកទិន្នន័យពី User Input ទៅបញ្ចូលក្នុង DOM ដោយមិនបានការពារឱ្យបានត្រឹមត្រូវ។

ក្នុងLabនេះ ខ្ញុំបានពិនិត្យ JavaScript Source Code របស់ WebGoat តាមរយៈ Developer Tools ដើម្បីស្វែងរក Route Configuration ដែលមាន Test Code នៅក្នុង Application។

ខ្ញុំបានរកឃើញ Route ដែលមានឈ្មោះ `testRoute`។ Route នេះអាចទទួល Parameter តាមរយៈ URL ហើយបញ្ជូនទៅកាន់ JavaScript Route Handler។

2. Evidence 

ខ្ញុំបានបើក Browser Developer Tools ហើយចូលទៅកាន់ JavaScript Source Code ដើម្បីពិនិត្យ Route Configuration របស់ WebGoat។

ខ្ញុំបានរកឃើញ Code ដូចខាងក្រោម៖

```javascript
'test/:param': 'testRoute',
```

**Base Route:**
```text
start.mvc#test
```

**Route Found:**
```text
test/:param
```

Route នេះប្រើ `:param` ដើម្បីទទួល Parameter ពី URL ហើយហៅ `testRoute` Handler។
![alt text](<screenshots/lab03 2.png>)

បន្ទាប់ពីសាកល្បងក្នុង WebGoat ខ្ញុំបានទទួល Result នៅក្នុង Browser ដូចដែលបង្ហាញក្នុង Screenshot ខាងក្រោម។
![alt text](<screenshots/lab03 1.png>)


 3. Why My Payload Works 

DOM-Based XSS អាចកើតឡើងនៅពេល JavaScript យក User Input ពី URL ឬប្រភពផ្សេងទៀតទៅបង្ហាញក្នុង DOM ដោយមិនបាន Encode ឬ Sanitize ឱ្យបានត្រឹមត្រូវ។

ក្នុង Lab នេះ Route `test/:param` ទទួល Parameter ពី URL ហើយបញ្ជូនទៅ `testRoute` Handler។

នៅពេល JavaScript ប្រើទិន្នន័យនោះដោយមិនមានការការពារត្រឹមត្រូវ Browser អាចចាត់ទុកទិន្នន័យនោះជា HTML ឬ JavaScript Code ហើយអាចបង្កឱ្យមាន DOM-Based XSS។

 4. What I Tried That Failed

ខ្ញុំបានពិនិត្យ JavaScript Source Code តាមរយៈ Developer Tools និងស្វែងរក Route Configuration។

ដោយស្វែងរក Route ដែលមាន Test Code មុននឹងអាចបន្តការសាកល្បងបាន។ប៉ុន្តែមិនមាន Failed Payload ផ្សេងទៀតសម្រាប់បង្ហាញ។

 5. Impact

ប្រសិនបើ Website ពិតប្រាកដមាន DOM-Based XSS អ្នកវាយប្រហារអាចបង្កើត URL ដែលមាន Malicious Input ហើយបញ្ឆោតឱ្យជនរងគ្រោះបើក URL នោះ។
JavaScript ដែលមានគ្រោះថ្នាក់អាចដំណើរការនៅក្នុង Browser របស់ជនរងគ្រោះ ហើយអាចបង្ហាញព័ត៌មានក្លែងក្លាយ អានទិន្នន័យដែល JavaScript អាចចូលប្រើបាន ឬធ្វើសកម្មភាពក្នុងនាមអ្នកប្រើប្រាស់។

 6. Prevention 
Developer គួរតែជៀសវាងការប្រើ Unsafe DOM Methods ដូចជា `innerHTML` ជាមួយ User Input ដែលមិនទាន់មានសុវត្ថិភាព ហើយគួរប្រើ `textContent` សម្រាប់បង្ហាញ Text ធម្មតា។

លើសពីនេះ គួរតែធ្វើការ Validate និង Sanitize Input ឱ្យបានត្រឹមត្រូវ និងប្រើ Content Security Policy (CSP) ជាស្រទាប់ការពារបន្ថែម។

Conclusion

តាមរយៈ Lab នេះ បានសិក្សាអំពី DOM-Based XSS និងរបៀបស្វែងរក Route Configuration នៅក្នុង JavaScript Source Code របស់ WebGoat។
ខ្ញុំបានរកឃើញ Route `test/:param` ដែលភ្ជាប់ទៅកាន់ `testRoute` Handler។ Lab នេះបានជួយឱ្យខ្ញុំយល់ដឹងបន្ថែមអំពីរបៀបដែល User Input អាចបង្កើត DOM-Based XSS ប្រសិនបើមិនមានការការពារត្រឹមត្រូវ។
