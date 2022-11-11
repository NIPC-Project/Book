# Python启动HTTP服务器

开发板 `server.py`

```py
from http.server import HTTPServer, BaseHTTPRequestHandler
import json

image_data = [1,2,3]

class Resquest(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "application/json")
        self.end_headers()
        dict={"image_data": image_data}
        json_text = json.dumps(dict)
        self.wfile.write(json_text.encode())

if __name__ == '__main__':
    host = ('0.0.0.0', 8888)
    server = HTTPServer(host, Resquest)
    server.serve_forever()
```

本机 `client.py`

```py
import requests
import json

url = "http://192.168.10.68:8888"
response = requests.get(url)
print(response.text)
response_dict = json.loads(response.text)
print(response_dict["image_data"])
```
