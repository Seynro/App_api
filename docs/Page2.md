# CODE

## API

### Libraries used

```
numpy == 1.24.3
pandas == 2.0.2
scikit-learn == 1.2.2
fastapi == 0.96.0
uvicorn == 0.22.0
configparser == 5.3.0
python-dotenv == 1.0.0
cx-oracle == 8.3.0
pymysql == 1.0.3
```


### main.py

``` py title="Importing", linenums="1"
from typing import Optional
from fastapi import FastAPI
from pydantic import BaseModel
from datetime import datetime
from enum import Enum
from scoring_model import scoring_func
```


``` py title="Starting API", linenums="7"
app = FastAPI(
    title='Trading app'
)
```


``` py title="Defining parameters' types", linenums="10"
class Param(BaseModel):
    PIN_CODE: str
    TX_FID: int
    APP_ID: str
```

``` py title="Starting Scoring Model and Returning Status", linenums="14"
@app.post('/scoring')
def scoring(param: list[Param]):
    result = scoring_func(param)
    print(result)
    if result == 'APP_ID' or result == 'TX_FID' or result == 'PIN_CODE':
        error = result + ' is invalid'
        return {"status": 404, "score": error}
    else:
        return {"status": 200, "score": result}
```

``` py title="Whole Code", linenums="1"
from typing import Optional
from fastapi import FastAPI
from pydantic import BaseModel
from datetime import datetime
from enum import Enum
from scoring_model import scoring_func

app = FastAPI(
    title='Trading app'
)


class Param(BaseModel):
    PIN_CODE: str
    TX_FID: int
    APP_ID: str


@app.post('/scoring')
def scoring(param: list[Param]):
    result = scoring_func(param)
    print(result)
    if result == 'APP_ID' or result == 'TX_FID' or result == 'PIN_CODE':
        error = result + ' is invalid'
        return {"status": 404, "score": error}
    else:
        return {"status": 200, "score": result}
```

## Scoring Model

### scoring_model.py



``` py title="importing", linenums="1"
import numpy as np
import re
```

Check for Right `input` and `creating output`

``` py title="main_class", linenums="3"
class main_class:
    def __init__(self, param):
        """
        Function takes parameters (param) as JSON and transforms tham into str and int for each parameter
        PIN_CODE - str, TX_FID - int, APP_ID - str
        """
        self.param = str(param)
        
        PIN_CODE_match = re.search(r"PIN_CODE='(\d+)'", self.param)
        TX_FID_match = re.search(r"TX_FID=(\d+)", self.param)
        APP_ID_match = re.search(r"APP_ID='(\{[\w\d]+\})'", self.param)

        self.PIN_CODE = PIN_CODE_match.group(1) if PIN_CODE_match else None
        self.TX_FID = int(TX_FID_match.group(1)) if TX_FID_match else None
        self.APP_ID = APP_ID_match.group(1) if APP_ID_match else None

    def check(self):
        """
        Function checks if PIN_CODE's length is 7, TX_FID's length is 11, APP_ID's length is 25 
        and it starts and ends with {}. It returns True and None if everything is right and False 
        and the name of parameter if not
        """
        if (len(self.PIN_CODE) != 7) or (not isinstance(self.PIN_CODE, str)):
            return (False, 'PIN_CODE')
        
        if  (not isinstance(self.TX_FID, int)) or (len(str(self.TX_FID)) != 10):
            return (False, 'TX_FID')
        
        if (not isinstance(self.APP_ID, str)) or (self.APP_ID is None) or (len(self.APP_ID) != 25) or (self.APP_ID[0] != "{") or (self.APP_ID[-1] != "}"):
            return (False, 'APP_ID')
        
        return (True, None)
    
    def random(self):
        """
        Function creates random number and returns it
        """
        num = np.random.randint(1, 101)
        return num
```

Function takes parameters (param) as `JSON` and transforms tham into `str` and `int` for each parameter
`PIN_CODE - str`, `TX_FID - int`, `APP_ID - str`

``` py title="transforming", linenums="1"
def __init__(self, param):
        self.param = str(param)
        
        PIN_CODE_match = re.search(r"PIN_CODE='(\d+)'", self.param)
        TX_FID_match = re.search(r"TX_FID=(\d+)", self.param)
        APP_ID_match = re.search(r"APP_ID='(\{[\w\d]+\})'", self.param)

        self.PIN_CODE = PIN_CODE_match.group(1) if PIN_CODE_match else None
        self.TX_FID = int(TX_FID_match.group(1)) if TX_FID_match else None
        self.APP_ID = APP_ID_match.group(1) if APP_ID_match else None
```


Function checks if `PIN_CODE` length is 7, `TX_FID` length is 11, `APP_ID` length is 25 
and it starts and ends with `{}`. It returns `True` and `None` if everything is right and `False` 
and the `name of parameter` if not


``` py title="check", linenums="1"
def check(self):
        if (len(self.PIN_CODE) != 7) or (not isinstance(self.PIN_CODE, str)):
            return (False, 'PIN_CODE')
        
        if  (not isinstance(self.TX_FID, int)) or (len(str(self.TX_FID)) != 10):
            return (False, 'TX_FID')
        
        if (not isinstance(self.APP_ID, str)) or (self.APP_ID is None) or (len(self.APP_ID) != 25) or (self.APP_ID[0] != "{") or (self.APP_ID[-1] != "}"):
            return (False, 'APP_ID')
        
        return (True, None)
```

Function creates random `number` and returns it

``` py title="Randomizer", linenums="1"
def random(self):
        num = np.random.randint(1, 101)
        return num
```