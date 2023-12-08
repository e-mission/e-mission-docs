# Running Behind NGINX Reverse Proxy

### Ramp up the body size limit

You must make sure that, NGINX can accept a very large request (i.e. 1GB).

So either add this to your `server` block or `http` block:

```nginx
client_max_body_size 1G;
```

Also, you must make sure that this configuration is conform with

`e-mission-server/net/api/cfc_webapp.py` file.

```python
BaseRequest.MEMFILE_MAX = 1024 * 1024 * 1024 # Allow the request size to be 1G
```
