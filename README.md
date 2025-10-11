Here’s a clean **README.md** you can use to document the information about `ProxyRequests On/Off` and Apache reverse proxy setup:

````markdown
# Apache ProxyRequests Guide

This document explains the difference between `ProxyRequests On` and `ProxyRequests Off` in Apache, their use cases, and associated security implications. It also covers the recommended configuration for reverse proxying a Django + Gunicorn application.

---

## 1. ProxyRequests Directive

The `ProxyRequests` directive controls **forward proxying** in Apache.

- **`ProxyRequests On`**  
  Apache acts as a **forward proxy** and can route requests for any client to any destination.  
- **`ProxyRequests Off`**  
  Apache **does not act as a forward proxy**. Only requests explicitly defined by `ProxyPass` are forwarded.  
  This is recommended for reverse proxy setups.

---

## 2. Example: `ProxyRequests On` (Dangerous)

```apache
<VirtualHost *:80>
    ServerName myserver.example.com

    ProxyRequests On
    <Proxy *>
        Require all granted
    </Proxy>
</VirtualHost>
````

### Risks of `ProxyRequests On`:

1. Anyone on the internet can use your server to route traffic.
2. Hackers can hide their identity behind your server.
3. Your server can be used for spam, attacks, or high traffic load.
4. Your IP can get blacklisted.

**Example misuse:**

```bash
curl -x http://myserver.example.com:80 http://targetsite.com/
```

* The request to `targetsite.com` appears to come from your server.

---

## 3. Recommended: `ProxyRequests Off` (Safe)

For reverse proxying (e.g., Django + Gunicorn):

```apache
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName masterbe.dev.metaphi.in

    ProxyRequests Off
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/masterbe.dev.metaphi.in/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/masterbe.dev.metaphi.in/privkey.pem

    ErrorLog ${APACHE_LOG_DIR}/masterbe.dev.metaphi.in_error.log
    CustomLog ${APACHE_LOG_DIR}/masterbe.dev.metaphi.in_access.log combined
</VirtualHost>
</IfModule>
```

* `ProxyRequests Off` ensures Apache only forwards requests defined by `ProxyPass`.
* `ProxyPreserveHost On` preserves the original host header for Django/Gunicorn.
* `SSLEngine on` enables HTTPS for secure traffic.

---

## 4. Summary

| Directive           | Behavior                                         | Use Case                                       |
| ------------------- | ------------------------------------------------ | ---------------------------------------------- |
| `ProxyRequests On`  | Apache acts as a forward proxy                   | Rare, only for controlled networks             |
| `ProxyRequests Off` | Apache only forwards explicitly defined requests | Safe for reverse proxy (Django, Node.js, etc.) |

**Recommendation:** Always use `ProxyRequests Off` for reverse proxy setups to avoid accidental exposure as an open proxy.

---

## 5. Additional Tips

* Enable SSL (`SSLEngine on`) for secure communication.
* Always add the Django host to `ALLOWED_HOSTS`.
* Ensure Apache listens on both port 80 (redirect HTTP → HTTPS) and 443 (SSL).
* Restrict forward proxy access if you must use `ProxyRequests On`:

```apache
<Proxy "*">
    Require ip 192.168.1.0/24
</Proxy>
```

---

**References:**

* [Apache mod_proxy documentation](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html)
* [Django deployment checklist](https://docs.djangoproject.com/en/stable/howto/deployment/checklist/)

```

---

If you want, I can also **create a simplified diagram version** of this README showing **HTTP → HTTPS → Gunicorn/Django flow** with the difference between forward and reverse proxy. It makes it very visual for a team README.  

Do you want me to do that?
```
