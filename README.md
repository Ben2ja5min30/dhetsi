# dhetsi
building a loan management system
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///loans.db'
db = SQLAlchemy(app)

class Client(db.Model):
    client_id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(100), nullable=False)
    phone = db.Column(db.String(20), nullable=False)

class Loan(db.Model):
    loan_id = db.Column(db.Integer, primary_key=True)
    client_id = db.Column(db.Integer, db.ForeignKey('client.client_id'), nullable=False)
    loan_amount = db.Column(db.Float, nullable=False)
    start_date = db.Column(db.Date, nullable=False)
    due_date = db.Column(db.Date, nullable=False)
    interest_rate = db.Column(db.Float, nullable=False)
    status = db.Column(db.String(10), default='active')

@app.route('/create_loan', methods=['POST'])
def create_loan():
    data = request.get_json()
    new_loan = Loan(client_id=data['client_id'], loan_amount=data['loan_amount'],
                    start_date=datetime.strptime(data['start_date'], '%Y-%m-%d'),
                    due_date=datetime.strptime(data['due_date'], '%Y-%m-%d'),
                    interest_rate=data['interest_rate'])
    db.session.add(new_loan)
    db.session.commit()
    return jsonify({'message': 'Loan created successfully!'})

if __name__ == '__main__':
    app.run(debug=True)
