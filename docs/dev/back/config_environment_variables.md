# Configuring Environment Variables

## 1. List of Environment Variables

### DB, WEBSERVER, PUSH Config
Use this [search](https://github.com/search?q=repo%3Ae-mission%2Fe-mission-server%20config.get&type=code) to obtain usage of `DB, WEBSERVER, PUSH` environment variables.

#### Environment variables:
DB config: <br>DB_HOST, DB_RESULT_LIMIT<br><br>
WEBSERVER config: <br>WEB_SERVER_HOST, WEBSERVER_PORT, WEBSERVER_TIMEOUT, WEBSERVER_NOT_FOUND_REDIRECT, WEBSERVER_AUTH, WEBSERVER_AGGREGATE_CALL_AUTH<br><br>
PUSH config: <br>PUSH_PROVIDER, PUSH_SERVER_AUTH_TOKEN, PUSH_APP_PACKAGE_NAME, PUSH_IOS_TOKEN_FORMAT<br><br>

### AWS Cognito Credentials

#### Environment variables: 
COGNITO_CLIENT_ID, COGNITO_CLIENT_SECRET, COGNITO_REDIRECT_URL, COGNITO_TOKEN_ENDPOINT, COGNITO_USER_POOL_ID, COGNITO_REGION, COGNITO_AUTH_URL

Use this [search](https://github.com/search?q=repo%3Ae-mission%2Fop-admin-dashboard+%2F.getenv%5C%28%5C%22%5BA-Z%5D%2F&type=code) to obtain usage of COGNITO variables in admin-dashboard repository.

### Overpass, Nominatim

#### Environment variables: 
GFBK_KEY, GEOFABRIK_OVERPASS_KEY, NOMINATIM_QUERY_URL, OPENSTREETMAP_QUERY_URL, GEOFABRIK_QUERY_URL, NOMINATIM_CONTAINER_URL

Use this [search](https://github.com/search?q=repo%3Ae-mission%2Fe-mission-server+%2F.environ.get%5C%28%5C%22%5BA-Z%5D%2F&type=code) to obtain usage of `Overpass, Nominatim, Geofabrik` environment variables.

This [search](https://github.com/search?q=repo%3Ae-mission%2Fe-mission-server+geofabrik&type=code) shows that some variables are set using GitHub secrets in the e-mission-server GitHub workflows: GEOFABRIK_API, GEOFABRIK_OVERPASS_KEY.
