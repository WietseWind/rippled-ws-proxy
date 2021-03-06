# XRPLD Edge Proxy

### wss://xrpl.ws

XRP ledger full history cluster
High available, low latency & geographic routing. Provided by trusted XRP community members.

See: https://xrpl.ws

## Setup

This package (Typescript, node) runs best on [node 14](https://nodejs.org/download/release/latest-v14.x/). Tested on Debian and Ubuntu, and runs in Docker containers (of course).

> Please run with NGINX/Cloudflare/... in front.

1. Make sture node 14 is installed
2. Clone the repository
3. Enter the local (checked out) repository directory
4. Run `npm install` and `npm build` to install dependencies and build 
5. Install the process manager `pm2` globally, `npm install -g pm2` (or run in dev mode without the process manager)
6. Copy `config.default.json` to `config.json` and modify. Remove the `mattermost` and `xrpforensics` section if not required.
7. Run. Install in the `pm2` process manager with `npm run pm2`, or run in dev (verbose) with `npm run dev`. If running in pm2, check status with `pm2 monit` (and check the pm2 manual for more commands)
8. When running in pm2 mode, the proxy will run at **TCP port 4001**, the admin API will run at **TCP port 4002**. Please **KEEP THE ADMIN PORT CLOSED IN THE FIREWALL OF YOUR MACHINE/NETWORK!**. The admin port will automatically be the public port +1. To change the public port, change `pm2.config.js`. When running in dev mode, the proxy will run at TCP port 4000 and the admin port (+1) at 4001. You can change this by invoking the dev command manually (with `nodemon` globally installed, `npm install -g nodemon`) (`npm run build;PORT=4000 DEBUG=app*,msg* nodemon dist/index.js`) with a changed port number, or changing `package.json`.

## Admin API commands
Not all commands, but it's a start.

##### Show clients, state, uplink, etc.
`http://#{ip}:4002/status?details=true`

##### Migrate uplink clients for maintenance
`http://#{ip}:4002/uplink/#{hash}/migrate`
All connected clients to the given uplink will be reconnected to another uplink, and websocket subscriptions (`subscribe` command) will be automatically re-subscribed at the new connected uplink server. Once done, a soft handover will take place. The client will notice nothing and stay connected to the proxy. Within a second the uplink can be taken down for maintenance without anybody noticing. After maintenance, use the next command to enable it in the pool again.

Verify clients connecte to a specific uplink with the `/status?details=true` command.

##### Take uplink back into pool
`http://#{ip}:4002/uplink/#{hash}/up`

##### Add uplink into running config (non persistent)
`http://#{ip}:4002/add-uplink/#{uplinkType}/#{protocol}/#{hostname}/#{hash}`

Eg.
`http://#{ip}:4002/add-uplink/basic/wss/some-backend.local/some-md5`
