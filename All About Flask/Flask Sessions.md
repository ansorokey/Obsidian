## What is a session in flask?
A flask session is an object that allows us to store data between requests. In order to use a session, a secret key must be in use. Works similarly to browser local/session storage, and behaves like a dictionary.

## How to set up:
All we need to do is import. The session persists data between requests/page navigation.

## Methods and properties
### Access data in a session:
`session.get('key')`
`session.get('views')`

### Add data to session:
`session['key'] = val`
`session['views'] = 12`

### Delete data from session
`session.pop('key', 'return value if not popped (default to None)')`
`session.pop('views', None)`

```py
# import from flask
from flask import Flask, session

# name the app after the file name
app = Flask(__name__)

# define a route
@app.route('/visits-counter/')
def visits():
    if 'visits' in session:
        # reading and updating session data
        session['visits'] = session.get('visits') + 1
    else:
        # setting session data
		# Assignment, no setter method
        session['visits'] = 1
    return "Total visits: {}".format(session.get('visits'))

@app.route('/delete-visits/', methods=["POST"])
def delete_visits():
	# delete visits
	# The second argument is the return value if the key is NOT found in the dictionary
    session.pop('visits', None)
    return 'Visits deleted'
```