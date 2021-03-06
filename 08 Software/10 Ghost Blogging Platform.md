<h1>Ghost Blogging Platform</h1>

        
<h2>Ghost Blogging Platform</h2><br>
<strong>Important note:</strong> You will need to have installed <code>node.js</code> to use Ghost. See this FAQ - <a href="https://www.feralhosting.com/faq/view?question=199">node.js - How to install</a><br>
The node install instructions are for 4.1.1 and Ghost will probably need at the wget:<br>
<pre><code><a href="https://nodejs.org/dist/v0.12.7/node-v0.12.7-linux-x64.tar.gz">https:&#x2F;&#x2F;nodejs.org&#x2F;dist&#x2F;v0.12.7&#x2F;node-v0.12.7-linux-x64.tar.gz</a></code></pre> <br>
 which should do the trick to give you 0.12.7 which suits the current version.<br>
<br>
<h2>Installation:</h2><br>
<pre><code>wget -qO ~&#x2F;ghost.zip <a href="http://ghost.org/zip/ghost-latest.zip">http:&#x2F;&#x2F;ghost.org&#x2F;zip&#x2F;ghost-latest.zip</a>
unzip -qo ~&#x2F;ghost.zip -d ~&#x2F;ghost
npm install forever -g
cd ~&#x2F;ghost
npm install --production</code></pre><br>
<h2>Configuration:</h2><br>
Now we need to edit this file:<br>
<br>
<pre><code>~&#x2F;ghost&#x2F;config.example.js</code></pre><br>
Using SSH:<br>
<br>
<pre><code>nano -w ~&#x2F;ghost&#x2F;config.example.js</code></pre><br>
The file is broken up into sections. The default section is the <code>Development</code> section. This is what we edit first.<br>
<br>
Find this:<br>
<br>
<pre><code>&#x2F;&#x2F; The url to use when providing links to the site, E.g. in RSS and email.
url: &#x27;<a href="http://my-ghost-blog.com">http:&#x2F;&#x2F;my-ghost-blog.com</a>&#x27;,</code></pre><br>
Edit it to match your server URL and add the <code>&#x2F;blog&#x2F;</code> subdirectory<br>
<br>
<pre><code>&#x2F;&#x2F; The url to use when providing links to the site, E.g. in RSS and email.
url: &#x27;<a href="http://server.feralhosting.com/username/blog/">http:&#x2F;&#x2F;server.feralhosting.com&#x2F;username&#x2F;blog&#x2F;</a>&#x27;,</code></pre><br>
Find this:<br>
<br>
<pre><code>server: {
&nbsp; &nbsp; &#x2F;&#x2F; Host to be passed to node&#x27;s net.Server#listen()
&nbsp; &nbsp; host: &#x27;127.0.0.1&#x27;,
&nbsp; &nbsp; &#x2F;&#x2F; Port to be passed to node&#x27;s net.Server#listen(), for iisnode set this to process.env.PORT
&nbsp; &nbsp; port: &#x27;2368&#x27;
}</code></pre><br>
Change the host to <code>0.0.0.0</code> and choose a port between the ranges <code>6000</code> - <code>50000</code><br>
<br>
<pre><code>server: {
&nbsp; &nbsp; &#x2F;&#x2F; Host to be passed to node&#x27;s net.Server#listen()
&nbsp; &nbsp; host: &#x27;0.0.0.0&#x27;,
&nbsp; &nbsp; &#x2F;&#x2F; Port to be passed to node&#x27;s net.Server#listen(), for iisnode set this to process.env.PORT
&nbsp; &nbsp; port: &#x27;23684&#x27;
}</code></pre><br>
<h2>Proxypass URLS with Apache or Nginx.</h2><br>
<h3>Apache:</h3><br>
Create a new blank file using this command:<br>
<br>
<pre><code>nano ~&#x2F;.apache2&#x2F;conf.d&#x2F;ghost.conf</code></pre><br>
<strong>Important note:</strong> Do not forget to change the <code>PORT</code> to the same port as used by Ghost.<br>
<br>
Now we will copy and paste the below code into this file, making sure to customise the <code>PORT</code> to match that used by Ghost:<br>
<br>
<pre><code>Include &#x2F;etc&#x2F;apache2&#x2F;mods-available&#x2F;proxy.load
Include &#x2F;etc&#x2F;apache2&#x2F;mods-available&#x2F;proxy_http.load

ProxyRequests Off
ProxyPreserveHost On

