pythonjs demo server
===========================

This project shows you how to install the NodeJS package, and how it can be used to write a dynamic transpiling server.  First, git clone this repo, and then run: `npm install python-js`.  Now you are ready to run the server, run: `./run-demo.js` and then open your browser to `localhost:8080`.

run-demo.js is a small wrapper script that reads in server.py, translates it to javascript and eval's the code.  server.py is written using a subset of the Tornado Web API that PythonJS is able to translate into the NodeJS `http` API.

run-demo.js
-----------

```
	#!/usr/bin/env node
	var fs = require('fs')
	var pythonjs = require('python-js')
	var pycode = fs.readFileSync( './server.py', {'encoding':'utf8'} )
	var jscode = pythonjs.translator.to_javascript( pycode )
	eval( pythonjs.runtime.javascript + jscode )

```

server.py
-----------

server.py runs an http server on port 8080, when serving an html page it will parse the document, translate and replace python scripts it finds with the javascript output, and then serve the page to the client.  Each time the page is reloaded the server-side translator will be run again on the python code.  
Below is an example that calls into html DOM to change the page contents.

```
	<html>
	<head>
	<script src="pythonjs.js"></script>
	</head>
	<body>
	<button id="mybutton" onclick="test()">click me</button>

	<script type="text/python">

	a = 'hello'
	b = 'world'

	def test():
		con = document.createElement( 'div' )
		con.setAttribute('id', 'mydiv')
		document.body.appendChild(con)
		print( con )
		txt = document.createTextNode( a+b )
		con.appendChild(txt)

		print( con.getAttribute('id') )
		btn = document.getElementById('mybutton')
		print(btn)
		btn.firstChild.nodeValue = 'CLICKED!'

	</script>
	</body>
	</html>

```