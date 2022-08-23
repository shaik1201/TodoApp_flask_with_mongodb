# Todo_app_flask

A simple Todo list app using flask on the backend.

Explainations to some of the important things:

## 1.
```python
client = MongoClient('localhost', 27017)
db = client.flask_db
todos = db.todos
```
`client` is an object and we pass it localhost for the local server started on our pc, and we pass a port number. (27017 uses for mongodb).
in `db = client.flask_db` we created a database to hold our data.
`todos = db.todos` create a collection called todos in our db.<br>
**_NOTE:_**  The database will only be created when we insert some info in it, so check in mongo shell for 'show dbs' won't show this db yet.

## 2. Add a todo:
```python
@app.route('/', methods=['POST','GET'])
def index():
    if request.method == 'POST':
        content = request.form['content']
        degree = request.form['degree']
        todos.insert_one({'content':content, 'degree':degree})
        return redirect(url_for('index'))


    all_todos = todos.find()
    return render_template('index.html', todos=all_todos, title='TODO')
```
Explanation on [request](https://flask.palletsprojects.com/en/2.2.x/reqcontext/) and some more explanations and examples [here](https://stackoverflow.com/questions/10434599/get-the-data-received-in-a-flask-request).

On `content = request.form['content']` and `degree = request.form['degree']` we access the form posted in the index.html page. We access through the
'name' attribute. e.g: `<input type="text" name="content" placeholder="Todo Content"></input>`.
After that we insert the object to database.
`return redirect(url_for('index'))` uses for redirect us to the home page after we add the todo in our db. (we want to reload the page and show the todo). the `url_for('index')` takes the view function name called index().
If the method is a GET request, we want to show all of todos we entered so far:
`all_todos = todos.find()` takes all the todos and `return render_template('index.html', todos=all_todos, title='TODO')` sends them to the
webpage where we can use them in the html page using [jinja2](https://jinja.palletsprojects.com/en/3.1.x/).
``` html
{% for todo in todos %}
        <!-- <p>{{todo['_id']}}</p> this to show that every item in the db gets an id -->
        <div class="todo">
            <p>{{ todo['content'] }} <i>({{ todo['degree']}})</i></p>

            <form method="POST" action="{{ url_for('delete', id=todo['_id']) }}">
                <input type="submit" value="Delete Todo"
                    onclick="return confirm('Are you sure you want to delete this Todo?')">
            </form>
        </div>
        {% endfor %}
```

## 3. Delete a todo:
```python
@app.post('/<id>/delete/')
def delete(id): # id must be the same name as in the decorator
    todos.delete_one({"_id": ObjectId(id)})
    return redirect(url_for('index'))
```


