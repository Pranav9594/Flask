
from flask import Flask, render_template, request, redirect, url_for, session, flash, jsonify
import pickle
import pandas as pd
import os

# Initialize the Flask application
app = Flask(__name__)
app.secret_key = 'your_secret_key_here'  # Change this in production!

# Load the pre-trained machine learning model
# Ensure that model.pkl is in the same folder or adjust the path accordingly.
model_path = os.path.join(os.getcwd(), "model.pkl")
with open(model_path, "rb") as file:
    model = pickle.load(file)

# ---------------------- ROUTES ----------------------------

# Home Page Route
@app.route('/')
def home():
    return render_template('home.html')

# Login Route
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        # Retrieve email and password from the form
        email = request.form.get('email')
        password = request.form.get('password')
        # For demonstration, we use a dummy check (replace with real authentication logic)
        if email == "test@example.com" and password == "password":
            session['user'] = email
            flash("Logged in successfully!", "success")
            return redirect(url_for('home'))
        else:
            flash("Invalid credentials.", "danger")
            return redirect(url_for('login'))
    return render_template('login.html')

# Registration Route
@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        # Extract registration details from form (name, email, password)
        name = request.form.get('name')
        email = request.form.get('email')
        password = request.form.get('password')
        # Dummy registration logic (replace with your own, e.g., storing in a database)
        flash("Registration successful! Please log in.", "success")
        return redirect(url_for('login'))
    return render_template('reg.html')

# Logout Route
@app.route('/logout')
def logout():
    session.pop('user', None)
    flash("Logged out successfully.", "info")
    return redirect(url_for('home'))

# Prediction Route
@app.route('/predict', methods=['GET', 'POST'])
def predict():
    prediction_result = None
    if request.method == 'POST':
        try:
            # Extract input data from the prediction form
            # Adjust the field names as they appear in your Predict.html
            age = float(request.form.get('age'))
            education = float(request.form.get('education'))
            experience = float(request.form.get('experience'))
            
            # Create a DataFrame with the input data.
            # Ensure the column names match those used when training the model.
            input_data = pd.DataFrame([[age, education, experience]],
                                      columns=['Age', 'EducationYears', 'YearsExperience'])
            
            # Make a prediction using the loaded model
            pred = model.predict(input_data)[0]
            prediction_result = "Employed" if pred == 1 else "Not Employed"
        except Exception as e:
            prediction_result = f"Error: {str(e)}"
    # Render Predict.html and pass the prediction result (if any)
    return render_template('Predict.html', prediction=prediction_result)

# (Optional) API Endpoint for AJAX requests
@app.route('/api/predict', methods=['POST'])
def api_predict():
    data = request.get_json(force=True)
    try:
        input_data = pd.DataFrame([data])
        pred = model.predict(input_data)[0]
        result = {"prediction": int(pred)}
    except Exception as e:
        result = {"error": str(e)}
    return jsonify(result)

# -----------------------------------------------------------

if __name__ == '__main__':
    app.run(debug=True)
