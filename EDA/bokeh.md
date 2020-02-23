
# Environment Variables
We can use YAML to set environment variables.  bokeh will look for a bokeh.yaml file in ```${HOME}/.bokeh/bokeh.yaml```.  The bokeh docs talk about environment variables in [this section](https://docs.bokeh.org/en/latest/docs/dev_guide/env_vars.html#devguide-envvars) of their docs.

Set environment variable BOKEH_DEV=yes  

see settings.py:
```  
def is_dev():
    return convert_bool(os.environ.get("BOKEH_DEV", False))  
```

See this SO for [setting environment variables in bash](https://askubuntu.com/questions/58814/how-do-i-add-environment-variables)

To get logging to work:

```
import logging

logging.basicConfig(logging.DEBUG)

INFO:tornado.access:200 GET /bkapp/autoload.js?bokeh-autoload-element=1002&bokeh-app-path=/bkapp&bokeh-absolute-url=http://localhost:5006/bkapp (::1) 433.80ms
INFO:tornado.access:101 GET /bkapp/ws?bokeh-protocol-version=1.0&bokeh-session-id=uE3QC8Ta3AOPWwGUVxi6PiHscC9gHHF4EONtBD1SMpyN (::1) 0.95ms
INFO:bokeh.server.views.ws:WebSocket connection opened
DEBUG:bokeh.server.views.ws:Receiver created for Protocol('1.0')
DEBUG:bokeh.server.views.ws:ProtocolHandler created for Protocol('1.0')
INFO:bokeh.server.views.ws:ServerConnection created
DEBUG:bokeh.server.session:Sending pull-doc-reply from session 'uE3QC8Ta3AOPWwGUVxi6PiHscC9gHHF4EONtBD1SMpyN'
DEBUG:bokeh.server.tornado:[pid 9875] 1 clients connected
DEBUG:bokeh.server.tornado:[pid 9875]   /bkapp has 2 sessions with 1 unused
DEBUG:bokeh.server.contexts:Scheduling 1 sessions to discard
DEBUG:bokeh.server.contexts:Discarding session 'o9nibaN3xW3wTFbgK2qYONZEtGXD4tBONpSHGza3gcbw' last in use 20616.93299999999 milliseconds ago
DEBUG:bokeh.document.document:Deleting 0 modules for <bokeh.document.document.Document object at 0x110978610>
```
Raspberry Pi:  
```
DEBUG:app_interactive:bk_worker TESTING!
INFO:werkzeug:192.168.86.24 - - [20/Feb/2020 04:53:51] "GET / HTTP/1.1" 200 -
DEBUG:bokeh.server.tornado:[pid 11652] 0 clients connected
DEBUG:bokeh.server.tornado:[pid 11652]   /bkapp has 0 sessions with 0 unused  
```

