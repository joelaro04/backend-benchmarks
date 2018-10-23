# WSGI Benchmark

## Flask + gunicorn
_benchmark_flask.py_
```python
from flask import Flask, jsonify
app = Flask(__name__)

@app.route('/hello')
def hello():
    return jsonify({'message': 'Hello world'})
```

_Benchmark Result 1:_
Gunicorn: ` gunicorn --workers=13 benchmark_flask:app`
Wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000/hello`

    Running 30s test @ http://localhost:8000/hello
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency    70.42ms   24.02ms 142.42ms   68.99%
        Req/Sec   207.60     86.33     1.07k    88.82%
      80702 requests in 30.10s, 13.70MB read
      Socket errors: connect 0, read 1797, write 0, timeout 0
    Requests/sec:   2680.72
    Transfer/sec:    465.98KB

_Benchmark Result 2:_
Gunicorn: `gunicorn --workers=13 --worker-class=meinheld.gmeinheld.MeinheldWorker benchmark_flask:app`
Wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000/hello`
Result:

    Running 30s test @ http://localhost:8000/hello
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     6.16ms    3.78ms  46.49ms   72.02%
        Req/Sec     2.38k   586.24     6.19k    71.25%
      923732 requests in 30.10s, 160.33MB read
    Requests/sec:  30690.36
    Transfer/sec:      5.33MB

---

## Falcon + gunicorn
_benchmark_falcon.py_
```python
import falcon

class HelloWorld:
    def on_get(self, req, resp):
        resp.media = {'message': 'Hello world'}

app = falcon.API()
app.add_route('/hello', HelloWorld())
```

_Benchmark Result 1:_
Gunicorn: `gunicorn --workers=13 benchmark_falcon:app`
Wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000/hello`
Result:

    Running 30s test @ http://localhost:8000/hello
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency    64.32ms   25.33ms 141.43ms   69.38%
        Req/Sec   213.85    108.14     1.35k    91.27%
      82679 requests in 30.09s, 15.22MB read
      Socket errors: connect 0, read 1167, write 2266, timeout 0
    Requests/sec:   2747.89
    Transfer/sec:    517.91KB

_Benchmark Result 2:_
Gunicorn: `gunicorn --workers=13 --worker-class=meinheld.gmeinheld.MeinheldWorker benchmark_falcon:app`
Wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000/hello`
Result:

    Running 30s test @ http://localhost:8000/hello
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     3.33ms    4.07ms  64.29ms   86.93%
        Req/Sec     5.31k     3.32k   18.80k    72.08%
      1990319 requests in 30.10s, 373.93MB read
      Socket errors: connect 0, read 0, write 5245, timeout 0
    Requests/sec:  66132.50
    Transfer/sec:     12.42MB