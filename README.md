# Flask API

## CREATE APP
### Setup Flask
*app.py*

```py {id="python-print" class="blue large" data-filename="app.py"}
from flask import Flask

app = Flask(__name__)

if __name__ == "__main__":
    app.run()

```

### Setup config
*app.py*

```py
app.config['PROPAGATE_EXCEPTIONS'] = True
app.config['API_TITLE'] = "Stores REST API"
app.config['API_VERSION'] = "v1"
app.config['OPENAPI_VERSION'] = "3.0.3"
app.config['OPENAPI_URL_PREFIX'] = "/"
app.config["OPENAPI_SWAGGER_UI_PATH"] = "/swagger-ui"
app.config["OPENAPI_SWAGGER_UI_URL"] = "https://cdn.jsdelivr.net/npm/swagger-ui-dist/"
```

### Setup API with Flask SMORest

*app.py*
```py
from flask_smorest import Api

api = Api(app)
```

## Create Blueprint(s) and Methodview(s):

*resources/< blueprint >.py*

```py
from flask.views import MethodView
from flask_smorest import Blueprint, abort

blp = Blueprint("blp name", __name__, description="descriptive term")
```

- Blueprints will contain the route(s) = <blp>.route("/")
- Each route will contain a class with a methodview
- The methodview functions will correspond to get, post, delete etc...

*resources/< blueprint >.py*

```py
@blp.route("/")
class ClassName(MethodView):

    def get(self):
        pass

    def post(self):
        pass
```

## Create schema with Marshmallow

*schema.py*

```py
from marshmallow import Schema, fields

class <SchemaOneName>(Schema):
    id = fields.Str(dump_only=True)
    name = fields.Str(required=True)
    price = fields.Float(required=True)

class <SchemaTwoName>(Schema):
    id = fields.Str(dump_only=True)
    person = fields.Str(required=True)
    age = fields.Float(required=True)
```

## Validation and Response
### Import the schema and use it to validate the data 

- @blp.arguments(< schema >)

To validate incoming (post or put) data - i.e. validating data that the client (postman) sends to us

*resources/< blueprint >.py*

```py
from schemas import StoreSchema

@blp.route("/")
class ClassName(MethodView):

    def get(self):
        pass

    @blp.arguments(StoreSchema)
    def post(self):
        pass
```

### Send response decorator 

- @blp.response(200, < schema >)

*resources/< blueprint >.py*

```py
@blp.route("/")
class ClassName(MethodView):

    @blp.response(200, StoreSchema(many=True))
    def get(self):
        pass

    @blp.response(200, StoreSchema)
    @blp.arguments(StoreSchema)
    def post(self):
        pass
```