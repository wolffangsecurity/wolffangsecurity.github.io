<p>Lab: <a href="https://tryhackme.com/room/crackthehash">Crack the hash</a></p>
<p>Tools: hashID, hashcat, John the Ripper, rockyou.txt, OneRuleToRuleThemAll.rule</p>
<p>All of these hashes can be cracked with the online tools: <a href="https://crackstation.net/">crackstation</a> and <a href="https://hashes.com/en/decrypt/hash">hashes.com</a>.</p>
<p><img src="https://github.com/paRaade/CTF-Writeups/assets/126734769/f2bd69e8-19a5-4042-b796-b179e7ee4766" alt="Pasted image 20240124204447">
<img src="https://github.com/paRaade/CTF-Writeups/assets/126734769/ac272b71-67a6-4aa4-9dea-5bbe9c63d227" alt="Pasted image 20240124215425">
<img src="https://github.com/paRaade/CTF-Writeups/assets/126734769/7c1115c5-ba6d-46e9-a67e-9f9e294cd762" alt="Pasted image 20240124211017">
<img src="https://github.com/paRaade/CTF-Writeups/assets/126734769/9d73ff1d-7805-4049-8f4f-ee5679eac39d" alt="Pasted image 20240124215257"></p>
<p>Manual method with hashcat and john the ripper</p>
<ul>
<li>Hashcat on Host OS (Windows)</li>
<li>John the Ripper in Virtual Machine  (Linux)</li>
</ul>
<p><strong>Level 1</strong></p>
<p>md5
Hash: 48bb6e862e54f2a795ffc4e541caed4d</p>
<pre><code>hashid 48bb6e862e54f2a795ffc4e541caed4d                                         
Analyzing '48bb6e862e54f2a795ffc4e541caed4d'
<span class="hljs-comment">[+]</span> MD2 
<span class="hljs-comment">[+]</span> MD5 
<span class="hljs-comment">[+]</span> MD4 
<span class="hljs-comment">[+]</span> Double MD5 
<span class="hljs-comment">[+]</span> LM 
<span class="hljs-comment">[+]</span> RIPEMD-128 
<span class="hljs-comment">[+]</span> Haval-128 
<span class="hljs-comment">[+]</span> Tiger-128 
<span class="hljs-comment">[+]</span> Skein-256(128) 
<span class="hljs-comment">[+]</span> Skein-512(128) 
<span class="hljs-comment">[+]</span> Lotus Notes/Domino 5 
<span class="hljs-comment">[+]</span> Skype 
<span class="hljs-comment">[+]</span> Snefru-128 
<span class="hljs-comment">[+]</span> NTLM 
<span class="hljs-comment">[+]</span> Domain Cached Credentials 
<span class="hljs-comment">[+]</span> Domain Cached Credentials 2 
<span class="hljs-comment">[+]</span> DNSSEC(NSEC3) 
<span class="hljs-comment">[+]</span> RAdmin v2.x 

hashcat -m 0 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.<span class="hljs-keyword">rule</span>
48bb6e862e54f2a795ffc4e541caed4d:easy
</code></pre><hr>
<p>sha1
Hash: CBFDAC6008F9CAB4083784CBD1874F76618D2A97</p>
<pre><code>hashid <span class="hljs-symbol">CBFDAC6008F9CAB4083784CBD1874F76618D2A97</span> 
<span class="hljs-symbol">Analyzing</span> <span class="hljs-string">'CBFDAC6008F9CAB4083784CBD1874F76618D2A97'</span>
[+] <span class="hljs-symbol">SHA</span><span class="hljs-number">-1</span> 
[+] <span class="hljs-symbol">Double</span> <span class="hljs-symbol">SHA</span><span class="hljs-number">-1</span> 
[+] <span class="hljs-symbol">RIPEMD</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">Haval</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">Tiger</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">HAS</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">LinkedIn</span> 
[+] <span class="hljs-symbol">Skein</span><span class="hljs-number">-256</span>(<span class="hljs-number">160</span>) 
[+] <span class="hljs-symbol">Skein</span><span class="hljs-number">-512</span>(<span class="hljs-number">160</span>) 

john -format:<span class="hljs-symbol">RAW</span>-<span class="hljs-symbol">SHA1</span> -wordlist:rockyou.txt hashes.txt
password123      (?)     

