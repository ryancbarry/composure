# Composure App Platform

### Architecture

```
Hypriot Raspberry Pi image
  Raspbian
    * SSH daemon
    * Docker daemon
      > Caddy server
        - Exposes port 80
        - Intercepts all https://{appid}.composure requests, proxies them to apps.
        - Serves the static files that've been cached during a lite app install.
        - Proxies requests for heavy apps to the app's main service.
      > Main system docker container (privileged container) [https://system.composure] (API has accompanying app toolkit)
        - Exposes port 8000
        - Docker client
        - SSH client
        - Nodejs, system API service (exposes OS and app-related actions to the client)
          - Uses docker client, docker-compose, ssh client
      > Home screen [https://composure]
        - Exposes port 8001
        - List of installed apps (both lite and heavy apps, run, uninstall apps)
        - List of running apps (quit apps)
        - System control (sleep, restart, shut down, log out)
        - Notification panel
      > { User-installed apps, both lite and heavy } [https://{appid}.composure]
        - Port-managed
      > Inter-app pubsub system [https://transfer.composure] (API has accompanying app toolkit)
        - For one-way data flow to any number of apps.
        - Apps can subscribe to messages through webhooks and/or websockets.
        - User clipboard
        - Data import/exports
        - App notifications
      > OAuth2 service (token and session management API) [https://lock.composure] (API has accompanying app toolkit)
        - Lock screen (Log in)
      > Personal App store (install both lite and heavy apps) [https://apps.composure]
        - Everyone hosts their own personal app store.
        - They can add apps to their store from another person's app store,
            or by registering an app themselves.
        - Adding an app means posting metadata to the app store:
            - Heavy apps: JSON file containing app name, description, url to control package with docker compose 
              config files, etc. Docker compose projects are registered as heavy apps by their maintainers.
            - Lite apps: JSON file containing url to site, which contains an HTML5 manifest (icons, list of files 
              to cache, etc. Web sites are registered as lite apps by users when they're known to work from a web archive.
      > Settings [https://settings.composure]
        - Mapping apps to app ids
        - Scoping notifications
```

[A Beginner's Guide to Using the Application Cache](http://www.html5rocks.com/en/tutorials/appcache/beginner/)

[Download a site's manifest files](https://www.npmjs.com/package/manifest)

[Inspect docker status](https://www.npmjs.com/package/dockerode)

[Execute a unix command with Node.js](https://dzone.com/articles/execute-unix-command-nodejs)

[Portable unix shell commands for Node.js](https://github.com/shelljs/shelljs)

[Run a remote command via SSH](http://www.cyberciti.biz/faq/unix-linux-execute-command-using-ssh/)


### Raspberry Pi image

[http://blog.hypriot.com/getting-started-with-docker-on-your-arm-device/](http://blog.hypriot.com/getting-started-with-docker-on-your-arm-device/)


### Build the system docker image

```
cd /root
git clone https://github.com/ryancbarry/composure.git
cd composure
docker build .
```

### Run the system container

```
docker run --name system --privileged -p 8000:8000 -v /dev:/dev -d {image_id}

```

### Test the system API

```
curl http://localhost:8000
```


### Static proxy server

```
https://github.com/armhf-docker-library/caddy
```

## Roadmap

  * Configure docker compose for the system container.
  
  * Create Home app.

  * Build and host (Docker hub) a Docker image from the control package.
  
  * Build and host (CDN) a Raspberry Pi micro SD card image that comes loaded with everything running.
    
  

