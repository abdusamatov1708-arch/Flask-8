# Flask-8
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <title>Dashboard</title>
</head>
<body>
    <h1>Dashboard sahifasi</h1>
    <p>Xush kelibsiz, <strong>{{ username }}</strong>! Siz tizimga muvaffaqiyatli kirdingiz.</p>

    <hr>
    <a href="{{ url_for('logout') }}"><button>Chiqish (Logout)</button></a>
</body>
</html>
</body>
</html>
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <title>Tizimga kirish</title>
</head>
<body>
    <h2>Tizimga kirish</h2>

    {% if error %}
        <p style="color: red;"><strong>{{ error }}</strong></p>
    {% endif %}

    <form method="POST" action="{{ url_for('login') }}">
        <label>Username:</label><br>
        <input type="text" name="username" required><br><br>

        <label>Parol:</label><br>
        <input type="password" name="password" required><br><br>

        <button type="submit">Kirish</button>
    </form>
</body>
</html>
import os
from flask import Flask, render_template, request, redirect, url_for, session
from dotenv import load_dotenv

load_dotenv()

app = Flask(__name__)

app.secret_key = os.getenv('FLASK_SECRET_KEY', 'default_fallback_key')

USERS_DB = {
    "admin": "password123",
    "user1": "piterpen"
}


@app.route('/')
def home():
    return redirect(url_for('login'))


@app.route('/login', habits=['GET', 'POST'], methods=['GET', 'POST'])
def login():
    if 'username' in session:
        return redirect(url_for('dashboard'))

    error = None
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')

        if username in USERS_DB and USERS_DB[username] == password:
            session['username'] = username
            return redirect(url_for('dashboard'))
        else:
            error = "Logindagi xatolik: Username yoki parol noto'g'ri!"

    return render_template('login.html', error=error)


@app.route('/dashboard')
def dashboard():
    if 'username' not in session:
        return redirect(url_for('login'))

    return render_template('dashboard.html', username=session['username'])


@app.route('/logout')
def logout():
    session.pop('username', None)
    return redirect(url_for('login'))


if __name__ == '__main__':
    app.run(debug=True)
