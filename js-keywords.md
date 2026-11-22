# JS Analysis Keywords — canonical battery
# The keyword set for the in-memory JS battery (js-analyst skill). These
# are KEYWORDS to look for — not commands to run. Every live host gets the
# battery, no exceptions; hosts serving no JS get that negative recorded.

Output convention: tag every hit with its source JS URL
("URL: `<js-url>` `<hit>`"), dedupe, and tee into one results file per
category (js_encoded, js_ips, js_sinks, js_secrets, js_urls), merged into
a master picklist.

IMPORTANT-only filter: the picklist keeps IMPORTANT hits — endpoints,
internal/dev/env/staging hosts, secrets-adjacent strings, auth surfaces —
NOT a blanket dump of every URL. Strip: third-party CDN/lib/asset URLs,
analytics/cookie-banner hosts, w3.org schema refs, webpack/npm
boilerplate, doc-site links. Target .js/.json/.yml/.yaml content is
NEVER saved to disk — tags and lists only.

--------------------------------------------------------------------------------

## 1. Encoded values (js_encoded)

Match values starting with these base64-ish prefixes:

| Prefix | Meaning |
|---|---|
| `eyJ` | base64 of `{"` — JSON/JWT start |
| `YTo` | base64 of PHP serialized array (`a:`) |
| `Tzo` | base64 of PHP serialized object (`O:`) |
| `PD[89]` | base64 start of `<` (XML/HTML embedded) |
| `aHR0cHM6L` | base64 of `https://` |
| `aHR0cDo` | base64 of `http://` |
| `rO0` | hex form of Java serialization magic (`AC ED`) |

Charset after prefix: `[%a-zA-Z0-9+/]+={0,2}`; truncate hits to ~50 chars.

## 2. Internal / any IPv4 addresses (js_ips)

Full octet-validated IPv4 regex (each octet
`25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?`). Special attention: RFC1918
(10/8, 172.16/12, 192.168/16), 127/8, metadata IPs (169.254.169.254,
service endpoints) — SSRF/internal-hint leads.

## 3. Sinks / sources (js_sinks)

Capture a 0-50 char context window after each hit:
- window\.location, document\.location, location\.href, location\.replace,
  location\.assign, location\.hash, location\.search
- document\.URL, document\.referrer, document\.write, document\.cookie
- window\.name, localStorage, sessionStorage
- addEventListener, innerHTML, outerHTML, setAttribute
- eval\(, Function\(, setTimeout\(', setInterval

## 4. Secrets (js_secrets)

Full alternation (keyword + up to 50 chars trailing context; several
variants deliberately included):

```
access_key|access_token|accessKey|accessToken|credential|database
email|api_key|key|api_secret|apikey|app_id
app_key|app_secret|application_id|asws_secret_token|aws_access|aws_config
aws_key|aws_secret|aws_secret_access_key|aws_secret_key|aws_token|bucket_password
client_secret|connectionstring|consumer_secret|credentials|db_password|db_server
db_username|dbpasswd|dbpassword|dbuser|django_password|email_host_password
facebook_app_secret|facebook_secret|fb_app_secret|fb_secret|google_id|google_oauth
google_oauth_client_id|google_oauth_client_secret|google_oauth_secret|google_secret|google_server_key
gsecr|heroku_api_key|heroku_key|heroku_oauth|heroku_oauth_secret|heroku_oauth_token
heroku_secret|heroku_secret_token|jwt_secret|jwt_token|s3_bucket|jwt_secret_token
keyPassword|mailgun_key|mailgun_secret|mysql_password|oauth_key|oauth_token
oauth2_secret|password|paypal_identity_token|paypal_sandbox|paypal_secret|paypal_token
postgres_password|private|private_key|redis_password|root_password|sa_password
secret|secret_access_key|secret_bearer|secret_key|secret_token|secretKey
security_credentials|send_keys|sf_username|slack_channel|slack_key|slack_secret
slack_token|slack_url|slack_webhook|slack_webhook_url|square_access_token|square_apikey
square_app|square_app_id|square_appid|square_secret|square_token|squareSecret
squareToken|ssh2_auth_password|sshkey|storePassword|strip_key|strip_secret
strip_secret_token|strip_token|stripe_key|stripe_secret|stripe_secret_token|stripe_token
stripSecret|stripToken|twitter_api_secret|twitter_consumer_key|twitter_consumer_secret|twitter_key
twitter_secret|twitter_token|twitterKey|twitterSecret|wordpress_password|aws_access_key
api|v1|firebase|v2|ssh key|config
jdbc|API_KEY|google_api|oauth_token_secret|passwd
gcp|db|sql|\.env|graphql|\.git
heroku|pwd|token|api_?key|ftp api_key|rest
twilio_api_key|twilio_account_sid|possible_Creds|json|mailgun_api_key|backup
amazon_mws_auth_token|WebSocket|amazon_aws_access_key_id|rsa_private_key|paypal_braintree_access_token|stripe_standard_api
square_oauth_secret|twilio_app_sid|google_captcha|htaccess|stripe_restricted_api
export|dump|authorization_api|ssh_dsa_private_key|json_web_token
slack|authorization_bearer|access key|swagger|admin|archive
SSH_privKey|debug|pgp_private_block|github_access_token|facebook_access_token
authorization_basic|amazon_aws_url|api key
```

The broad generic terms are deliberate too: api, v1, v2, config, db, sql,
.env, .git, graphql, json, rest, backup, export, dump, debug, admin,
swagger, websocket, htaccess, pwd, token, key, firebase, gcp, heroku.

## 5. URLs (js_urls)

Extract every absolute URL: `https?://` up to a quote, angle bracket,
space, or `)`. Group by source bundle file. Watch for: third-party hosts,
internal/staging hosts, endpoints no wordlist would ever cover.
