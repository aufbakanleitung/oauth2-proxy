# OAuth2-proxy

zie vooral: https://www.talkingquickly.co.uk/webapp-authentication-keycloak-OAuth2-proxy-nginx-ingress-kubernetes

## Keycloak configureren

First we'll need to create a client application with Keycloak. Crate a new OpenID Connection application and set:

Client ID: `oauth2-proxy`

Access Type: `confidential`

Valid Redirect URLs: `https://oauth.sitesandsuch.nl/oauth2/callback`


You'll then need to save the entry and go to the newly available "Credentials" tab and make a note of the "Secret".

Finally we go to the "Mappers" tab, choose "Create" and select:

Name: `Groups`

Mapper Type: `Group Membership`

Token Claim Name: `groups`

All other options "On"

And then choose save. This ensures that the groups the user is a member of are passed back to OAuth2 Proxy and subsequently to the application itself.


## Oauth-proxy installeren

`helm repo add oauth2-proxy https://oauth2-proxy.github.io/manifests`

`helm repo update`

`helm install oauth2-proxy oauth2-proxy/oauth2-proxy -n oauth2-proxy -f values-oauth2-proxy.yaml`

The key fields to update with your own values are:

clientSecret: This is the client secret noted down from the Keycloak credentials page
cookieSecret: This can be randomly generated with: openssl rand -base64 32 | head -c 32 | base64
loginurl, redeemurl, validate_url: which should be updated to match the relevant URL's for your Keycloak installation and realm (in the example above I'm using the master realm)
cookiedomain, whitelistdomain: which should be updated to match the base URL you're deploying services on. E.g. in this example configuration I have sso.ssotest.staging.talkingquickly.co.uk, someapp.ssotest.staging.talkingquickly.co.uk, oauth.ssotest.staging.talkingquickly.co.uk etc and so my base domain would be .ssotest.staging.talkingquickly.co.uk.
ingress hosts: These should be set the subdomain you wish to deploy OAuth2 Proxy to
Setting the cookie_domain and whitelist_domain is important because by default, OAuth2 Proxy is configured to work only with the subdomain it is deployed on. So cookies will be specific to that subdomain and redirects will only be allowed to that subdomain.

The scope = "openid profile email" line is important because by default, OAuth2 Proxy will request a scope called api which does not exist in Keycloak which will result in a 403 Invalid Scopes erorr.

The set_authorization_header line ensures that the JWT is passed back to the NGinx ingress, this is important because it allows us to then pass this header back to the authenticating application so that it can access more information about the logged in user.

Finally the nginx.ingress.kubernetes.io/proxy-buffer-size: "16k" avoids an issue where the large headers which are often passed around with OAuth requests don't exceed the buffer size in NGinx which leads to errors along the lines of "Cookie "oauth2proxy" not present" and "upstream sent too big header while reading response header from upstream".


## Website achter OAuth plaatsen
