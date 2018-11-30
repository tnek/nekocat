The application is a twitter clone running on a flask clone based around werkzeug. The application's a twitter clone where users can share links and the site will fetch a preview of the site.

Werkzeug by default uses pickle as it's serialization method for SecureCookies: [https://github.com/pallets/werkzeug/issues/60](https://github.com/pallets/werkzeug/issues/60).

The application uses SecureCookies for it's session cookie, so this is unserialized per-request. 

```
SECRET_KEY = os.environ.get("FLAGON_SECRET_KEY", "")

...


class Request(BaseRequest):
    @cached_property
    def session(self):
        data = self.cookies.get("session_data")
        if not data:
            return SecureCookie(secret_key=SECRET_KEY)
        return SecureCookie.unserialize(data, SECRET_KEY)
```


The secret key is defined as an environment variable, and there's the following hardcoded route in the challenge in `dist/flagon/flagonpy`:

```
def flagoninfo(request):
    if request.remote_addr != "127.0.0.1":
        return render_template("404.html")

    info = {
        "system": " ".join(os.uname()),
        "env": str(os.environ)
    }

    return render_template("flaginfo.html", info_dict=info)

...


flaginfo_route = "/flaginfo"
self.routes[flaginfo_route] = flagoninfo 
```

The first stage of the challenge is to get this secret key by getting a request that appears to come from `127.0.0.1` to the `/flaginfo` route.


