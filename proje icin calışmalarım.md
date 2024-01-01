
Tabii ki, Amazon Linux 2023 EC2 örneğinde Flask kullanarak formları işlemek, veritabanına bağlanmak ve SQL kullanmak için adımları anlatmaya çalışayım.

Adım 1: Python ve Flask Kurulumu
Öncelikle, Python ve Flask'ı EC2 örneğine kurmanız gerekiyor. SSH ile EC2 örneğine bağlanın ve aşağıdaki adımları takip edin:

bash

# Güncelleştirmeleri yapın
sudo yum update -y

# Python3'ü yükleyin
sudo yum install python3 -y

# pip3'ü yükleyin
sudo yum install python3-pip -y

# Flask'ı yükleyin
pip3 install Flask

Adım 2: SQLite Veritabanı Kurulumu ve Flask Bağlantısı

# SQLite3'ü yükleyin
sudo yum install sqlite -y

# Flask SQL Alchemy'yi yükleyin
pip3 install Flask-SQLAlchemy


Adım 3: Flask Uygulaması Oluşturma

1. Flask uygulamanızın bulunduğu bir dizin oluşturun:

mkdir myflaskapp
cd myflaskapp

1.1. Ardından, bir dosya oluşturun ve içine Flask uygulamanızı yazın. Örneğin:

# app.py

from flask import Flask, render_template, request
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///./database.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

@app.route('/', methods=['GET', 'POST'])
def home():
    if request.method == 'POST':
        username = request.form['username']
        email = request.form['email']

        new_user = User(username=username, email=email)
        db.session.add(new_user)
        db.session.commit()

    users = User.query.all()
    return render_template('index.html', users=users)

if __name__ == '__main__':
    db.create_all()
    app.run(debug=True, host='0.0.0.0', port=8080)


Bu Flask uygulaması, formdan gelen bilgileri bir SQLite veritabanına ekleyen basit bir örnektir.


<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask Form App</title>
</head>
<body>
    <h1>Users</h1>
    <ul>
        {% for user in users %}
            <li>{{ user.username }} - {{ user.email }}</li>
        {% endfor %}
    </ul>
    
    <h2>Add User</h2>
    <form method="post" action="/">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required>
        
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required>
        
        <button type="submit">Add User</button>
    </form>
</body>
</html>


Bu HTML şablonu, mevcut kullanıcıları listeler ve yeni bir kullanıcı eklemek için basit bir form içerir.


Adım 5: Uygulamayı Çalıştırma
Uygulamayı çalıştırmak için terminale şu komutu yazın:

python3 app.py

Uygulama şimdi http://YOUR_EC2_PUBLIC_IP:8080 adresinde çalışıyor olmalıdır. Bu adreste uygulamanıza erişebilir ve form aracılığıyla yeni kullanıcılar ekleyebilirsiniz.

Bu örnek, temel bir Flask uygulamasını ve form işlemlerini gösterir. Daha karmaşık uygulamalar için bu temeli genişletebilirsiniz.






