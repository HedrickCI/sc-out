Demo vid: https://youtu.be/7ZgAJ9MDGGw

Im sorry. I am not shy or short of words, my first demo recording ended up being four minutes long. 


The Fantasy Premier League is the worlds largest fantasy sports league by player volume, one of the main challenges that people face is that outside of the official FPL data analysis, there is no other way players can make a team on their own by analysing a wide range of player data. That is why i have embarked on creating sc-out, which is a triple entendre actually. 

Sc-out fetches live data from the official FPL API and uses a proprietary scoring algorithm to rank every player by value, form, xg involvement, etc. Player cards can be expanded to reveal analytsics powered by Claude (the de-facto ALU agent atp).

The webapp can be found at http://52.55.9.183 (our load balancer)

Features:

•	Live FPL player data from the official Premier League API
•	Proprietary SC-OUT scoring algorithm (form, xGI, value, differential bonus)
•	Search, filter by position, sort by multiple metrics, price range slider
•	AI-powered player analysis via Claude API (Anthropic)
•	3-proxy CORS fallback chain for reliability
•	Graceful error handling with retry functionality
•	Fully responsive dark-mode UI

External APIs Used:

FPL Bootstrap Static API
https://fantasy.premierleague.com/api/bootstrap-static/
Official Premier League API providing live player stats, team data, and gameweek info. Public API — no key required.

CORS Proxy Chain (corsproxy.io → AllOrigins → CodeTabs)
A chain of 3 public CORS proxies tried in sequence. If one fails, the app automatically falls back to the next. No API key required.

Claude API — claude-sonnet-4-20250514 (Anthropic)
https://api.anthropic.com/v1/messages
Powers the AI player analysis text shown when expanding a player card. API key is handled server-side by the platform and is never exposed in client-side code or the repository.


TO RUN LOCALLYYY!!!!!
clone this repo
git clone https://github.com/HedrickCI/sc-out.git
open sc-out-final.html in any modern web browser
BOOM, you got it!

Deployment
The app is deployed across two web servers load-balanced by a third server:

•	Web01: 3.83.207.131
•	Web02: 18.212.197.79
•	Load Balancer (Lb01): 52.55.9.183

Steps taken:
1.	Installed Nginx on Web01 and Web02
2.	Copied sc-out-final.html to /var/www/html/index.html on both servers via SCP
3.	Installed and configured Nginx on Lb01 as a round-robin reverse proxy
4.	Configured UFW firewall on all three servers (ports 22, 80, 443)
5. Pointed f41r.tech domain A record to load balancer IP 52.55.9.183


Load Balancer Nginx Configuration:
upstream web_servers {
    server 3.83.207.131;
    server 18.212.197.79;

server {
    listen 80;
    server_name f41r.tech www.f41r.tech;
    location / {
        proxy_pass http://web_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

SC-OUT Scoring Algorithm:

Each player receives a composite SC-OUT Score calculated from: points-per-million (value), current form, expected goal involvement (xG + xA), minutes reliability, differential ownership bonus, bonus points, and clean sheet contributions. Scores range from 0 (Avoid) to 100+ (Elite).

Challenges & Solutions:

•	CORS Restrictions: The FPL API blocks direct browser requests. Solved using a chain of 3 CORS proxies (corsproxy.io, AllOrigins, CodeTabs) with automatic fallback.
•	API Downtime: Implemented graceful error handling with retry button and user-friendly error messages.
•	AI Latency: Claude AI analysis is generated on-demand per card expansion to avoid blocking the initial data load. Results are cached so the same player is not re-fetched.

Credits & Attribution:

•	Player & stats data: Fantasy Premier League (official API) — https://fantasy.premierleague.com
•	CORS proxies: corsproxy.io, AllOrigins (allorigins.win), CodeTabs
•	AI analysis: Anthropic Claude — https://anthropic.com
•	Fonts: Google Fonts — Bebas Neue, DM Sans, DM Mono



