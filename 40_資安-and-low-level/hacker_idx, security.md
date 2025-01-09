---
aliases:
  - security, security_idx
---
---  

- [[XSS]]
- [[CSRF]]
- [notion note on Burpsuite Academy](https://www.notion.so/nture4388/Burpsuite-Academy-c920a33e87f3467480c40044965bcce4?pvs=4)




---



[(592) one of the craziest exploits i've ever seen - YouTube](https://www.youtube.com/watch?v=89ysXVYH2Sk)
- a one pixel img contain below hacking scheme
- webp lib, the bug is abt no length check when create a huffman table (when it unpack it over and over again)
- so hacker is able to create a table in a way to have a bufferoverflow in the bss in the lib webp -> can lead to double free, a heap exploit and can conduct rce


[(592) secret backdoor found in open source software (xz situation breakdown) - YouTube](https://www.youtube.com/watch?v=jqjtNDtbDNI) & [(592) revealing the features of the XZ backdoor - YouTube](https://www.youtube.com/watch?v=vV_WdTBbww4)
- ref: [oss-security - backdoor in upstream xz/liblzma leading to ssh server compromise](https://openwall.com/lists/oss-security/2024/03/29/4)
- open source project but have backdoor in it
- how?
- it's a xz (compressed file) in test folder
	- so you can not easily scan code and find it, you need to go futhur step and check each binary file, zipped file in test folder!
	- that file, unzip it, you will find the code unzip another file 
	- in this file, is a script , this script is a hook to the build file(when build, this script will run sth)
	- and in it, there's .o file (.o is the middle step of C compliation process,    .c -> .o -> exectuable)
	- this .o will be binding to the program via dynamic linker in build exetuable process
	- and this .o is any code hacker can run when you run xz
	- 



[(592) malicious javascript injected into 100,000 websites - YouTube](https://www.youtube.com/watch?v=bbatLr98fEY&t=70s)
- polyfil, or polykill attach
- a chinese company buy a domain polyfill.io which serve a polyfill via cdn
- and over 100,000 website use this cdn for polyfill util
- when you visit this website, the this polyfill js run
- inside this minial js, there is a code will run a another js file, called googie.analysic script
- in this script, it's all js and obsiudcate
- this code shall be a payload, which at its extreme, can possible exploit v8 bug and escape from v8 sandbox and RCE on ur computer
- related link
	- [GitHub · Where software is built](https://github.com/polyfillpolyfill/polyfill-service/issues/2873)
	- [Exploiting V8 at openECSC Ʊ lyra's epic blog](https://lyra.horse/blog/2024/05/exploiting-v8-at-openecsc/)



[(592) new SSH exploit is absolutely wild - YouTube](https://www.youtube.com/watch?v=Rj3sTAMYNQk)
- [regreSSHion: Remote Unauthenticated Code Execution Vulnerability in OpenSSH server | Qualys Security Blog](https://blog.qualys.com/vulnerabilities-threat-research/2024/07/01/regresshion-remote-unauthenticated-code-execution-vulnerability-in-openssh-server)
- open SSH,  a recent ver just remove a header and this bug 又跑出來
- race condition
	- 特定時間去打 sig alarm
	- 會讓heap的某一個區間可以被 access，因為還沒有被 maclloc 掉
	- 透過 send different key 多次去建立讓這個heap區間可以注入code來達到 RCE
	- 32bit and aslr系統約 6-8 hr 的try
	- 64 bit可能要很多天 try
