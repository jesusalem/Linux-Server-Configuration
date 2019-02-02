<h1><a id="Linux_Server_configuration_0"></a>Linux Server configuration</h1>
<h3><a id="Introduction_2"></a>Introduction</h3>
<p>This project is about configuring an Ubuntu Linux server to host a web application. The Catalog application will be hosted.</p>
<p>This project is part of the Udacity Full Stack Web development Nanodegree, on which we learn the following</p>
<ul>
<li>Gain an understanding of the Linux operating system</li>
<li>Linux security</li>
<li>How to implement web application servers.</li>
</ul>
<p>The site is located on <a href="http://54.83.157.49/">http://54.83.157.49/</a></p>
<h1><a id="Steps_14"></a>Steps</h1>
<h3><a id="Get_your_server_15"></a>Get your server.</h3>
<p>#####1. Start a new Ubuntu Linux server instance on Amazon Lightsail.</p>
<ul>
<li>For this just login to <a href="https://lightsail.aws.amazon.com/">https://lightsail.aws.amazon.com/</a>, follow the instructions and create an Ubuntu instance.</li>
</ul>
<h5><a id="2_ssh_to_your_server_19"></a>2. ssh to your server.</h5>
<ul>
<li>Dowloand the .pem public key and connect bu running the following:<br>
<code>ssh -i ~/LightsailDefaultKey-us-east-1.pem ubuntu@54.83.157.49 -p 22</code></li>
</ul>
<h3><a id="Secure_your_server_23"></a>Secure your server</h3>
<h5><a id="3_Update_all_currently_installed_packages_Run_the_following_24"></a>3. Update all currently installed packages. Run the following:</h5>
<pre><code>sudo apt-get update
sudo apt-get upgrade
</code></pre>
<h5><a id="4_Change_the_SSH_port_from_22_to_2200_Make_sure_to_configure_the_Lightsail_firewall_to_allow_it_30"></a>4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.</h5>
<ul>
<li>Run the following:</li>
</ul>
<pre><code>sudo nano /etc/ssh/sshd_config.
</code></pre>
<ul>
<li>On the sshd_config file change the port from 22 to 2200.</li>
</ul>
<pre><code>sudo service ssh restart
</code></pre>
<ul>
<li>Configure to accept connections to port 2200 on the Lightsail server.</li>
</ul>
<h5><a id="5Configure_the_Uncomplicated_Firewall_UFW_to_only_allow_incoming_connections_for_SSH_port_2200_HTTP_port_80_and_NTP_port_123_Run_the_following_41"></a>5.Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123). Run the following:</h5>
<pre><code>sudo ufw status                  
sudo ufw default deny incoming   
sudo ufw default allow outgoing  
sudo ufw allow 2200/tcp          
sudo ufw allow www               
sudo ufw allow 123/udp           
sudo ufw deny 22                 
sudo ufw enable
</code></pre>
<ul>
<li>Reconnect by doing the following:<br>
<code>ssh -i ~/LightsailDefaultKey-us-east-1.pem ubuntu@54.83.157.49 -p 2200</code></li>
</ul>
<h3><a id="Give_grader_access_56"></a>Give grader access.</h3>
<h5><a id="6_Create_a_new_user_account_named_grader_57"></a>6. Create a new user account named grader.</h5>
<p><code>sudo adduser grader</code></p>
<h5><a id="7_Give_grader_the_permission_to_sudo_59"></a>7. Give grader the permission to sudo.</h5>
<pre><code>touch /etc/sudoers.d/grader
sudo touch /etc/sudoers.d/grader
sudo nano /etc/sudoers.d/grader
</code></pre>
<ul>
<li>Add <code>grader ALL=(ALL:ALL) ALL</code> to /etc/sudoers.d/grader and save.</li>
</ul>
<h5><a id="8_Create_an_SSH_key_pair_for_grader_using_the_sshkeygen_tool_66"></a>8. Create an SSH key pair for grader using the ssh-keygen tool.</h5>
<ul>
<li>On the local machine, run the following on git bash</li>
</ul>
<pre><code>cd ~/.ssh
ssh-keygen
Enter file in which to save the key (/c/Users/jmacias2/.ssh/id_rsa): graderKey
cat graderKey.pub
</code></pre>
<ul>
<li>Copy the public key from grader.pub<br>
On our Ubuntu Linux server,</li>
</ul>
<pre><code>mkdir .ssh
touch .ssh.authorized_keys
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
ssh -i grader_key.rsa grader@54.83.157.49 -p 2200
</code></pre>
<h3><a id="Prepare_to_deploy_your_project_83"></a>Prepare to deploy your project.</h3>
<h5><a id="9_Configure_the_local_timezone_to_UTC_84"></a>9. Configure the local timezone to UTC</h5>
<p><code>sudo dpkg-reconfigure tzdata</code></p>
<ul>
<li>On Geographic area select: None of the above.The select UTC on Time Zone.</li>
</ul>
<h5><a id="10_Install_and_configure_Apache_to_serve_a_Python_mod_wsgi_application_87"></a>10. Install and configure Apache to serve a Python mod_wsgi application.</h5>
<ul>
<li>Run the following on the Ubuntu server.</li>
</ul>
<pre><code>sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi python-dev
</code></pre>
<ul>
<li>Enable mod_wsgi</li>
</ul>
<pre><code>sudo a2enmod wsgi
sudo service apache2 start
</code></pre>
<ul>
<li>Create catalog folder</li>
</ul>
<pre><code>cd /var/wwww
sudo mkdir catalog
sudo chown -R grader:grader catalog
cd catalog
</code></pre>
<h5><a id="11_Install_and_configure_PostgreSQL_Do_not_allow_remote_connections_and_create_a_new_database_user_named_catalog_105"></a>11. Install and configure PostgreSQL. Do not allow remote connections and create a new database user named catalog.</h5>
<pre><code>sudo apt-get install libpq-dev python-dev
sudo apt-get install postgresql postgresql-contrib
sudo -u postgres -i

