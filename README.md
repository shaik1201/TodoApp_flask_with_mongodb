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
I will paste the html code in index.html to try to explain:
```html
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
what we are currently intersted in is:
```html
<form method="POST" action="{{ url_for('delete', id=todo['_id']) }}">
                <input type="submit" value="Delete Todo"
                    onclick="return confirm('Are you sure you want to delete this Todo?')">
            </form>
```
First, we need to know that when insert an object to the db it gets a unique id. If you uncomment the `<p>{{todo['_id']}}</p>` you will get the ids showing on the page.
We have the input tag with type 'submit' and the value showing on screen is 'Delete Todo'. The form's action tells us that when we press the button and submit the form we send a 'POST' request to the server, into the view function called `delete(id)` which gets  an id as a parameter with the same name as in the decorator. `<id>` is a variable placeholder. It captures a string at that position in the url and passes that to the view function. It is passed as a keyword with the same name as the placeholder, so the view function needs to accept an argument with the same name (or **kwargs).
After that, we can delete the specific todo with the sent id and redirect to home page.


