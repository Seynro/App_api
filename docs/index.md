# FastAPI

API for Scoring Model


## STARTING

HTTP = http://127.0.0.1:8000/scoring


### POST

INPUT:

```js
[
  {
    "PIN_CODE": "STR 7",
    "TX_FID": INT 11,
    "APP_ID": "{STR 25}"
  }
]
```

OUTPUT:

```JS
{
    "status": STATUS,
    "score": INT
}
```