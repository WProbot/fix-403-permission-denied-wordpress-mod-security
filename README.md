# fix-403-permission-denied-wordpress-mod-security
fix 403 permission denied wordpress mod security

#Usage

Insert in apache2.conf

inside virtual host -> between </Directory> and </VirtualHost>

like:

<VirtualHost 12.11.11.11:8080>

    ServerName domain.com
    ServerAlias www.domain.com
    ServerAdmin info@domain.com
    DocumentRoot /home/admin/web/domain.com/public_html
    ScriptAlias /cgi-bin/ /home/admin/web/domain.com/cgi-bin/
    Alias /vstats/ /home/admin/web/domain.com/stats/
    Alias /error/ /home/admin/web/domain.com/document_errors/
    #SuexecUserGroup admin admin
    CustomLog /var/log/apache2/domains/domain.com.bytes bytes
    CustomLog /var/log/apache2/domains/domain.com.log combined
    ErrorLog /var/log/apache2/domains/domain.com.error.log
    <Directory /home/admin/web/domain.com/public_html>
        AllowOverride All
        Options +Includes -Indexes +ExecCGI
        php_admin_value open_basedir /home/admin/web/domain.com/public_html:/home/admin/tmp
        php_admin_value upload_tmp_dir /home/admin/tmp
        php_admin_value session.save_path /home/admin/tmp
    </Directory>
    <Directory /home/admin/web/domain.com/stats>
        AllowOverride All
    </Directory>
    
<LocationMatch "/>
SecRuleRemoveById 910006 # Google robot activity - Useful in someways but noisy for sites where you want them crawled
SecRuleRemoveById 960015 # Request Missing an Accept Header -  Allow for Google Reader
</LocationMatch>

<LocationMatch "/wp-includes/">
SecRuleRemoveById 960010 # Request content type is not allowed by policy - Allows for amongst other things spell check to work on admin area
SecRuleRemoveById 960012 # Require Content-Length to be provided with every POST request - Same as above
</LocationMatch> 

<LocationMatch "(/wp-admin/|/wp-login.php)">    
SecRuleRemoveById 950005 # Remote File Access Attempt - This rule probably doesn't need to be disabled by everyone but it stops me putting /etc/ in posts and other such linux paths.
SecRuleRemoveById 950117 # Remote File Inclusion Attack - Disable to allow http:// to be passed in args
</LocationMatch>

<LocationMatch "(/wp-admin/post.php|/wp-admin/options.php|/wp-admin/theme-editor.php|/wp-includes/)">
SecRuleRemoveById 950006 # System Command Injection - Another rule that probably doesn't need to be disabled by everyone it stops .exe and various other extensions being passed in args.
</LocationMatch>
   
</VirtualHost>
