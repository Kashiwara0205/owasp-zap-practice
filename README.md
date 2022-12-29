# owasp-zap-practice

owasp-zapを試しに稼働させたのでメモ

## goat docker-compose

攻撃対象となるwebgoat用意


```
version: '3'

services:
  web:
    image: webgoat/webgoat-8.0
    ports:
      - "8050:8080"
    tty: true
```

## owasp-zapイメージを用意する

```
docker pull owasp/zap2docker-weekly
```


## 脆弱性診断を実施する

```
# webgoat立ち上げ
docker-compose up

# 192から始まるIP抽出(localhostでのアクセスができないため)
# mac版
ifconfig | grep "inet " | grep -v 127.0.0.1 | cut -d\  -f2

# 攻撃
docker run -t owasp/zap2docker-weekly zap-baseline.py -t http://192.168.xxx.xxx:8050/WebGoat
```

## 実行結果

```
Total of 19 URLs
PASS: Vulnerable JS Library (Powered by Retire.js) [10003]
PASS: In Page Banner Information Leak [10009]
PASS: Cookie Without Secure Flag [10011]
PASS: Re-examine Cache-control Directives [10015]
PASS: Cross-Domain JavaScript Source File Inclusion [10017]
PASS: Content-Type Header Missing [10019]
PASS: Anti-clickjacking Header [10020]
PASS: X-Content-Type-Options Header Missing [10021]
PASS: Information Disclosure - Debug Error Messages [10023]
PASS: Information Disclosure - Sensitive Information in URL [10024]
PASS: Information Disclosure - Sensitive Information in HTTP Referrer Header [10025]
PASS: HTTP Parameter Override [10026]
PASS: Information Disclosure - Suspicious Comments [10027]
PASS: Open Redirect [10028]
PASS: Cookie Poisoning [10029]
PASS: User Controllable Charset [10030]
PASS: Viewstate [10032]
PASS: Directory Browsing [10033]
PASS: Heartbleed OpenSSL Vulnerability (Indicative) [10034]
PASS: Strict-Transport-Security Header [10035]
PASS: HTTP Server Response Header [10036]
PASS: Server Leaks Information via "X-Powered-By" HTTP Response Header Field(s) [10037]
PASS: X-Backend-Server Header Information Leak [10039]
PASS: Secure Pages Include Mixed Content [10040]
PASS: HTTP to HTTPS Insecure Transition in Form Post [10041]
PASS: HTTPS to HTTP Insecure Transition in Form Post [10042]
PASS: User Controllable JavaScript Event (XSS) [10043]
PASS: Big Redirect Detected (Potential Sensitive Information Leak) [10044]
PASS: Retrieved from Cache [10050]
PASS: X-ChromeLogger-Data (XCOLD) Header Information Leak [10052]
PASS: CSP [10055]
PASS: X-Debug-Token Information Leak [10056]
PASS: Username Hash Found [10057]
PASS: X-AspNet-Version Response Header [10061]
PASS: PII Disclosure [10062]
PASS: Timestamp Disclosure [10096]
PASS: Hash Disclosure [10097]
PASS: Cross-Domain Misconfiguration [10098]
PASS: Weak Authentication Method [10105]
PASS: Reverse Tabnabbing [10108]
PASS: Modern Web Application [10109]
PASS: Dangerous JS Functions [10110]
PASS: Private IP Disclosure [2]
PASS: Session ID in URL Rewrite [3]
PASS: Script Passive Scan Rules [50001]
PASS: Stats Passive Scan Rule [50003]
PASS: Insecure JSF ViewState [90001]
PASS: Java Serialization Object [90002]
PASS: Sub Resource Integrity Attribute Missing [90003]
PASS: Charset Mismatch [90011]
PASS: Application Error Disclosure [90022]
PASS: WSDL File Detection [90030]
PASS: Loosely Scoped Cookie [90033]
WARN-NEW: Cookie No HttpOnly Flag [10010] x 1 
	http://192.168.0.5:8050/WebGoat/ (302 Found)
WARN-NEW: User Controllable HTML Element Attribute (Potential XSS) [10031] x 5 
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
WARN-NEW: Content Security Policy (CSP) Header Not Set [10038] x 5 
	http://192.168.0.5:8050/WebGoat (200 OK)
	http://192.168.0.5:8050/WebGoat/login (200 OK)
	http://192.168.0.5:8050/WebGoat/login?error (200 OK)
	http://192.168.0.5:8050/WebGoat/registration (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
WARN-NEW: Non-Storable Content [10049] x 11 
	http://192.168.0.5:8050/WebGoat (302 Found)
	http://192.168.0.5:8050/WebGoat/ (302 Found)
	http://192.168.0.5:8050/WebGoat/start.mvc (302 Found)
	http://192.168.0.5:8050/WebGoat/login (302 Found)
	http://192.168.0.5:8050/ (404 Not Found)
WARN-NEW: Cookie without SameSite Attribute [10054] x 1 
	http://192.168.0.5:8050/WebGoat/ (302 Found)
WARN-NEW: Permissions Policy Header Not Set [10063] x 5 
	http://192.168.0.5:8050/WebGoat (200 OK)
	http://192.168.0.5:8050/WebGoat/login (200 OK)
	http://192.168.0.5:8050/WebGoat/login?error (200 OK)
	http://192.168.0.5:8050/WebGoat/registration (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
WARN-NEW: Absence of Anti-CSRF Tokens [10202] x 5 
	http://192.168.0.5:8050/WebGoat (200 OK)
	http://192.168.0.5:8050/WebGoat/login (200 OK)
	http://192.168.0.5:8050/WebGoat/login?error (200 OK)
	http://192.168.0.5:8050/WebGoat/registration (200 OK)
	http://192.168.0.5:8050/WebGoat/register.mvc (200 OK)
FAIL-NEW: 0	FAIL-INPROG: 0	WARN-NEW: 7	WARN-INPROG: 0	INFO: 0	IGNORE: 0	PASS: 53

```

## メモ

- ログイン後のURLをスキャンするにはbaselineスキャンでは難しそう。GUI版を使ったらできる
- ログインがないAPIサーバーの検証に向いてるかも
- dockerのGUI版を試したが起動も動作も重たくてCI向きではなさそう