# MySQLSafeBackup
MySQLSafeBackup lets you dump / backup mysql databases, encrypt and also compress the output files. Also restoring of encrypted mysql backup files is supported.

# Making MySQLSafeBackup work with gnupg2
To do this, you have to add the file gpg.conf in the GNUPGHOME of
MySQLSafeBackup, with the following contents:

  use-agent
  pinentry-mode loopback

Also add gpg-agent.conf with:

  allow-loopback-pinentry

# Debian package
You can find a .deb package for using with dpkg here: https://github.com/VerboteneZone/MySQLSafeBackup/releases

<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=helga_welter%40hotmail%2ecom&lc=MQ&item_name=Oliver%20Welter&item_number=mysqlsafebackup&no_note=0&currency_code=EUR&bn=PP%2dDonationsBF%3abtn_donate_LG%2egif%3aNonHostedGuest">Make a donation</a>

<h4>Beware</h4>
<p>
I take no responsibility for any data loss or corruption when using mysqlsafebackup or the following documentration! 
By using this, you agree to this term and you take the responsibility on your own!
</p>

MySQLSafeBackup depends on the following commands:
<ul>
<li>awk</li>
<li>sed</li>
<li>grep</li>
<li>mysql</li>
<li>mysqldump</li>
<li>gpg</li>
<li>aespipe</li>
<li>bzip2</li>
<li>gzip</li>
<li>bunzip2</li>
<li>gunzip</li>
<li>makepasswd</li>
</ul>

On Debian, you can use the following command to install the needed dependencies:<br />
<code>apt-get install mawk sed grep mysql-client mysql-common gpgv aespipe makepasswd gzip bzip2</code>

After installation of MySQLSafeBackup, edit /etc/mysqlsafebackup/mysqlsafebackup.conf and adapt it to your needs. For a list of possible command arguments, just type<br />
<code>mysqlsafebackup --help</code>

If you want to use MySQLSafeBackup as a cronjob, you have to edit /etc/mysqlsafebackup/mysqlsafebackup.conf and set <strong>MODE=usage</strong> to <strong>MODE=backup</strong>.
Normally, the command mysqlsafebackup, without any argument, would display a short usage and exits. MODE=backup leads mysqlsafebackup command to immediatly start the backup, configured in /etc/mysqlbsafebackup/mysqlsafebackup.conf and not to display the short usage.
Now, you can place a symbolic link in /etc/cron.daily:<br />
<code>ln -s /usr/sbin/mysqlsafebackup /etc/cron.daily</code>

For a detailed documentation of configurable options, type:
<code>mysqlsafebackup -d</code>

<hr/>

A useful tutorial on how to configure gpg, can be found here: https://alexcabal.com/creating-the-perfect-gpg-keypair/

<hr/>

For the encryption to work properly, gpg has to be configured and you have to set parameters, beginning with <strong>GPG</strong> within /etc/mysqlsafebackup/mysqlsafebackup to their correct values.

The important ones are GPGKEYID and GPGHOMEDIR. For some reasons it could be useful to make a copy of your gpg home directory (e.g. /root/.gnupg) and copy it to /etc/mysqlsafebackup/gpg, for example. This is also the default for GPGHOMEDIR.

If you dont know your public key id, which has to be set at GPGKEYID, you can find it with:<br />
<code>gpg --list-keys</code>

Example output:<br />
<pre>
 /root/.gnupg/pubring.gpg
 ------------------------
 pub   4096R/1234567F 2016-06-21
 uid                  Verbotene Zone (Example key) <contact@verbotene.zone>
 uid                  [jpeg image of size 3109]
 sub   4096R/09876543 2016-06-21
 sub   4096R/23456789 2016-06-21
</pre>

So GPGKEYID has to be <strong>1234567F</strong> and also a private key must exist for this public key.

Now, save the private key passphrase to the file, which GPGPASSWDFILE is set to. After GPGKEYFILE is set to your needs, you can generate the keyfile. But beware! The file will be overwritten, without a warning! This is done by executing:<br />
<code>mysqlsafebackup --gen-keyfile</code>

<h5>Remeber to protect the password and keyfile!!</h5>

Also, you can generate your own keyfile, by executing (e.g., set 1234567F to your public key id):<br />
<code>makepasswd --count 65 --chars 20 | gpg --encrypt -r 1234567F > /path/to/keyfile</code>

After you have finished, setting up /etc/mysqlsafebackup/mysqlsafebackup.conf to your needs, you are ready to use <strong>mysqlsafebackup</strong> command. Please test backup and restore, before using MySQLSafeBackup in a production environment.

Hint:
You can use <code>mysqlsafebackup -c /path/to/a/backupfile.sql.gz.aes > /path/to/decrypted/sqlfile.sql</code> to convert an encrypted and compressed backup file to normal sql file.
