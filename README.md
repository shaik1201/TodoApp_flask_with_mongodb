# Todo_app_flask

A simple Todo list app using flask on the backend.

Explainations to some of the important things:

## 1.
client = MongoClient('localhost', 27017)

db = client.flask_db
todos = db.todos
