# Keycloak

## Where do I find the most important settings?

Go to Realm setting > General > OpenID Endpoint Configuration

## Authentication with oauth2

One of the problems I had when setting this up is the missing email for the user. When using keycloak with OAuth2 there needs to be an email adress set ine each user profile otherwise you might get:

> Error creating session during OAuth2 callback: id_token did not contain an email and profileURL is not defined

Sources:

- <https://www.talkingquickly.co.uk/webapp-authentication-keycloak-OAuth2-proxy-nginx-ingress-kubernetes>
