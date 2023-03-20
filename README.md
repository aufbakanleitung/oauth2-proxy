# OAuth2-proxy

zie vooral: `https://oauth2-proxy.github.io/oauth2-proxy/docs/configuration/oauth_provider/#keycloak-oidc-auth-provider`

zie vooral: `https://kubernetes.github.io/ingress-nginx/examples/auth/oauth-external-auth/`

## Keycloak OIDC Auth Provider configureren

Create new client in your Keycloak realm with Access Type 'confidental', Client protocol 'openid-connect' and Valid Redirect URIs 'https://keycloak.sitesandsuch.nl/oauth2/callback'

Take note of the Secret in the credential tab of the client

Create a mapper with Mapper Type 'Group Membership' and Token Claim Name 'groups'.

Create a mapper with Mapper Type 'Audience' and Included Client Audience and Included Custom Audience set to your client name.

Make sure you set the following to the appropriate url:

Client ID: `oauth2-proxy-external-domain`

Valid Redirect URLs: `https://oauth.sitesandsuch.nl/oauth2/callback`

Zie hiervoor: `https://oauth2-proxy.github.io/oauth2-proxy/docs/configuration/oauth_provider/#keycloak-oidc-auth-provider`

## Oauth-proxy installeren

De installatie bestaat uit 2 delen:
1. De deployment: `kubectl apply -f oauth2-proxy-external-domain.yaml`

2. De ingress: `kubectl apply -f ingress-external-domain.yaml`

Verklaring argument in Deployment

zie: `https://oauth2-proxy.github.io/oauth2-proxy/docs/configuration/overview#command-line-options`

## Website achter OAuth plaatsen

Het is genoeg om annotaties in de Ingress resource op te nemen:

Zie hiervoor de ingress-templates.
