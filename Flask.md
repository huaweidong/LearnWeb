# Flask

## 1. 安装

### virtualenv

virtualenv 为每个不同项目提供一份 Python 安装。它并没有真正安装多个 Python 副本，但是它确实提供了一种巧妙的方式来让各项目环境保持独立。

```sh
$ easy_install virtualenv
#或者
$ pip install virtualenv
#或者
$ apt-get install python-virtualenv
```

```sh
$ mkdir myproject
$ cd myproject
$ virtualenv venv	// 创建环境
New python executable in venv/bin/python
Installing distribute ...... done.
```

```sh
$ . venv/bin/activate	// 激活环境
```

在激活的venv环境中安装Flask

```sh
pip install Flask
```

全局环境安装Flask也可以，但不推荐。

获取Flask的开发版本

```sh
$ mkdir flask
$ cd flask
$ virtualenv venv --distribute
$ . venv/bin/activate
New python executable in venv/bin/python
Installing distribute............done.
$ pip install Flask==dev
...
Finished processing dependencies for Flask==dev
```

## 2. 快速入门

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

```sh
$ python hello.py
* Running on http://127.0.0.1:5000/
```

使该网站可以外部访问

```python
app.run(host='0.0.0.0')
```

调试模式：如果你启用了调试支持，服务器会在代码修改后自动重新载入

```python
app.debug = True
app.run()
# 或者
app.run(debug = True)
```

路由： `route()`装饰器把一个函数绑定到对应的 URL 上。

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello World'
```

你可以构造含有动态部分的 URL，也可以在一个函数上附着多个规则。

要给 URL 添加变量部分，你可以把这些特殊的字段标记为 <variable_name> ， 这个部分将会作为命名参数传递到你的函数。规则可以用 <converter:variable_name> 指定一个可选的转换器。

```python
@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return 'User %s' % username

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return 'Post %d' % post_id
```

在遗忘尾斜线时，允许关联的 URL 接任工作

```python
@app.route('/projects/')	#访问projects也可以成功
def projects():
    return 'The project page'

@app.route('/about')	#访问about/则会失败
def about():
    return 'The about page'
```

可以用 [url_for()](http://docs.jinkan.org/docs/flask/api.html#flask.url_for)来给指定的函数构造 URL。它接受函数名作为第一个参数，也接受对应 URL 规则的变量部分的命名参数。

```python
>>> @app.route('/')
... def index(): pass
...
>>> @app.route('/login')
... def login(): pass
...
>>> @app.route('/user/<username>')
... def profile(username): pass
...
>>> with app.test_request_context():
...  print url_for('index')
...  print url_for('login')
...  print url_for('login', next='/')
...  print url_for('profile', username='John Doe')
...
/
/login
/login?next=/
/user/John%20Doe
```

默认情况下，路由只回应 GET 请求，但是通过 [route()](http://docs.jinkan.org/docs/flask/api.html#flask.Flask.route) 装饰器传递 methods 参数可以改变这个行为。

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        do_the_login()
    else:
        show_the_login_form()
```

静态文件：只要在你的包中或是模块的所在目录中创建一个名为 static 的文件夹，在应用中使用 /static 即可访问。给静态文件生成 URL ，使用特殊的 'static' 端点名:

```
url_for('static', filename='style.css')
```

这个文件应该存储在文件系统上的 static/style.css 。

## 3. 模板渲染

Flask 配备了 [Jinja2](http://jinja.pocoo.org/) 模板引擎。

你可以使用 [render_template()](http://docs.jinkan.org/docs/flask/api.html#flask.render_template) 方法来渲染模板。你需要做的一切就是将模板名和你想作为关键字的参数传入模板的变量。

```
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

Flask 会在 templates 文件夹里寻找模板。如果你的应用是个模块，这个文件夹应该与模块同级；如果它是一个包，那么这个文件夹作为包的子目录。

```html
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}
```

**模板继承**能使特定元素 （比如页眉、导航栏和页脚）可以出现在所有的页面。

### 访问Request数据

全局的 [request](http://docs.jinkan.org/docs/flask/api.html#flask.request) 对象来提供客户端发送给服务器的数据。

#### 环境局部变量

Flask 中的某些对象是全局对象，但却不是通常的那种。这些对象实际上是特定环境的局部对象的代理。

#### Request对象

```python
from flask import request
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                      request.form['password']):
            return log_the_user_in(request.form['username'])
```

访问表单数据 request.form

获取get参数 request.args.get('q', '')

#### 文件上传

表单属性添加`enctype="multipart/form-data"`

request.files获取上传的文件

secure_filename()用户用户设置的真实文件名

```python
from flask import request
from werkzeug import secure_filename

@app.route('/upload', method=['GET', 'POST'])
def upload_file():
	if request.files['the_file']
	f.save('/var/www/uploads/' + secure_filename(f.filename))
```

#### cookies

cookies

#### 跳转和错误

```python
redirect(url_For('login')) 跳转
abort(401)	终止
render_template('page_not_fount.html'), 404
```

#### 会话

session

#### 消息闪烁

flash()

get_flashed_messages()

#### 日志

app.logger.debug('')

app.logger.warning('')

app.logger.error('')

#### WSGI中间件集成

## 4. 示例

## 5. 模板

Flask使用Jinja2作为默认模版，其存在以下全局变量：

* config
* request
* session
* g
* url_for()
* get_flashed_messages()

这些变量属于Jinja的上下文变量,而不是普通的全局变量。它们的区别是上下文变量 在导入的模版中默认是不可见的。



{% from '__helpers.html' import my_macro with context %} 导入自定义宏

{{ user.username|tojson|safe}}	tojson过滤器，用于将对象转为json格式

三种禁用自动转义的方法

1. 在Python中转义。先在Markup对象中转义，然后将它传送给模板

2. 在模板文件中转义，通过|safe过滤器表示字符串是安全的

3. 暂时禁用全局的自动转义

   ```html
   {% autoescaping false %}
   	<p>autoescaping is disabled here
   {% endautosecaping %}
   ```

引入自定义的过滤器的两种方法

1. 使用jinja_env对象

   ```python
   @app.template_filter('reverse')
   def reverse_filter(s):
   	return s[::-1]
   ```

2. 使用template_filter()装饰器

   ```python
   def reverse_filter(s):
       return s[::-1]
   app.jinja_env.filters['reverse'] = reverse_filter
   ```

## 7. 日志

## 8. 配置Config

## 9. Signals



