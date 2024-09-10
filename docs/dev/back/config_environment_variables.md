# Configuring Environment Variables

## 1. List of Environment Variables

#### DB, WEBSERVER, PUSH Config
Use this [search](https://github.com/search?q=repo%3Ae-mission%2Fe-mission-server%20config.get&type=code) to obtain usage of `DB, WEBSERVER, PUSH` environment variables.

#### AWS Cognito Credentials
Use this [search](https://github.com/search?q=repo%3Ae-mission%2Fop-admin-dashboard+%2F.getenv%5C%28%5C%22%5BA-Z%5D%2F&type=code) to obtain usage of COGNITO variables in admin-dashboard repository.


#### Overpass, Nominatim

Use this [search](https://github.com/search?q=repo%3Ae-mission%2Fe-mission-server+%2F.environ.get%5C%28%5C%22%5BA-Z%5D%2F&type=code) to obtain usage of `Overpass, Nominatim, Geofabrik` environment variables.


These variables are set using GitHub secrets in the e-mission-server repository: GEOFABRIK_API, GEOFABRIK_OVERPASS_KEY.


## 2. Table of Environment Variables
The table lists the environment variables to be added to the deployments of the respective repositories.

Environment variables with a 'Default Value' already have a value set in a Dockerfile, a docker-compose.yml file, a GitHub workflow file, or directly in the code.


| S. No. | Reposistory | Environment Variable | Default Value | Source |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| 1. | e-mission-server, op-admin-dashboard, em-public-dashboard | DB_HOST | db | conf/storage/db.conf | 
| 2. | e-mission-server, op-admin-dashboard, em-public-dashboard | DB_RESULT_LIMIT | --- | conf/storage/db.conf |
| 3. | e-mission-server, op-admin-dashboard, em-public-dashboard | WEB_SERVER_HOST | 0.0.0.0 | conf/net/api/webserver.conf | 
| 4. | e-mission-server, op-admin-dashboard, em-public-dashboard | WEBSERVER_PORT | --- | conf/net/api/webserver.conf | 
| 5. | e-mission-server, op-admin-dashboard, em-public-dashboard | WEBSERVER_TIMEOUT | --- | conf/net/api/webserver.conf | 
| 6. | e-mission-server, op-admin-dashboard, em-public-dashboard | WEBSERVER_NOT_FOUND_REDIRECT | --- | conf/net/api/webserver.conf | 
| 7. | e-mission-server, op-admin-dashboard, em-public-dashboard | WEBSERVER_AUTH | --- | conf/net/api/webserver.conf |  
| 8. | e-mission-server, op-admin-dashboard, em-public-dashboard | WEBSERVER_AGGREGATE_CALL_AUTH | --- | conf/net/api/webserver.conf |  
| 9. | e-mission-server, op-admin-dashboard | PUSH_PROVIDER | --- | conf/net/ext_service/push.json | 
| 10. | e-mission-server, op-admin-dashboard | PUSH_SERVER_AUTH_TOKEN | --- | conf/net/ext_service/push.json | 
| 11. | e-mission-server, op-admin-dashboard | PUSH_APP_PACKAGE_NAME | --- | conf/net/ext_service/push.json | 
| 12. | e-mission-server, op-admin-dashboard | PUSH_IOS_TOKEN_FORMAT | --- | conf/net/ext_service/push.json | 
| 13. | op-admin-dashboard | COGNITO_CLIENT_ID | --- | AWS Cognito | 
| 14. | op-admin-dashboard | COGNITO_CLIENT_SECRET | --- | AWS Cognito |  
| 15. | op-admin-dashboard | COGNITO_REDIRECT_URL | --- | AWS Cognito | 
| 16. | op-admin-dashboard | COGNITO_TOKEN_ENDPOINT | --- | AWS Cognito | 
| 17. | op-admin-dashboard | COGNITO_USER_POOL_ID | --- | AWS Cognito | 
| 18. | op-admin-dashboard | COGNITO_REGION | --- | AWS Cognito | 
| 19. | op-admin-dashboard | COGNITO_AUTH_URL | --- | AWS Cognito | 
| 20. | e-mission-server | GFBK_KEY | --- | .github/workflows/nominatim-docker-test.yml | 
| 21. | e-mission-server | GEOFABRIK_OVERPASS_KEY | --- | .github/workflows/test-overpass.yml |
| 22. | e-mission-server | NOMINATIM_QUERY_URL |  --- | emission/individual_tests/TestNominatim.py |
| 23. | e-mission-server | OPENSTREETMAP_QUERY_URL | https://nominatim.openstreetmap.org | emission/integrationTests/docker-compose.yml |
| 24. | e-mission-server | GEOFABRIK_QUERY_URL |  https://geocoding.geofabrik.de/$GFBK_KEY | emission/integrationTests/docker-compose.yml |
| 25. | e-mission-server | NOMINATIM_CONTAINER_URL | http://rhodeisland-nominatim:8080 | emission/integrationTests/docker-compose.yml | 

