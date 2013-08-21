# GitHub WebHook

These instructions cover how to setup a GitHub Service Hook to deploy code to your web server automatically with GitHub. Stop using FTP to deploy your code changes!

### On the Web Server

Generate ssh keys for the Apache user in the `.ssh` folder of Apache's home folder: `# ssh-keygen -t rsa`. The home folder for the Apache user is `/var/www` in most cases. Run `# cat /etc/passwd` or `# echo ~apache` to verify where the Apache user's home folder is actually located. Depending on your flavor of Linux, the Apache user may be named `www-data` or `apache`. Ensure that the `.ssh` folder is assigned `0700` permissions and is owned by Apache. Also, ensure the newly created key files are given `0600` permissions and are owned by Apache: `# chown apache:apache -R /var/www/.ssh && chmod 0700 /var/www/.ssh && chmod 0600 /var/www/.ssh/*`

In a public webroot on the server make a `webhook` folder with this structure (`# mkdir webhook && cd webhook && touch hook.php webhook.log .htaccess`):

	webhook
	|
	|-hook.php
	|
	|-webhook.log
	|
	|-.htaccess

Grant the Apache user ownership of this folder and its contents: `# chown apache:apache -R webhook`. View the sample `hook.php` and `.htaccess` files I've included. The `hook.php` script is merely a simple example of how to pull new commits to the server and checkout the code into a live Apache webroot. The `.htaccess` file simply protects the `webhook.log` from being publicly readable.

Change directories to the folder containing your website on your server (`# cd /srv/www/example.com`).

Important: *Do not navigate into the public webroot folder (eg. `public_html`). For security reasons I do not store git repositories in publicly available web folders.*

Create a folder containing the domain name of the site with ".git" appended to the name (`# mkdir example.com.git`). Change directories into this newly created folder and issue commands:

- `# git init` to initialize the repo.
- Add the GitHub origin address as a remote: `# git remote add origin git@github.com:username/repo_name.git`.
- Verify that the remote was successfully added: `# git remote -v`. Ensure that ports 22 and 9418 are open in the firewall for git to contact remotes.
- Issue an initial pull request: `# git pull origin master:master`. If everything is configured properly the pull will complete successfully.

Make Apache the owner of the `example.com.git` folder: `# chown apache:apache -R /srv/www/example.com/example.com.git`. Finally, make Apache the owner of the public web folder that the git repository will checkout into: `# chown apache:apache -R /srv/www/example.com/public_html`.

### On GitHub.com

- Add Apache's deployment key in the repository settings (`id_rsa.pub`).
- Visit the "Service Hooks" section of the repository settings and add the "WebHook URL" (ie. `http://yoursite.com/webhook/hook.php?key=TsbWK7eZXGkxipXt6C`). This URL is the address you intend to use as the destination of the WebHook on your server. Don't forget to include the GET key. Don't use mine, generate a new one.
- Ensure that the name of the repository is the same as the domain name (ie. `example.com`). This step ensures that the WebHook will know which website is being updated.

-----

## License

The MIT License (MIT)

Copyright (c) 2013 Stephen Howells

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
