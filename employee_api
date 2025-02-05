from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from flask_cors import CORS  # Add CORS support

app = Flask(__name__)

# Enable CORS
CORS(app)

# Database Configuration
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+mysqlconnector://root:12345678@localhost/employee_db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

# Initialize SQLAlchemy
db = SQLAlchemy(app)

# Define the Employee Model
class Employee(db.Model):
    __tablename__ = 'employees'  # Ensure the table name matches your database
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    role = db.Column(db.String(100), nullable=False)
    department = db.Column(db.String(100), nullable=False)

# Initialize Database Tables
with app.app_context():
    db.create_all()

# Routes

# GET all employees
@app.route('/employees', methods=['GET'])
def get_employees():
    try:
        employees = Employee.query.all()
        employee_list = [
            {"id": emp.id, "name": emp.name, "role": emp.role, "department": emp.department}
            for emp in employees
        ]
        return jsonify({"status": "success", "data": employee_list}), 200
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

# POST a new employee
@app.route('/employees', methods=['POST'])
def add_employee():
    data = request.json
    required_fields = ['id', 'name', 'role', 'department']
    
    # Validate required fields
    for field in required_fields:
        if field not in data:
            return jsonify({"status": "error", "message": f"Missing field: {field}"}), 400

    # Check for duplicate ID
    if Employee.query.get(data['id']):
        return jsonify({"status": "error", "message": "Employee with this ID already exists"}), 400

    try:
        new_employee = Employee(
            id=data['id'],
            name=data['name'],
            role=data['role'],
            department=data['department']
        )
        db.session.add(new_employee)
        db.session.commit()
        return jsonify({"status": "success", "message": "Employee added successfully!"}), 201
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

# PUT (update) an employee by ID
@app.route('/employees/<int:id>', methods=['PUT'])
def update_employee(id):
    employee = Employee.query.get(id)
    if not employee:
        return jsonify({"status": "error", "message": f"Employee with ID {id} not found"}), 404

    data = request.json
    try:
        employee.name = data.get('name', employee.name)
        employee.role = data.get('role', employee.role)
        employee.department = data.get('department', employee.department)

        db.session.commit()
        return jsonify({"status": "success", "message": "Employee updated successfully!"}), 200
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

# DELETE an employee by ID
@app.route('/employees/<int:id>', methods=['DELETE'])
def delete_employee(id):
    employee = Employee.query.get(id)
    if not employee:
        return jsonify({"status": "error", "message": f"Employee with ID {id} not found"}), 404

    try:
        db.session.delete(employee)
        db.session.commit()
        return jsonify({"status": "success", "message": "Employee deleted successfully!"}), 200
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
