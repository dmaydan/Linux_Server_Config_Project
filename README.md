<h1>Linux Server Configuration Project</h1>
This is my submission for Udacity's Full Stack Web Developer Nanodegree's Linux Server Configuration Project. The task was to take a baseline installation of a Linux server and prepare it to host a web application. I secured the server from a number of attack vectors, installed and configured a database server, installed and configured a web server, and deployed an existing web applications onto it.
<h2>Server Details</h2>
<p>IP Address: <code>35.183.106.36</code></p>
<p>SSH Port: <code>2200</code></p>
<p>URL: http://www.35.183.106.36.nip.io/</p>
<p><i>NIP.IO is a wildcard DNS that allows you to map <code>[anything].[IP Address].nip.io</code> to the corresponding <code>[IP Address]</code></i></p>
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
<p>Locate the line</p>
<p><code># Port 22</code></p>
<p>Replace that line</p>
<p><code>Port 2200</code></p>
<p>In order for the changes to take affect, you need to restart the SSH daemon</p>
<p><code>sudo service sshd restart</code></p>
<p>You will temporarily be locked out of your server until you take the following step</p>
<h3>Configure Lightsail (AWS) Firewall</h3>
<p>Go the the dashboard for your Lightsail linux instance</p>
<p>Click on the <code>Networking</code> tab</p>
<p>Click the <code>Add another</code> button under the firewall table and enable 2 more ports:</p>
<p>Open the new SSH port</p>
<p><code>Custom | TCP | 2200</code></p>
<p>Open the default port for NTP</p>
<p><code>Custom | TCP | 123</code></p>
<p>Click <code>Edit rules</code> and delete the rule for the default SSH port 22</p>
<p>Now, you can connect to the Linux server via SSH from your own terminal at the new port (2200)</p>
<p>Download the private key from the Lightsail (AWS) dashboard</p>
<p>Place the file into the <code>/users/[user]/.ssh</code> directory</p>
<p>Make sure to tighten the permissions on this file</p>
<p><code>chmod 400 [private key]</code></p>
<p>Login to the server</p>
<p><code>ssh ubuntu@[public ip] -p 2200 -i ~/.ssh/[private key]</code></p>
<h3>Configure Uncomplicated Firewall</h3>
<p>By default, block all incoming requests to all ports</p>
<p><code>sudo ufw default deny incoming</code></p>
<p>By default, allow all outgoing requests from all ports</p>
<p><code>sudo ufw default allow outgoing</code></p>
<p>Open port 2200 for SSH</p>
<p><code>sudo ufw allow 2200/tcp</code></p>
<p>Open port 80 for HTTP</p>
<p><code>sudo ufw allow www</code></p>
<p>Open port 123 for NTP</p>
<p><code>sudo ufw allow ntp</code></p>
<p>Enable the firewall</p>
<p><code>sudo ufw enable</code></p>
<h3>Disable Root Login</h3>
<p>Open configuration file of the SSH daemon<p>
<p><code>sudo nano /etc/ssh/sshd_config</code></p>
<p>Locate the line</p>
<p><code>PermitRootLogin without-password</code></p>
<p>Replace that line</p>
<p><code>PermitRootLogin no</code></p>
<p>In order for the changes to take affect, you need to restart the SSH daemon</p>
<p><code>sudo service sshd restart</code></p>
<h2>Creating New User</h2>
<h3>Create and Give Sudo Access</h3>
<p>Create the user</p>
<p><code>sudo adduser grader</code></p>
<p>Give sudo access</p>
<p><code>sudo usermod -aG sudo grader</code></p>
<h3>Set Up SSH for New User</h3>
<p>Return to local machine and generate the key-pair</p>
<p><code>ssh-keygen</code></p>
<p>This command will prompt you for a path for the keypair</p>
<p><code>/users/[local_user]/.ssh/[any_file_name]</code></p>
<p>Read out the contents of the public key</p>
<p><code>cat .ssh/[public key]</code></p>
<p>Copy the result of this command to your clipboard</p>
<p>Log back into the server as the <code>grader</code> user</p>
<p>Create a directory called <code>.ssh</code> within that user's home directory</p>
<p><code>mkdir .ssh</code></p>
<p>Create a new file within that directory that will contain all the public keys that this user is allowed to user for authentication</p>
<p><code>touch .ssh/authorized_keys</code></p>
<p>Open this file and paste in the public key</p>
<p><code>sudo nano .ssh/authorized_keys</code></p>
<p>Now, we need to tighten the permissions on the <code>authorized_keys</code> file and the <code>.ssh</code> directory</p>
<p><code>chmod 700 .ssh</code></p>
<p><code>chmod 644 .ssh/authorized_keys</code></p>
<h3>Forcing Key-Based Authentication</h3>
<p>Now that we have set up the key-pair for the <code>grader</code> user, we need to force key-based authentication for security purposes</p>
<p>Open configuration file of the SSH daemon<p>
<p><code>sudo nano /etc/ssh/sshd_config</code></p>
<p>Locate the line</p>
<p><code># PasswordAuthentication no</code></p>
<p>Uncomment the file so it reads</p>
<p><code>PasswordAuthentication no</code></p>
<p>In order for the changes to take affect, you need to restart the SSH daemon</p>
<p><code>sudo service sshd restart</code></p>
