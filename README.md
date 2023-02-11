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

`helm install oauth2-proxy oauth2-proxy/oauth2-proxy -n oauth2-proxy -f oauth2-proxy/values-oauth2-proxy.yaml`


## Website achter OAuth plaatsen
