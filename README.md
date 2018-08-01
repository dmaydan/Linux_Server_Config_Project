<h1>Linux Server Configuration Project</h1>
This is my submission for Udacity's Full Stack Web Developer Nanodegree's Linux Server Configuration Project. The task was to take a baseline installation of a Linux server and prepare it to host a web application. I secured the server from a number of attack vectors, installed and configured a database server, installed and configured a web server, and deployed an existing web applications onto it.
<h2>Server Details</h2>
<p>IP Address: <code>35.183.106.36</code></p>
<p>SSH Port: <code>2200</code></p>
<p>URL: http://www.35.183.106.36.nip.io/</p>
<i>NIP.IO is a wildcard DNS that allows you to map <code>[anything].[IP Address].nip.io</code> to the corresponding <code>[IP Address]</code></i>
<p>Linux Distribution: <code>Ubuntu</code></p>
Server Host: <code>Amazon Lightsail (Amazon Web Services)</code>
<h2>Secure the Server</h2>
<h3>Update all currently installed packages</h3>
<p>Update the package source list</p>
<p><code>sudo apt-get update</code></p>
<p>Update the software</p>
<p><code>sudo apt-get upgrade</code></p>
<h3>Change SSH Port</h3>
<p>Open configuration file of the SSH daemon<p>
<p><code>sudo nano /etc/ssh/sshd_config</code></p>
<p>Locate the line:</p>
<p><code># Port 22</code></p>
<p>Replace that line with:</p>
<p><code>Port 2200</code></p>
<p>In order for the changes to take affect, you need to restart the SSH daemon</p>
<p><code>sudo service sshd restart</code></p>
