# stellar-documentations

here's a step-by-step guide to deploy the Cattr server application on your AWS EC2 instance:

1. Create EC2 Instance with Ubuntu Latest follow the screenshots: 
   Screenshot : https://prnt.sc/kwpHv3SM_n4F
   Screenshot : https://prnt.sc/DG7GBMaP7KDM
   Screenshot : https://prnt.sc/oOk3u-IHTMKM

Login to EC2 instance with the PEM file generated via SSH

1. **Update your system**
   ```bash
   sudo apt-get update
   ```

10. **Install Nginx**
   ```bash
   sudo apt-get install nginx
   ```
after install nginx if you load ip in browser it should show you nginx default page

4. **Install PHP and necessary modules**
   ```bash
   sudo add-apt-repository ppa:ondrej/php
   sudo apt-get update
   sudo apt-get install php php-{bcmath,bz2,intl,gd,mbstring,mysql,zip,fpm,curl,xml}
   ```
if you choose ubuntu latest it will auto install php latest version 

2. **Install Node.js**
   ```bash
   curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```
Node: v18 will install 

3. **Install MySQL**
   ```bash
   sudo apt-get install mysql-server
   sudo mysql_secure_installation
   ```


5. **Install Composer and cURL**
   ```bash
   sudo apt install curl
   curl -sS https://getcomposer.org/installer | php
   sudo mv composer.phar /usr/local/bin/composer
   ```

6. **Clone the Cattr Server Application**
   ```bash
   cd /var/www/html

   git clone https://github.com/cattr-app/server-application.git .
   
   ```
   clone the applion inside /var/www/html and remove default nginx index file if there

7. **Install the Server Application Dependencies**
   ```bash
   composer install
   ```

8. **Configure the Server Application**
   Run the installation manager:
   ```bash
   php artisan key:generate
   php artisan migrate --seed --seeder=InitialSeeder
   php artisan cattr:make:admin

   ```
you will be able to login with following credentials

   admin@cattr.app
   password


9. **Build the Frontend Application**
   ```bash
   yarn
   yarn prod
   ```

10. **Configure Nginx**
   ```bash
   sudo nano /etc/nginx/sites-available/stellar.buildberg.co
   ```
   Before NGINX configure first of all point your domain with attached elastic IP 
   and replace stellar.buildber.co with your domain name and put this server default server block 
   
   ```nginx
   server {
    listen 80;
    server_name stellar.buildberg.co;
    root /var/www/html/public;

    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock; # Adjust according to your PHP version and PHP-FPM socket
    }

    location ~ /\.ht {
        deny all;
    }
}

   ```

   Enable the site and restart Nginx:
   ```bash
   sudo ln -s /etc/nginx/sites-available/stellar.buildberg.co /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

   here also make sure file name 


11. **Configure SSL with Let's Encrypt**
   ```bash
   sudo apt-get install certbot python3-certbot-nginx
   sudo certbot --nginx
   sudo systemctl reload nginx

   ```

12. ** Install and configure phpmyadmin to manage database ** (Optional)

Here's a step-by-step guide to install phpMyAdmin on a Linux server with Nginx:

1. **Update Package List**
   - Before installing new packages, it's a good practice to update your package list. Run the following command:
   ```bash
   sudo apt-get update
   ```

2. **Install phpMyAdmin**
   - Use the following command to install phpMyAdmin:
   ```bash
   sudo apt-get install phpmyadmin
   ```
   - During the installation, a prompt will appear asking for the web server that should be automatically configured to run phpMyAdmin. Since Nginx is not in the list, hit TAB, and then ENTER to skip this step.
   - Then, you'll be asked if you want to use 'dbconfig-common' to set up the database. You can select 'Yes'.
   - Next, you'll be asked to provide the password for the 'phpmyadmin' user. Enter a strong password and remember it.

3. **Configure Nginx to Serve phpMyAdmin**
   - Open your Nginx default server block configuration file. This is typically located at `/etc/nginx/sites-available/default`. You can use a text editor like nano to open it:
   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```
   - Add the following location block within the `server` block:
   ```nginx
   location /phpmyadmin {
       root /usr/share/;
       index index.php index.html index.htm;
       location ~ ^/phpmyadmin/(.+\.php)$ {
           try_files $uri =404;
           root /usr/share/;
           fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
           fastcgi_index index.php;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }
   }
   ```
   - Save and close the file. Please note that the `fastcgi_pass` directive should point to your PHP-FPM socket. The above example assumes PHP 7.4. If you're using a different version of PHP, adjust it accordingly.

4. **Restart Nginx Server**
   - To apply the changes, restart the Nginx server with the following command:
   ```bash
   sudo systemctl restart nginx
   ```

5. **Access phpMyAdmin**
   - Open a web browser and navigate to `http://your_server_ip/phpmyadmin`. You should see the phpMyAdmin login page.

Remember to replace `your_server_ip` with the actual IP address of your server. Also, the exact steps may vary depending on your specific server configuration. Always ensure you understand each step before performing it.









Sure, here's a more structured and formatted version of the instructions:

```markdown
# Desktop Application Setup

## NPM and Yarn Installations

If you prefer to use npm, update the `package.json` dependencies as follows:

Replace 
```json
"@cattr/node": "^4.0.0-RC4"
```
with 
```json
"cattr-node": "^4.0.0-RC4"
```

## Installation Steps

1. Clone this repository.
2. Install dependencies using either `npm install` or `yarn install`.
3. Run webpack using either `npm run build-watch` or `yarn build-watch`. If you don't want to watch files for changes, use `npm run build-development` or `yarn build-development`.
4. Once the build completes, run `npm run dev` or `yarn dev` to launch the client in development mode.

## Development Mode

In development mode, there are two major differences from production mode:

- Automatic error reporting is disabled.
- The installation uses a different keychain service name and application folder path (with a "-develop" suffix).

## Build Process

1. Ensure that all necessary dependencies are installed.
2. Ensure that `package.json` and `src/base/config.js` contain the correct configuration.
3. Build the application in production mode using `npm run build-production` or `yarn build-production`.
4. Build the executable for your preferred platform (the output directory is `/target`).

## Sentry Sourcemaps

1. Copy `.sentry.main.example` to `.sentry.main`, then fill it with the configuration for the Main process project.
2. Copy `.sentry.renderer.example` to `.sentry.renderer`, then fill it with the configuration for the Renderer process project.
3. During release preparation, run `npm run build-release` or `yarn build-release` to build and submit release files to Sentry.

## Building Executables

- **macOS**: `npm run package-mac` or `yarn package-mac` will produce a signed and notarized DMG.
- **Linux**: `npm run package-linux` or `yarn package-linux` will produce a Tarball, DPKG, and AppImage.
- **Windows**: `npm run package-windows` or `yarn package-windows` will produce an installer and portable executables.
```

