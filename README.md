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
<h2>Install and Configure PostgreSQL</h2>
<p>Use the <code>apt-get</code> package installer to download PostgreSQL</p>
<p><code>sudo apt-get install postgresql postgresql-contrib</code></p>
<p>Now, we need to login into the database server</p>
<p><code>sudo -u postgres psql</code></p>
<p>Let's create a database user named <code>catalog</code></p>
<p><code>CREATE USER catalog WITH PASSWORD 'password';</code></p>
<p>Let's give the user permission to create databases</p>
<p><code>ALTER USER catalog CREATEDB;</code></p>
<p>Create a database named <code>catalog</code></p>
<p><code>CREATE DATABASE catalog WITH OWNER catalog;</code></p>
<p>Connect to the catalog database</p>
<p><code>\connect catalog</code></p>
<p>Revoke all rights</p>
<p><code>REVOKE ALL ON SCHEMA public FROM public;</code></p>
<p>Grant rights to <code>catalog</code> user</p>
<p><code>GRANT ALL ON SCHEMA public TO catalog;</code></p>
<p>Exit the database</p>
<p><code>\q</code></p>
<h2>Install and Configure Apache to Run Python mod_wsgi App</h2>
<p>Install the Apache2 web server</p>
<p><code>sudo apt-get install apache2</code></p>
<p>Install the application handler - mod_wsgi</p>
<p><code>sudo apt-get install libapache2-mod-wsgi-py3</code></p>
<p>We will create a directory in our home directory to work in and link to it from the site-root defined by Apache's configuration</p>
<p><code>mkdir ~/flaskapp</code></p>
<p><code>sudo ln -sT ~/flaskapp /var/www/html/flaskapp</code></p>
<p>Now, we bring in all the code from my github repo into this directory</p>
<p><code>https://github.com/dmaydan/Item_Catalog_Project</code></p>
<p>Now, rename the file <code>app.py</code> as <code>flaskapp.py</code></p>
<p><code>mv app.py flaskapp.py</code></p>
<h3>Fill PostgreSQL Database</h3>
<p>Open the python3 shell</p>
<p><code>python3</code></p>
<p>Import the database model from <code>flaskapp.py</code></p>
<p><code>from flaskapp import db</code></p>
<p>Create the database model in the PostgreSQL database</p>
<p><code>db.create_all()</code></p>
<p>Open the <code>filldatabase.py</code> file in the <code>flaskapp</code> directory</p>
<p><code>sudo nano filldatabase.py</code></p>
<p>Replace <code>from app import Category, Item, User</code> with <code>from flaskapp import Category, Item, User</code></p>
<p>Replace <code>engine = create_engine('[db_name]')</code> with <code>engine = create_engine('postgresql://catalog:password@localhost/catalog')</code></p>
<p>Then, we run this file to fill in the database</p>
<p><code>python3 filldatabase.py</code>
<h3>Configure Apache and mod_wsgi</h3>
<p>Create a <code>.wsgi</code> file to load the app</p>
<p><code>sudo nano flaskapp.wsgi</code></p>
<p>Paste in the following code</p>
<pre>
  activate_this = '/home/ubuntu/environment/bin/activate_this.py'
  with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))
  import sys
  sys.path.insert(0, '/var/www/html/flaskapp')
  from flaskapp import app as application
</pre>
<p>Now, we need to adjust the Apache2 server configuration</p>
<p><code>sudo nano /etc/apache2/sites-enabled/000-default.conf</code></p>
<p>Paste in the following code just after the <code>DocumentRoot /var/www/html</code> line</p>
<pre>
WSGIDaemonProcess flaskapp threads=5
WSGIScriptAlias / /var/www/html/flaskapp/flaskapp.wsgi
&lt;Directory flaskapp&gt;
    WSGIProcessGroup flaskapp
    WSGIApplicationGroup %{GLOBAL}
    Order deny,allow
    Allow from all
&lt;/Directory&gt;
</pre>
