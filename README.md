# Flask
What This Code Does
Initialization:

Imports necessary modules.
Initializes the Flask app and sets a secret key (required for session management).
Loads your pre-trained machine learning model from model.pkl.
Routes:

Home Route (/): Renders your homepage (home.html).
Login, Register, and Logout Routes: Provide basic user authentication functionality using forms (the repository includes login.html, reg.html, etc.).
Prediction Route (/predict):
On a GET request, it renders the prediction page (Predict.html) with a form.
On a POST request, it reads form inputs (e.g., age, education, experience), converts them to a pandas DataFrame (ensuring the column names match your model’s training), calls the model’s predict method, and then passes the result to the template.
Optional API Endpoint (/api/predict): Accepts JSON input and returns a JSON response, which is useful for AJAX calls.
Running the App:

When executed directly, the Flask server runs in debug mode on http://127.0.0.1:5000/.
