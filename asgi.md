# ASGI Benchmark

## [uvicorn](https://github.com/encode/uvicorn)
_uvicorn_sample.py_
```python
class App():
    def __init__(self, scope):
        self.scope = scope

    async def __call__(self, receive, send):
        await send({
            'type': 'http.response.start',
            'status': 200,
            'headers': [
                [b'content-type', b'text/plain'],
            ],
        })
        await send({
            'type': 'http.response.body',
            'body': b'Hello, world!',
        })
```

_Benchmark Result 1:_
Gunicorn: ` uvicorn uvicorn_sample:App`
Wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000/hello`

    Running 30s test @ http://localhost:8000
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency   846.71us  259.88us   8.62ms   69.52%
        Req/Sec     5.23k     1.82k    7.48k    56.31%
      313155 requests in 30.10s, 44.80MB read
      Socket errors: connect 186, read 0, write 10, timeout 0
    Requests/sec:  10405.02
    Transfer/sec:      1.49MB

_Benchmark Result 2:_
uvicorn: `gunicorn3 -w 13 -k uvicorn.workers.UvicornWorker uvicorn_sample:App`
Wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000/hello`
Result:

    Running 30s test @ http://localhost:8000
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency   488.39us    1.19ms  49.22ms   97.97%
        Req/Sec     9.86k     0.88k   15.21k    67.17%
      588997 requests in 30.06s, 84.26MB read
      Socket errors: connect 185, read 0, write 11, timeout 0
    Requests/sec:  19594.34
    Transfer/sec:      2.80MB

---

## [Starlette](https://www.starlette.io/)
_starlette_sample.py_
```python
from starlette.responses import PlainTextResponse

class App:
    def __init__(self, scope):
        self.scope = scope

    async def __call__(self, receive, send):
        response = PlainTextResponse('Hello, world!')
        await response(receive, send)
```

_Benchmark Result 1:_
Uvicorn: `uvicorn starlette_sample:App`
wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000`
Result:

    Running 30s test @ http://localhost:8000
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency   146.75us   10.71us 413.00us   90.83%
        Req/Sec     6.40k   112.69     6.55k    80.73%
      191731 requests in 30.10s, 26.88MB read
      Socket errors: connect 194, read 0, write 3, timeout 0
    Requests/sec:   6370.67
    Transfer/sec:      0.89MB
    
_Benchmark Result 2:_
Uvicorn: `gunicorn3 -w 13 -k uvicorn.workers.UvicornWorker starlette_sample:App`
wrk: `wrk -c 200 -t 13 -d 30s http://localhost:8000`
Result:

    Running 30s test @ http://localhost:8000
      13 threads and 200 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency   203.14us   80.93us  10.79ms   97.11%
        Req/Sec    14.01k   233.50    14.46k    74.42%
      419673 requests in 30.10s, 58.83MB read
      Socket errors: connect 192, read 0, write 4, timeout 0
    Requests/sec:  13944.51
    Transfer/sec:      1.95MB