ProxyPass &#x2F;blog <a href="http://10.0.0.1:PORT/">http:&#x2F;&#x2F;10.0.0.1:PORT&#x2F;</a>${USER}&#x2F;blog
ProxyPassReverse &#x2F;blog <a href="http://10.0.0.1:PORT/">http:&#x2F;&#x2F;10.0.0.1:PORT&#x2F;</a>${USER}&#x2F;blog</code></pre><br>
Once you have copied and pasted this then press and hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm. Now you can reload the conf like this:<br>
<br>
<pre><code>&#x2F;usr&#x2F;sbin&#x2F;apache2ctl -k graceful</code></pre><br>
<h3>Nginx:</h3><br>
<pre><code>nano ~&#x2F;.nginx&#x2F;conf.d&#x2F;000-default-server.d&#x2F;ghost.conf</code></pre><br>
We are going to copy and paste this once it is edited:<br>
<br>
<pre><code>location ^~ &#x2F;blog {
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header Host $http_x_host;
	proxy_set_header X-NginX-Proxy true;

	rewrite &#x2F;(.*) &#x2F;username&#x2F;$1 break;
	proxy_pass <a href="http://10.0.0.1:PORT/&#x3B;">http:&#x2F;&#x2F;10.0.0.1:PORT&#x2F;;</a>
	proxy_redirect off;
}</code></pre><br>
But before we do this we are required to edit these two lines:<br>
<br>
<pre><code>rewrite &#x2F;(.*) &#x2F;username&#x2F;$1 break;
proxy_pass <a href="http://10.0.0.1:PORT/&#x3B;">http:&#x2F;&#x2F;10.0.0.1:PORT&#x2F;;</a></code></pre><br>
Change:<br>
<br>
1: <code>username</code> in the <code>rewrite &#x2F;(.*) &#x2F;username&#x2F;$1 break;</code> to your Feral username <br>
2: <code>PORT</code> in the <code>proxy_pass <a href="http://10.0.0.1:PORT/&#x3B;">http:&#x2F;&#x2F;10.0.0.1:PORT&#x2F;;</a></code> to the port you configured in the <code>~&#x2F;ghost&#x2F;config.example.js</code><br>
<br>
Then copy and paste this into the file.<br>
<br>
Press and hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm.<br>
<br>
Now reload the nginx conf files for the change to take effect using this command:<br>
<br>
<pre><code>&#x2F;usr&#x2F;sbin&#x2F;nginx -s reload -c ~&#x2F;.nginx&#x2F;nginx.conf</code></pre><br>
<h2>Running Ghost:</h2><br>
Now we are ready to start Ghost:<br>
<br>
<pre><code>screen -S ghost
cd ~&#x2F;ghost
npm start</code></pre><br>
Now once it has loaded&nbsp; you will see something like this:<br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/Software/Ghost%20Blogging%20Platform/1.png"><br>
<br>
Then press and hold <code>CTRL</code> and <code>a</code> then press <code>d</code> to detach from the screen. This leaves it running in the background.<br>
<br>
<h2>Ghost and forever</h2><br>
Optionally you can exit the screen session and start Ghost using <code>forever</code> since we installed it at the start:<br>
<br>
<pre><code>cd ~&#x2F;ghost &amp;&amp; </code></pre><br>
The run this command, Ghost will be forked to the background and monitored by forever.<br>
<br>
<pre><code>forever start index.js</code></pre><br>
Using Variables:<br>
<br>
You can use the <code>NODE_ENV</code> to start Ghost with production instead of development. This requires you to have edited the relevant section of the Ghost config.<br>
<br>
<pre><code>NODE_ENV=production forever start index.js</code></pre><br>
<h2>Your Ghost URLs</h2><br>
You should now be able to visit the URL where <code>username</code> if your Feral username and <code>server</code> if the name of the server that the relevant slot is hosted on.<br>
<br>
<strong>Important note:</strong> https works fine so you can use the https URL.<br>
<br>
<pre><code><a href="https://server.feralhosting.com/username/blog/">https:&#x2F;&#x2F;server.feralhosting.com&#x2F;username&#x2F;blog&#x2F;</a></code></pre><br>
Visit this URL to configure your admin account.<br>
<br>
<pre><code><a href="https://server.feralhosting.com/username/blog/ghost">https:&#x2F;&#x2F;server.feralhosting.com&#x2F;username&#x2F;blog&#x2F;ghost</a></code></pre><br>
<h2>Updating:</h2><br>
<a href="http://support.ghost.org/how-to-upgrade">http:&#x2F;&#x2F;support.ghost.org&#x2F;how-to-upgrade</a><br>
<br>
