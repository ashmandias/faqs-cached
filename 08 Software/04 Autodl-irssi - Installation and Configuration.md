<h1>Autodl-irssi - Installation and Configuration</h1>

        
<strong>Please note!</strong> Though this software runs in nearly all cases without problems, Feral cannot provide much support for its use or help should anything go wrong. Please make sure you&#x27;ve read this FAQ fully as help on the problem may be covered already.<br>
<br>
This notice absolutely does not prevent you from raising a ticket should anything go wrong but please bear in mind staff may not be able to assist beyond basic troubleshooting (for example restarting, clarifying error messages and so on).<br>
<br>
<h2>Preparation</h2>You&#x27;ll need to execute some commands via SSH (secure shell). If you&#x27;ve never used SSH commands the idea of using a terminal window may seem daunting. There is a guide on simply getting connected to your slot through SSH <a href="https://www.feralhosting.com/faq/view?question=12">here</a>. Commands are kept as simple as possible and in most cases will simply need to be copied and pasted into the terminal window (then executed by pressing the <code>Enter</code> key).<br>
<br>
<br>
<h2>Installation - using the automated bash script</h2>Features of the bash script:<br>
 <br>
&nbsp; 1.&nbsp; Installs the latest version of autodl-irssi<br>
&nbsp; 2.&nbsp; Runs the fix script to use correct IPs for Feral slots<br>
&nbsp; 3.&nbsp; Sets a port and password in autodl.cfg and the ruTorrent plugin&#x27;s conf.php<br>
&nbsp; 4.&nbsp; It does not amend autodl.cfg if it exists<br>
<br>
To use the script just use this command:<br>
<br>
<pre><code>wget -qO ~&#x2F;install.autodl.sh <a href="http://git.io/oTUCMg">http:&#x2F;&#x2F;git.io&#x2F;oTUCMg</a> &amp;&amp; bash ~&#x2F;install.autodl.sh</code></pre><br>
<br>
<h2>Installation - manual</h2>Manual installation allows a greater degree of control but the automated script should be sufficient for the majority of use cases.<br>
<br>
<br>
<h3>Preparation for manual install</h3><br>
First you need to make sure any previous versions are dead and removed, after having backed up the autodl.cfg. If this is your first time installing autodl you will not need to run these.<br>
<br>
<pre><code>killall irssi -u $(whoami); screen -wipe
cp -f ~&#x2F;.autodl&#x2F;autodl.cfg ~&#x2F;.autodl&#x2F;autodl.cfg.bak
rm -rf ~&#x2F;.irssi&#x2F;scripts&#x2F;AutodlIrssi ~&#x2F;.irssi&#x2F;scripts&#x2F;autorun&#x2F;autodl-irssi.pl
rm -rf ~&#x2F;www&#x2F;$(whoami).$(hostname -f)&#x2F;public_html&#x2F;rutorrent&#x2F;plugins&#x2F;autodl-irssi
</code></pre><br>
<br>
<h3>Installing autodl-irssi</h3><pre><code>mkdir -p ~&#x2F;.irssi&#x2F;scripts&#x2F;autorun ~&#x2F;.autodl
&nbsp; &nbsp; echo -e &quot;[options]\ngui-server-port = 0\ngui-server-password = pass&quot; &gt; ~&#x2F;.autodl&#x2F;autodl.cfg
&nbsp; &nbsp; wget -qO ~&#x2F;autodl-irssi.zip <a href="https://github.com/autodl-community/autodl-irssi/releases/download/community-v1.60/autodl-irssi-community-v1.60.zip">https:&#x2F;&#x2F;github.com&#x2F;autodl-community&#x2F;autodl-irssi&#x2F;releases&#x2F;download&#x2F;community-v1.60&#x2F;autodl-irssi-community-v1.60.zip</a>
&nbsp; &nbsp; unzip -qo ~&#x2F;autodl-irssi.zip -d ~&#x2F;.irssi&#x2F;scripts&#x2F;
&nbsp; &nbsp; cp -f ~&#x2F;.irssi&#x2F;scripts&#x2F;autodl-irssi.pl ~&#x2F;.irssi&#x2F;scripts&#x2F;autorun&#x2F;
&nbsp; &nbsp; rm -f autodl-irssi.zip .irssi&#x2F;scripts&#x2F;{README*,autodl-irssi.pl,CONTRIBUTING.md}
</code></pre><br>
<br>
<h3>Installing the autodl ruTorrent plugin</h3><pre><code>wget -qO ~&#x2F;autodl-rutorrent.zip <a href="https://github.com/autodl-community/autodl-rutorrent/archive/master.zip">https:&#x2F;&#x2F;github.com&#x2F;autodl-community&#x2F;autodl-rutorrent&#x2F;archive&#x2F;master.zip</a>
unzip -qo ~&#x2F;autodl-rutorrent.zip
mv ~&#x2F;autodl-rutorrent-master&#x2F;_conf.php ~&#x2F;autodl-rutorrent-master&#x2F;conf.php
mv ~&#x2F;autodl-rutorrent-master ~&#x2F;www&#x2F;$(whoami).$(hostname -f)&#x2F;public_html&#x2F;rutorrent&#x2F;plugins&#x2F;autodl-irssi 
rm ~&#x2F;autodl-rutorrent.zip
</code></pre><br>
<br>
<h2>Configuring - manual install</h2>This guide does not cover configuring autodl.cfg (either manually or via the ruTorrent plugin). It only discusses fixing a couple of issues and configuring the plugin to be able to communicate with auto-irssi. This step is not needed if you run the bash script.<br>
<br>
<br>
<h3>Fixing IPs</h3>Autodl-irssi and its plugin assume a local IP of 127.0.0.1 rather than the 10.0.0.1 that Feral uses. These commands make the necessary changes - please execute them whilst autodl-irssi is not running (see the next section for checking if it&#x27;s running and how to kill and start it up):<br>
<br>
<pre><code>sed -i &quot;s|use constant LISTEN_ADDRESS =&gt; &#x27;127.0.0.1&#x27;;|use constant LISTEN_ADDRESS =&gt; &#x27;10.0.0.1&#x27;;|g&quot; ~&#x2F;.irssi&#x2F;scripts&#x2F;AutodlIrssi&#x2F;GuiServer.pm
sed -i &#x27;s|$rtAddress = &quot;127.0.0.1$rtAddress&quot;|$rtAddress = &quot;10.0.0.1$rtAddress&quot;|g&#x27; ~&#x2F;.irssi&#x2F;scripts&#x2F;AutodlIrssi&#x2F;MatchedRelease.pm
sed -i &#x27;s|my $scgi = new AutodlIrssi::Scgi($rtAddress, {REMOTE_ADDR =&gt; &quot;127.0.0.1&quot;});|my $scgi = new AutodlIrssi::Scgi($rtAddress, {REMOTE_ADDR =&gt; &quot;10.0.0.1&quot;});|g&#x27; ~&#x2F;.irssi&#x2F;scripts&#x2F;AutodlIrssi&#x2F;MatchedRelease.pm
sed -i &#x27;s|if (!socket_connect($socket, &quot;127.0.0.1&quot;, $autodlPort))|if (!socket_connect($socket, &quot;10.0.0.1&quot;, $autodlPort))|g&#x27; ~&#x2F;www&#x2F;&quot;$(whoami)&quot;.&quot;$(hostname -f)&quot;&#x2F;public_html&#x2F;rutorrent&#x2F;plugins&#x2F;autodl-irssi&#x2F;getConf.php
</code></pre><br>
<br>
<h3>Linking autodl-irssi with the ruTorrent plugin</h3>To be able to manage autodl-irssi through ruTorrent we need to get them communicating with each other by editing their config files. Nano is a good choice to do this since it will prevent possible line ending issues when using other tools.<br>
<br>
Edit the autodl-irssi config file with no by executing this command:<br>
<br>
<pre><code>nano ~&#x2F;.autodl&#x2F;autodl.cfg</code></pre><br>
The file will look like this:<br>
<br>
<pre><code>[options]
gui-server-port = 0
gui-server-password = <em>pass</em>
</code></pre><br>
Change the port to something between <code>10001</code> and <code>49999</code> and change <em>pass</em> to a password of your choice.<br>
<br>
Once you&#x27;re done hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm.<br>
<br>
Using nano again, amend the ruTorrent plugin&#x27;s config file:<br>
<pre><code>nano ~&#x2F;www&#x2F;$(whoami).$(hostname -f)&#x2F;public_html&#x2F;rutorrent&#x2F;plugins&#x2F;autodl-irssi&#x2F;conf.php</code></pre><br>
Change the variables $autodlPort and $autodlPassword so they match what you selected in autodl.cfg.<br>
<br>
<br>
<h2>Starting and restarting</h2>In order to restart the software the existing process should be killed. If the check below does not display a process number please proceed to step 3. These steps are tailored towards processes started by this FAQ.<br>
<br>
1.&nbsp; &nbsp; Check:<br>
<pre><code>pgrep -fu &quot;$(whoami)&quot; &quot;autodl&quot;</code></pre><br>
2.&nbsp; &nbsp; Kill: <br>
<pre><code>kill &quot;$(pgrep -fu &quot;$(whoami)&quot; &quot;autodl&quot;)&quot;</code></pre><br>
3.&nbsp; &nbsp; Restart: <br>
<pre><code>screen -dmS autodl irssi</code></pre><br>
<strong>Important note:</strong> You can repeat step 1 after attempting to kill to check if it&#x27;s been shut down. Give the program at least 10 seconds to shut down at step 2 before trying to restart. If it refuses to exit then you have to force it to quit using this command instead:<br>
<br>
<pre><code>kill -9 &quot;$(pgrep -fu &quot;$(whoami)&quot; &quot;autodl&quot;)&quot;</code></pre><br>
<br>
<h2>Upgrading</h2>You can choose to update autodl-irssi within ruTorrent by clicking the autodl-irssi tab and then the Update button, or within irssi by executing:<br>
<pre><code>&#x2F;autodl update</code></pre><br>
Updating the core files will revert parts of autodl-irssi to use 127.0.0.1 as a local IP instead of 10.0.0.1. So if you see an error after updating please re-run the bash script<br>
<br>
<br>
<h2>Cron - start up automatically if the server is rebooted</h2>Unfortunately sometimes the server needs to be rebooted. When this happens the system will start up a lot of the processes automatically on your slot. It will not however start up custom software. We need to manually set something up to make this happen.<br>
<br>
To bring up the crontab editor use the following command:<br>
<br>
<pre><code>crontab -e</code></pre><br>
If this is your very first time you&#x27;ll be given a choice of editors. Nano is fine to use so unless you specifically want something else just press enter.<br>
<br>
There will already be some basic information on crontabs in the file - you can keep it if you like, or get rid of it if you prefer. The lines are commented out so are for information purposes only and don&#x27;t affect any cron jobs added.<br>
<br>
At a new line in the file copy-paste the following:<br>
<br>
<pre><code>@reboot screen -dmS autodl irssi</code></pre><br>
<br>
<h2>Troubleshooting</h2><em>I get &quot;Error: Could not connect: (111) Connection refused&quot;</em><br>
<br>
1.&nbsp;  Is autodl-irssi running? Check using the check command in the Starting and restarting section. Start it up if not.<br>
2.&nbsp;  If running, please try re-running the fix commands from the Configuration section. Refresh ruTorrent to see if it has helped.<br>
3.&nbsp;  If it hasn&#x27;t helped, double check the port and passwords in autodl.cfg and conf.php for the plugin match.<br>
<br>
