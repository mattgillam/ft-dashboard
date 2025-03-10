Live website http://ft-dashboard.ddns.net/

# How to install
## Initial setup
Skip this step if you think you have done them previously
* [Create sudo-enabled non-root user](https://botacademy.ddns.net/2023/09/13/how-to-create-new-sudo-enabled-user/)
* [Install PHP8.2](https://botacademy.ddns.net/2023/09/13/install-php8-2-and-modules-on-ubuntu/). You only need to install fpm and mongodb modules if you are doing docker. Otherwise, it's better to install all the common modules.
* [Install docker-compose](https://botacademy.ddns.net/2023/09/13/how-to-install-docker-compose-on-ubuntu/)
* Install Tailscale

## Setup the dashboard
1. Clone this project `sudo git clone https://github.com/stash86/ft-dashboard`

2. Copy the bots.json.example into bots.json and put the talscale ip, username, and password for all the bots
`cp bots.json.example bots.json`

3. Run `id` to get the uid and gid to be used in the next step

4. Copy the .env.example into .env and put change relevant info (especially uid and gid)
`cp .env.example .env`

### Docker installation
```
docker-compose build
docker-compose run --rm php composer install
docker-compose up -d
```

### Non-docker
1. Install some required libraries through composer
`composer install`

2. Setup cronjob to fetch the data from APIs regularly `crontab -e`
`*/10 * * * * /usr/bin/php <address to the folder>/scripts/fetch_data.php`
The command above will fetch the data every 10 minutes. Change it to suit your preference.

3. Go to scripts/fetch_data.php, find this line `if ($interval >= 600)` and change the value (in seconds) to match the time you set at your cronjob

4. Customize the page yourself to suit your preference, or you can just use it as it is.


## Troubleshooting
* `Error response from daemon: driver failed programming external connectivity on endpoint ft-dashboard-nginx-1 (5715ad4646ad5d120e65509981a1c790b0a7c98eded19a3f6752d759ae9c67e2): Error starting userland proxy: listen tcp4 0.0.0.0:80: bind: address already in use`
Means port 80 is being used right now. Try `sudo lsof -i:80` to see which services using port 80. Tailscale will be one of them, but see whether apache2 is active as well. If it does and you still want to use the docker route (which will use nginx), either stop or remove apache2.


# Available configurations
This settings can be set via `.env` file

### CRON_MINUTES
How often the dashboard fetch data from freqtrade bots. Default is 10

### API
Whether [API endpoints](#api-endpoints) being enabled or not. Default is false

### DEBUG
Whether [debug mode](#debug) being enabled or not. Default is false

### START_0
Whether the profit chart's first point start from zero point. Default is false

### CHART_MIN_TRADES
How many closed trades required before a strategy's profit being plotted in the chart. Default is 2

### BOT_NAME_AS_INDEX
Instead as using strategy's class name, use `bot_name` as index in databae


# API Endpoints
By default, the API isn't enabled. You can activate API endpoints by setting `API=true` in your `.env` file. The supported responses for now are `strategy` for the strategy class name, `strategy_version` for the strategy version, `profit` for performance summary, `trades` for closed trades, `status` for open trades, and `chart_profit_data` for the data needed to plot the profit chart.
The link to access the api is `http://<your ip or domain>/api.php?response=status&bot_id=<bot_id>`
Note that `bot_id` start from 1, and it follow the order of your input inside `bots.json`


# Debug
There are several scripts that are used to debug several aspects of the dashboard. To use it, you need to set `DEBUG=true` in your `.env` file. The debug scripts are located at `/public/debug` folder.

**WARNING!!!**

When you enable this, this means anyone can access those scripts as well. While the scripts won't be able to alter the dashboard, it can be subjected to some DDoS attacks. It's advisable to only enable this mode when you need it, and turn it off after you are done.


# How to update
`git pull` followed by

## Docker
```
docker-compose down
docker-compose run --rm php composer update
docker-compose up -d
```

## Non-docker
`composer update`


# Links
## Affiliate Links
* Vultr for server and bot hosting (you get $100 credit that expires in 14 days) https://www.vultr.com/?ref=8944192-8H
* Binance.com (Non-US citizen) https://accounts.binance.com/en/register?ref=143744527


## Donation
* Github sponsor - https://github.com/sponsors/stash86/
* Patreon (for donation only, no extra perks) - https://patreon.com/stash86

## Crypto
* BTC: 1FghqtgGLpD9F21BNDMje4iyj4cSzVPZPb
* ERC20 : 0x1b7b65e64f3d944d29ba025c3ad0bb9389492370
* TRC20 : TDqRvLXwbkCkBrhdsCm7aDNhfzeJqLRr94
* BEP20 : 0x1b7b65e64f3d944d29ba025c3ad0bb9389492370