john --show hashes.txt                                   
?:password123
</code></pre><hr>
<p>sha256
Hash: 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032</p>
<pre><code>
hashid 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
Analyzing '1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032'
<span class="hljs-comment">[+]</span> Snefru-256 
<span class="hljs-comment">[+]</span> SHA-256 
<span class="hljs-comment">[+]</span> RIPEMD-256 
<span class="hljs-comment">[+]</span> Haval-256 
<span class="hljs-comment">[+]</span> GOST R 34.11-94 
<span class="hljs-comment">[+]</span> GOST CryptoPro S-Box 
<span class="hljs-comment">[+]</span> SHA3-256 
<span class="hljs-comment">[+]</span> Skein-256 
<span class="hljs-comment">[+]</span> Skein-512(256) 

hashcat -m 1400 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.<span class="hljs-keyword">rule</span>
1c8bfe8f801d79745c4631d09fff36c82aa37fc4cce4fc946683d7b336b63032:letmein
</code></pre><hr>
<p>bcrypt
Hash: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom</p>
<pre><code>This one has a hint <span class="hljs-keyword">that</span> <span class="hljs-keyword">is</span> essential because <span class="hljs-keyword">of</span> how long <span class="hljs-keyword">it</span> takes <span class="hljs-keyword">to</span> crack bcrypt hashes

hashid '$<span class="hljs-number">2</span>y$<span class="hljs-number">12</span>$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom' 
Analyzing '$<span class="hljs-number">2</span>y$<span class="hljs-number">12</span>$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom'
[+] Blowfish(OpenBSD) 
[+] Woltlab Burning Board <span class="hljs-number">4.</span>x 
[+] bcrypt 

The total <span class="hljs-built_in">number</span> <span class="hljs-keyword">of</span> passwords <span class="hljs-keyword">in</span> rockyou
cat rockyou.txt | wc -l
<span class="hljs-number">14344392</span>

Copied <span class="hljs-built_in">contents</span> rockyou.txt <span class="hljs-keyword">to</span> a new <span class="hljs-built_in">file</span>
cp rockyou.txt rockyou_new.txt

Created another <span class="hljs-built_in">file</span> <span class="hljs-keyword">with</span> all passwords <span class="hljs-keyword">that</span> have four <span class="hljs-built_in">characters</span> <span class="hljs-keyword">and</span> less
awk '<span class="hljs-built_in">length</span>($<span class="hljs-number">0</span>) &lt;= <span class="hljs-number">4</span>' rockyou_new.txt &gt; rockyou_four.txt

Total <span class="hljs-built_in">number</span> <span class="hljs-keyword">of</span> passwords <span class="hljs-keyword">in</span> final rockyou <span class="hljs-built_in">file</span>
cat rockyou_four.txt | wc -l
<span class="hljs-number">21028</span>

john <span class="hljs-comment">--format=bcrypt --wordlist=rockyou_four.txt hashes.txt </span>
bleh             (?)     

john <span class="hljs-comment">--show hashes.txt                                              </span>
?:bleh
</code></pre><hr>
<p>md4
Hash: 279412f945939ba78ce0758d3fd83daa</p>
<pre><code>hashid 279412f945939ba78ce0758d3fd83daa                              
Analyzing '279412f945939ba78ce0758d3fd83daa'
<span class="hljs-comment">[+]</span> MD2 
<span class="hljs-comment">[+]</span> MD5 
<span class="hljs-comment">[+]</span> MD4 
<span class="hljs-comment">[+]</span> Double MD5 
<span class="hljs-comment">[+]</span> LM 
<span class="hljs-comment">[+]</span> RIPEMD-128 
<span class="hljs-comment">[+]</span> Haval-128 
<span class="hljs-comment">[+]</span> Tiger-128 
<span class="hljs-comment">[+]</span> Skein-256(128) 
<span class="hljs-comment">[+]</span> Skein-512(128) 
<span class="hljs-comment">[+]</span> Lotus Notes/Domino 5 
<span class="hljs-comment">[+]</span> Skype 
<span class="hljs-comment">[+]</span> Snefru-128 
<span class="hljs-comment">[+]</span> NTLM 
<span class="hljs-comment">[+]</span> Domain Cached Credentials 
<span class="hljs-comment">[+]</span> Domain Cached Credentials 2 
<span class="hljs-comment">[+]</span> DNSSEC(NSEC3) 
<span class="hljs-comment">[+]</span> RAdmin v2.x 

