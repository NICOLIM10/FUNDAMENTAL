# FUNDAMENTAL
from flask import Flask, render_template_string, request

app = Flask(__name__)

# HTML Template and CSS design
html_template = '''
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grade Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: aqua; #OUTERBOXCOLOR
            color: #333; #di ko alam kung saan toh
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 600px;
            margin: 50px auto;
            padding: 20px;
            background-color: pink; #INNERBOXCOLOR
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            border-radius: 10px;
            text-align: center;
        }
        h1 {
            color: green; #wordGRADECAUCLATOR
            font-size: 2.5em;
        }
        form {
            margin: 20px 0;
        }
        form label {
            display: block;
            font-size: 1.2em;
            margin-bottom: 10px;
            color: red; #enter your name prelim grade color
        }
        form input[type="text"] {
            padding: 10px;
            font-size: 1em;
            width: 80%;
            margin-bottom: 20px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        form button {
            padding: 10px 20px;
            font-size: 1.2em;
            background-color: #4CAF50;
            color: white; #calculate color
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        form button:hover {
            background-color: #45a049;
        }
        .result {
            margin-top: 30px;
            padding: 20px;
            background-color: #e7f3e7;
            border: 2px solid #4CAF50;
            border-radius: 5px;
        }
        .result h2 {
            color: red;
        }
        .result p {
            font-size: 1.2em;
            color: blue; 
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Grade Calculator</h1>
        <form method="POST">
            <label for="prelim">Enter your Prelim Grade:</label>
            <input type="text" id="prelim" name="prelim" required>
            <button type="submit">Calculate</button>
        </form>
        {% if result %}
        <div class="result">
            <h2>Results:</h2>
            <p>{{ result }}</p>
        </div>
        {% endif %}
    </div>
</body>
</html>
'''

@app.route('/', methods=['GET', 'POST'])
def calculate_grades():
    result = None
    if request.method == 'POST':
        try:
            # Get the Prelim grade from the user
            prelim_grade = float(request.form['prelim'])
            
            if prelim_grade < 0 or prelim_grade > 100:
                result = "Error: Prelim grade must be between 0 and 100."
            else:
                # Calculate remaining marks needed to pass
                min_midterm = (75 - (0.20 * prelim_grade) - (0.50 * 100)) / 0.30
                min_final = (75 - (0.20 * prelim_grade) - (0.30 * 100)) / 0.50

                # Check if it's possible to pass
                if prelim_grade * 0.20 + 100 * 0.30 + 100 * 0.50 < 75:
                    result = f"Based on your Prelim grade of {prelim_grade}, it's impossible to pass."
                else:
                    result = f"To pass, you need at least a Midterm grade of {max(0, min_midterm):.2f} and a Final grade of {max(0, min_final):.2f}."
        
        except ValueError:
            result = "Error: Please enter a valid numerical Prelim grade."
    
    return render_template_string(html_template, result=result)

if __name__ == '__main__':
    app.run(debug=True)