CREATE USER catalog WITH PASSWORD [your password];
ALTER USER catalog CREATEDB;
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
</code></pre>
<h5><a id="12_Install_git_118"></a>12. Install git</h5>
<ul>
<li>Run the following<br>
<code>sudo apt-get install git</code></li>
</ul>
<h3><a id="Deploy_the_Item_Catalog_project_122"></a>Deploy the Item Catalog project.</h3>
<h5><a id="13_Clone_and_setup_your_Item_Catalog_project_from_the_Github_repository_you_created_earlier_in_this_Nanodegree_program_123"></a>13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.</h5>
<ul>
<li>On the path /var/www/catalog run the following:<br>
<code>git clone https://github.com/jesusalem/catalog-items.git catalog</code></li>
</ul>
<h5><a id="14_Set_it_up_in_your_server_so_that_it_functions_correctly_when_visiting_your_servers_IP__address_in_a_browser_128"></a>14. Set it up in your server so that it functions correctly when visiting your server’s IP  address in a browser.</h5>
<ul>
<li>
<p>Create wsgi file on /var/www/catalog/<br>
<code>sudo nano catalog.wsgi</code></p>
</li>
<li>
<p>Add the following:</p>
</li>
</ul>
<pre><code>#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, &quot;/var/www/catalog/&quot;)

from catalog import app as application
application.secret_key = 'super_secret_key'
</code></pre>
<ul>
<li>Install virtual environment</li>
</ul>
<pre><code>sudo apt-get install python-pip
sudo pip install virtualenv
sudo virtualenv venv
source venv/bin/activate
sudo chmod -R 777 venv
</code></pre>
<ul>
<li>install packages</li>
</ul>
<pre><code>sudo pip install Flask
sudo pip install httplib2 
sudo pip oauth2client 
sudo pip sqlalchemy 
sudo pip psycopg2 
sudo pip requests
</code></pre>
<ul>
<li>Configure and enable the virtual host</li>
</ul>
<p><code>sudo nano /etc/apache2/sites-available/catalog.conf</code></p>
<ul>
<li>Add the following:</li>
</ul>
<pre><code>&lt;VirtualHost *:80&gt;
    ServerName 54.83.157.49
    ServerAlias ec2-54-83-157-49.compute-1.amazonaws.com
    ServerAdmin admin@54.83.157.49
    WSGIDaemonProcess catalog python-

path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    &lt;Directory /var/www/catalog/catalog/&gt;
        Order allow,deny
        Allow from all
    &lt;/Directory&gt;
    Alias /static /var/www/catalog/catalog/static
    &lt;Directory /var/www/catalog/catalog/static/&gt;
        Order allow,deny
        Allow from all
    &lt;/Directory&gt;
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
&lt;/VirtualHost&gt;
</code></pre>
<ul>
<li>
<p>On the path /var/www/catalog run the following:<br>
<code>git clone https://github.com/jesusalem/catalog-items.git catalog</code></p>
</li>
<li>
<p>Rename <a href="http://application.py">application.py</a> to __ init__.py</p>
</li>
<li>
<p>On __ init__.py change client_secrets.json line to <code>/var/www/catalog/catalog/client_secrets.json</code> as follows <code>CLIENT_ID = json.loads( open ('/var/www/catalog/catalog/client_secrets.json', 'r').read())['web']['client_id']</code></p>
</li>
<li>
<p>Replace <code>if __name__ == '__main__': app.secret_key = 'super_secret_key' app.debug = True app.run (0.0.0.0, port=5000)</code>  to  <code>if __name__ == '__main__': app.secret_key = 'super_secret_key' app.debug = True app.run()</code></p>
</li>
<li>
<p>On __ init__.py, database_setup.py and <a href="http://addcatalogsitems.py">addcatalogsitems.py</a>  change the line <code>engine = create_engine('sqlite:///catalog.db')</code> to <code>engine = create_engine ('postgresql://catalog:password@localhost/catalog)</code></p>
</li>
<li>
<p>Initiate database and add records by running the following:</p>
</li>
</ul>
<pre><code>python database_setup.py
python addcatalogsitems.py
</code></pre>
<ul>
<li>Restart Apache</li>
</ul>
<pre><code>sudo service apache2 restart
</code></pre>