hashcat -m 900 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.<span class="hljs-keyword">rule</span>
279412f945939ba78ce0758d3fd83daa:Eternity22
</code></pre><hr>
<p><strong>Level 2</strong></p>
<p>sha256
Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85</p>
<pre><code>Sha256
hashid F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
Analyzing 'F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85'
<span class="hljs-comment">[+]</span> Snefru-256 
<span class="hljs-comment">[+]</span> SHA-256 
<span class="hljs-comment">[+]</span> RIPEMD-256 
<span class="hljs-comment">[+]</span> Haval-256 
<span class="hljs-comment">[+]</span> GOST R 34.11-94 
<span class="hljs-comment">[+]</span> GOST CryptoPro S-Box 
<span class="hljs-comment">[+]</span> SHA3-256 
<span class="hljs-comment">[+]</span> Skein-256 
<span class="hljs-comment">[+]</span> Skein-512(256) 

hashcat -m 1400 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.<span class="hljs-keyword">rule</span>
f09edcb1fcefc6dfb23dc3505a882655ff77375ed8aa2d1c13f640fccc2d0c85:paule
</code></pre><hr>
<p>NTLM
Hash: 1DFECA0C002AE40B8619ECF94819CC1B</p>
<pre><code>
hashid 1DFECA0C002AE40B8619ECF94819CC1B                                
Analyzing '1DFECA0C002AE40B8619ECF94819CC1B'
<span class="hljs-comment">[+]</span> MD2 
<span class="hljs-comment">[+]</span> MD5 
<span class="hljs-comment">[+]</span> MD4 
<span class="hljs-comment">[+]</span> Double MD5 
<span class="hljs-comment">[+]</span> LM 
<span class="hljs-comment">[+]</span> RIPEMD-128 
<span class="hljs-comment">[+]</span> Haval-128 
<span class="hljs-comment">[+]</span> Tiger-128 
<span class="hljs-comment">[+]</span> Skein-256(128) 
<span class="hljs-comment">[+]</span> Skein-512(128) 
<span class="hljs-comment">[+]</span> Lotus Notes/Domino 5 
<span class="hljs-comment">[+]</span> Skype 
<span class="hljs-comment">[+]</span> Snefru-128 
<span class="hljs-comment">[+]</span> NTLM 
<span class="hljs-comment">[+]</span> Domain Cached Credentials 
<span class="hljs-comment">[+]</span> Domain Cached Credentials 2 
<span class="hljs-comment">[+]</span> DNSSEC(NSEC3) 
<span class="hljs-comment">[+]</span> RAdmin v2.x 

hashcat -m 1000 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.<span class="hljs-keyword">rule</span>
1dfeca0c002ae40b8619ecf94819cc1b:n63umy8lkf4i
</code></pre><hr>
<p>sha512
Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.</p>
<p>Salt: aReallyHardSalt</p>
<pre><code><span class="hljs-attribute">hashid</span> <span class="hljs-string">'<span class="hljs-variable">$6</span><span class="hljs-variable">$aReallyHardSalt</span><span class="hljs-variable">$6</span>WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.'</span>
Analyzing <span class="hljs-string">'<span class="hljs-variable">$6</span><span class="hljs-variable">$aReallyHardSalt</span><span class="hljs-variable">$6</span>WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.'</span>
[+] SHA-<span class="hljs-number">512</span> Crypt 

hashcat -m <span class="hljs-number">1800</span> hahahashes.txt rockyou.txt -O
<span class="hljs-variable">$6</span><span class="hljs-variable">$aReallyHardSalt</span><span class="hljs-variable">$6</span>WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:waka99
</code></pre><hr>
<p>HMAC-SHA1
e5d8870e5bdd26602cab8dbe07a942c8669e56d6
Salt: tryhackme</p>
<pre><code>hashid e5d8870e5bdd26602cab8dbe07a942c8669e56d6                                                                   
<span class="hljs-symbol">Analyzing</span> <span class="hljs-string">'e5d8870e5bdd26602cab8dbe07a942c8669e56d6'</span>
[+] <span class="hljs-symbol">SHA</span><span class="hljs-number">-1</span> 
[+] <span class="hljs-symbol">Double</span> <span class="hljs-symbol">SHA</span><span class="hljs-number">-1</span> 
[+] <span class="hljs-symbol">RIPEMD</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">Haval</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">Tiger</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">HAS</span><span class="hljs-number">-160</span> 
[+] <span class="hljs-symbol">LinkedIn</span> 
[+] <span class="hljs-symbol">Skein</span><span class="hljs-number">-256</span>(<span class="hljs-number">160</span>) 
[+] <span class="hljs-symbol">Skein</span><span class="hljs-number">-512</span>(<span class="hljs-number">160</span>) 

hashcat -m <span class="hljs-number">160</span> hahahashes.txt rockyou.txt
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme:<span class="hljs-number">481616481616</span>
</code></pre>
