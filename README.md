# docker-nginx-dev-env
NOKNOK Studios development environment for PHP / Wordpress development projects.

## Tech & Features
- nginx
- xdebug (configured for VSCode)
- ssl
- mariadb
- phpmyadmin
- mailhog
- webhook relay (Provided by [Webhook Relay](https://webhookrelay.com/))

## Starting the docker-compose project
```docker-compose up -d```

## Accessing services over HTTP
- nginx: https://docker.myproject.local
- phpmyadmin: https://docker.phpmyadmin.local:8443
- mailhog: https://docker.mailhog.local:8026


## Hostname
We recommend assigning your project a hostname. In our experience some browsers (and some JS libraries) act weirdly when interacting with ```https://localhost``` so assigning a hostname to your project can help prevent headaches during development.


### Update your hosts file
Make sure you add your hostname and any subdomains to your hosts file so you can access your webserver from the host machine.
On windows this can be found under ```c:\Windows\System32\Drivers\etc\hosts```. Also make sure you add hosts for phpmyadmin and mailhog aswell.

#### Example
```
127.0.0.1 docker.myproject.local
127.0.0.1 mysubdomain.docker.myproject.local
127.0.0.1 docker.phpmyadmin.local
127.0.0.1 docker.mailhog.local
```

# SSL Setup
SSL is preconfigured to use certificates in the ssl directory with the following filenames e.g
- cert_file: ```ssl/server.crt```
- cert_key: ```ssl/server.key```

## Creating a development certificate using mkcert
We recommend using mkcert to generate a dev certificate. 

After installing mkcert and running the ```mkcert install``` command you can generate this certificate automatically with the following command (replace docker.myproject.local with your projects hostname)

```SHELL
mkcert --key-file ./ssl/server.key --cert-file ./ssl/server.crt "docker.myproject.local" "*.docker.myproject.local" "docker.mailhog.local" "docker.phpmyadmin.local" localhost 127.0.0.1 ::1
```

## Webhook Relay
When it comes to testing webhooks locally in a office environment, setting things up securely can be a little bit of a challenge. This project includes a Webhook relay provided by [Webhook Relay](https://webhookrelay.com/) to help get those pesky webhooks to your private server. The free version of Webhook Relay should be all you really need to get things going.

### Setup

To get started load your access token into your environment file (.env) like so.

```ini
WEBHOOK_RELAY_KEY=<your_key_here>
WEBHOOK_RELAY_SECRET=<your_secret_here>
```

Next setup your webhook relay bucket by going to "Request Forwarding" > "Buckets" > "Create Bucket". Name the bucket whatever you want.

Once created, save your bucket ID to your environemtn file (.env)

```ini
WEBHOOK_RELAY_BUCKET=<your_bucket_id>
```

Next you need to set an output destination so your relay agent can send webhooks to the right place. Open your buckets configuration page and add a new "Output Destination". Set the name to whatever you like but set the destination to ```http://nginx.local```. Make sure you also have the new destination set to "Internal".

Lastly to make sure we're forwarding complete URLs with paths and query strings attached we need to disable the 'Lock destination path' on our output destination settings.

## XDebug
XDebug is configured for VSCode by default. By setting your workspace to the project root vscode will inherit the launch settings from .vscode/launch.json. You can launch the PHP debugger using the (F5) key.

If you want to configure XDebug for your own IDE you can use the following settings
- port: 9000
- Path mappings: ```/app/public => ${workspaceRoot}/public```

## Performance issues with WSL2 & HyperV
Docker containers seem to have massive performance issues with WSL2. You can read more about it [here](https://github.com/microsoft/WSL/issues/4387). For that reason we highly recommend that windows users use Hyper V or find some alterntive way of mounting your ```/public``` directory.


