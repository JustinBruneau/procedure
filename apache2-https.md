## Debian 11 :

Installation de Certbot, Apache2 pour Mettre un site en HTTPS
Prèrequis avoir un nom de domaine et pouvoir changer son DNS (pour moi : cloudflare)

'''	
sudo apt update
sudo apt install apache2
sudo apt install certbot python3-certbot-apache
sudo certbot --apache -d example.com -d www.exemple.com
sudo nano /etc/apache2/sites-available/example.com.conf

'''
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/html

    # Rediriger tout le trafic HTTP vers HTTPS
    RewriteEngine on
    RewriteCond %{SERVER_NAME} =www.example.com [OR]
    RewriteCond %{SERVER_NAME} =example.com
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>

<VirtualHost *:443>
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf

    <Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

'''
sudo a2enmod rewrite
sudo a2enmod ssl
sudo a2ensite example.com
sudo systemctl restart apache2
sudo certbot renew --dry-run
