# The deploy.sh script is the orchestrator.
     - It decides which environment (blue or green) is currently active by reading .active_env.
     - It then deploys the newer version into the inactive environment by running:



# the logic is: detect active → deploy inactive → switch proxy → mark new active.



📌 Summary- Blue/Green containers run on 8081 and 8082 → direct access if you want to peek.
- Proxy container runs on 80 → exposed as http://localhost.
- Proxy decides which backend (Blue or Green) is active → that’s why you only need http://localhost.
👉 So think of it like this:- http://localhost → always points to the active environment (via proxy).
- http://localhost:8081 / http://localhost:8082 → direct access to Blue/Green containers, regardless of proxy.


#### we need to have 
1 Nginx container (the load balancer)

1 server block inside its config

2 backend containers (blue-web and green-web)

The upstream selection tells Nginx which backend to send traffic to.


#### the nginx learnign flow

### the nginx configure file writting standard
- events {} block → Configures how Nginx handles connections internally
- If a user opens your site in a browser then it counts as a connection If the user keeps the tab open, maintains keep-alive, or streams data, the connection stays open.

- http {} block → Defines HTTP server behavior (reverse proxy, serving files, etc.)
- include /etc/nginx/mime.types; → Loads a file mapping file extensions to MIME (Multipurpose Internet Mail Extensions) types (e.g., .html → text/html).
- A MIME type file (like /etc/nginx/mime.types) is simply a list that maps file extensions (like .html, .css, .jpg) to their correct MIME types (text/html, text/css, image/jpeg).
- in the nginx container we will get this file of mapping like type{} object 

## Why does Nginx need the mime.types file?

- Because when Nginx serves a file (static content), it must set the correct:Content-Type: text/html
Content-Type: image/png
- If it doesn’t specify it, Nginx will use the default: default_type application/octet-stream;
- application/octet-stream means unknown binary data, so browsers treat it as a file to download, not display. so if the default type is rendered then the browser will ask us to dowload the file/photo


#### the server block (The server block = one virtual host.)

- A server block defines a virtual server — basically one “website” or “service” that Nginx will serve.
- the server {} block is: A configuration rule telling Nginx how to handle incoming requests.


#### Predefined Words (Directives) in Your File
Here are the built‑in Nginx directives used in your proxy/nginx.conf:
Top‑level blocks
- events {} → Configures connection handling.
- http {} → Defines HTTP server behavior.
- upstream {} → Defines a group of backend servers.
- server {} → Defines a virtual server.
- location {} → Defines rules for handling specific request paths.
Inside events
- worker_connections → Max simultaneous connections per worker.
Inside http
- include → Includes another config file (here: MIME types).
- default_type → Default MIME type for unknown files.
Inside upstream
- server → Defines backend server(s) (here: blue-web:80 or green-web:80).
Inside server
- listen → Port to listen on (here: 80).
- server_name → Hostname(s) this server responds to.
Inside location /
- proxy_pass → Forwards requests to the backend upstream.
- proxy_http_version → Sets HTTP version for proxying.
- proxy_set_header → Defines headers passed to backend (Host, X‑Real‑IP, etc.).
Inside location /health
- access_log off → Disables logging for health checks.
- add_header → Adds a response header.
- return → Immediately returns a status code and body.

