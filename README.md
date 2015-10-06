# Openshift DIY Cartridge for Crossbar #

This repository is supposed to be a base template for a openshift's cartridge to run a crossbar instance.

    $ rhc app create --from-code https://

## What it does ##

- Provides `build` hook, to grab a specific python version, compile and install it locally at `$OPENSHIFT_DATA_DIR`.

- Use `pip` to install dependencies specified in `requirements.txt`.

- Provides `start`/`stop` scripts that dynamically creates crossbar configuration from a template config.

The DIY cartridges provides full control of what is being ofered for Openshift proxy.  Application setup is done through "applicatio hooks", see [diy cartridge][1] documentation for more information.


## The config template ##

During `start` script, it will look for a `config.(json|yaml)` file and every line will be evaluated by the shell so variables can be properly expanded.  Then it will be saved in the CBDIR (`.crossbar`) to be used by the crossbar instance.


## Caveats ##

When connecting to the router, use `:8000`.

This is because openshift routing use [proxies][2] to your application's local port.  The thing is, for websockets this routing is [different from http proxy][3], so when connecting to the wamp router, use port `8000`.

Also provide a single serializer (e.g. `autobahn.serializer.JsonSerializer`).  If you don't provide a single serializer, during the websocket handshake the client will expect a single protocol in the server response header `sec-websocket-protocol`, but openshift keeps returning a comma-separated list.

Example (Autobahn|Python):

```python
ApplicationRunner(url=u'ws://<app>-<domain>.rhcloud.com:8000',
                  realm=u'realm1',
                  serializers=[JsonSerializer()])
```


 [1]: https://docs.openshift.org/origin-m4/oo_cartridge_guide.html#diy
 [2]: https://developers.openshift.com/en/managing-port-binding-routing.html
 [3]: https://blog.openshift.com/paas-websockets/
 [4]: https://blog.openshift.com/paas-websockets/
