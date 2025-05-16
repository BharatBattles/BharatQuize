<script type="text/javascript">
        var gk_isXlsx = false;
        var gk_xlsxFileLookup = {};
        var gk_fileData = {};
        function filledCell(cell) {
          return cell !== '' && cell != null;
        }
        function loadFileData(filename) {
        if (gk_isXlsx && gk_xlsxFileLookup[filename]) {
            try {
                var workbook = XLSX.read(gk_fileData[filename], { type: 'base64' });
                var firstSheetName = workbook.SheetNames[0];
                var worksheet = workbook.Sheets[firstSheetName];

                // Convert sheet to JSON to filter blank rows
                var jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, blankrows: false, defval: '' });
                // Filter out blank rows (rows where all cells are empty, null, or undefined)
                var filteredData = jsonData.filter(row => row.some(filledCell));

                // Heuristic to find the header row by ignoring rows with fewer filled cells than the next row
                var headerRowIndex = filteredData.findIndex((row, index) =>
                  row.filter(filledCell).length >= filteredData[index + 1]?.filter(filledCell).length
                );
                // Fallback
                if (headerRowIndex === -1 || headerRowIndex > 25) {
                  headerRowIndex = 0;
                }

                // Convert filtered JSON back to CSV
                var csv = XLSX.utils.aoa_to_sheet(filteredData.slice(headerRowIndex)); // Create a new sheet from filtered array of arrays
                csv = XLSX.utils.sheet_to_csv(csv, { header: 1 });
                return csv;
            } catch (e) {
                console.error(e);
                return "";
            }
        }
        return gk_fileData[filename] || "";
        }
        </script><!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bharat Quizzes</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/197/197419.png" type="image/png">
</head>
<body>
    <header>
        <div class="logo">
            <i class="fas fa-graduation-cap"></i>
            <h1>Bharat Quizzes</h1>
        </div>
        <nav>
            <a href="index.html"><i class="fas fa-home"></i> Home</a>
            <a href="login.html"><i class="fas fa-sign-in-alt"></i> Login</a>
        </nav>
    </header>
    <main>
        <section>
            <h2>Welcome to Bharat Quizzes!</h2>
            <p>Test your knowledge or create your own quizzes about India. Log in to start as a Live Student or Quiz Master!</p>
            <a href="login.html" class="btn"><i class="fas fa-sign-in-alt"></i> Log In</a>
        </section>
    </main>
    <footer>
        <p>© 2025 Bharat Quizzes. All rights reserved.</p>
    </footer>
</body>
</html>

<!-- login.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login - Bharat Quizzes</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/197/197419.png" type="image/png">
</head>
<body>
    <header>
        <div class="logo">
            <i class="fas fa-graduation-cap"></i>
            <h1>Bharat Quizzes</h1>
        </div>
        <nav>
            <a href="index.html"><i class="fas fa-home"></i> Home</a>
            <a href="login.html"><i class="fas fa-sign-in-alt"></i> Login</a>
        </nav>
    </header>
    <main>
        <section class="login-section">
            <h2>Log In</h2>
            <div id="login-form">
                <label for="email"><i class="fas fa-envelope"></i> Email:</label>
                <input type="email" id="email" placeholder="Enter your email" required>
                <label for="password"><i class="fas fa-lock"></i> Password:</label>
                <input type="password" id="password" placeholder="Enter your password" required>
                <button onclick="sendVerificationCode()"><i class="fas fa-paper-plane"></i> Send Verification Code</button>
            </div>
            <div id="verify-form" style="display: none;">
                <p>Check your console (F12) for the verification code.</p>
                <label for="code"><i class="fas fa-key"></i> Verification Code:</label>
                <input type="text" id="code" placeholder="Enter code" required>
                <label for="role"><i class="fas fa-user"></i> Role:</label>
                <select id="role" required>
                    <option value="" disabled selected>Select your role</option>
                    <option value="student">Live Student</option>
                    <option value="quizmaster">Quiz Master</option>
                </select>
                <button onclick="verifyAndLogin()"><i class="fas fa-check"></i> Verify and Login</button>
            </div>
            <div id="login-message"></div>
        </section>
    </main>
    <footer>
        <p>© 2025 Bharat Quizzes. All rights reserved.</p>
    </footer>
    <script src="script.js"></script>
</body>
</html>

<!-- quiz.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quiz - Bharat Quizzes</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/197/197419.png" type="image/png">
</head>
<body>
    <header>
        <div class="logo">
            <i class="fas fa-graduation-cap"></i>
            <h1>Bharat Quizzes</h1>
        </div>
        <nav>
            <a href="index.html"><i class="fas fa-home"></i> Home</a>
            <a href="quiz.html"><i class="fas fa-question-circle"></i> Take Quiz</a>
            <a href="certificate.html"><i class="fas fa-certificate"></i> Certificate</a>
            <a href="login.html"><i class="fas fa-sign-out-alt"></i> Logout</a>
        </nav>
    </header>
    <main>
        <section id="quiz">
            <h2>Select a Quiz</h2>
            <div id="quiz-list"></div>
            <div id="quiz-container" style="display: none;"></div>
            <button id="submit-quiz" onclick="submitQuiz()" style="display: none;"><i class="fas fa-check"></i> Submit Quiz</button>
            <div id="result"></div>
        </section>
    </main>
    <footer>
        <p>© 2025 Bharat Quizzes. All rights reserved.</p>
    </footer>
    <script src="script.js"></script>
</body>
</html>

<!-- certificate.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Certificate - Bharat Quizzes</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/197/197419.png" type="image/png">
</head>
<body>
    <header>
        <div class="logo">
            <i class="fas fa-graduation-cap"></i>
            <h1>Bharat Quizzes</h1>
        </div>
        <nav>
            <a href="index.html"><i class="fas fa-home"></i> Home</a>
            <a href="quiz.html"><i class="fas fa-question-circle"></i> Take Quiz</a>
            <a href="certificate.html"><i class="fas fa-certificate"></i> Certificate</a>
            <a href="login.html"><i class="fas fa-sign-out-alt"></i> Logout</a>
        </nav>
    </header>
    <main>
        <section class="certificate-section">
            <h2>Generate Your Certificate</h2>
            <p>Enter your name to generate a certificate for completing the quiz.</p>
            <input type="text" id="user-name" placeholder="Enter your name">
            <button onclick="generateCertificate()"><i class="fas fa-download"></i> Generate Certificate</button>
            <div id="certificate-link"></div>
        </section>
    </main>
    <footer>
        <p>© 2025 Bharat Quizzes. All rights reserved.</p>
    </footer>
    <script src="script.js"></script>
</body>
</html>

<!-- add-quiz.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Add Quiz - Bharat Quizzes</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/197/197419.png" type="image/png">
</head>
<body>
    <header>
        <div class="logo">
            <i class="fas fa-graduation-cap"></i>
            <h1>Bharat Quizzes</h1>
        </div>
        <nav>
            <a href="index.html"><i class="fas fa-home"></i> Home</a>
            <a href="add-quiz.html"><i class="fas fa-plus-circle"></i> Add Quiz</a>
            <a href="login.html"><i class="fas fa-sign-out-alt"></i> Logout</a>
        </nav>
    </header>
    <main>
        <section class="add-quiz-section">
            <h2>Create a New Quiz</h2>
            <p>Enter the quiz title and add questions. Save when done.</p>
            <label for="quiz-title"><i class="fas fa-book"></i> Quiz Title:</label>
            <input type="text" id="quiz-title" placeholder="Enter quiz title" required>
            <h3>Add Questions</h3>
            <form id="add-question-form">
                <label for="question-type"><i class="fas fa-list"></i> Question Type:</label>
                <select id="question-type" required>
                    <option value="" disabled selected>Select question type</option>
                    <option value="multiple">Multiple Choice</option>
                    <option value="fill">Fill in the Blank</option>
                    <option value="qa">Question and Answer</option>
                </select>
                <div id="question-input">
                    <label for="question"><i class="fas fa-question"></i> Question:</label>
                    <input type="text" id="question" placeholder="Enter the question" required>
                </div>
                <div id="options-input" style="display: none;">
                    <label for="option1"><i class="fas fa-dot-circle"></i> Option 1:</label>
                    <input type="text" id="option1" placeholder="Enter option 1">
                    <label for="option2"><i class="fas fa-dot-circle"></i> Option 2:</label>
                    <input type="text" id="option2" placeholder="Enter option 2">
                    <label for="option3"><i class="fas fa-dot-circle"></i> Option 3:</label>
                    <input type="text" id="option3" placeholder="Enter option 3">
                    <label for="option4"><i class="fas fa-dot-circle"></i> Option 4:</label>
                    <input type="text" id="option4" placeholder="Enter option 4">
                    <label for="answer"><i class="fas fa-check-circle"></i> Correct Answer:</label>
                    <select id="answer">
                        <option value="" disabled selected>Select correct answer</option>
                        <option value="1">Option 1</option>
                        <option value="2">Option 2</option>
                        <option value="3">Option 3</option>
                        <option value="4">Option 4</option>
                    </select>
                </div>
                <div id="answer-input" style="display: none;">
                    <label for="single-answer"><i class="fas fa-check-circle"></i> Correct Answer:</label>
                    <input type="text" id="single-answer" placeholder="Enter correct answer">
                </div>
                <button type="submit"><i class="fas fa-plus"></i> Add Question</button>
            </form>
            <div id="question-list"></div>
            <button onclick="saveQuiz()" class="btn"><i class="fas fa-save"></i> Save and Upload Quiz</button>
            <div id="form-message"></div>
        </section>
    </main>
    <footer>
        <p>© 2025 Bharat Quizzes. All rights reserved.</p>
    </footer>
    <script src="script.js"></script>
</body>
</html>

<!-- styles.css -->
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Arial, sans-serif;
}

body {
    line-height: 1.6;
    color: #333;
    background: linear-gradient(135deg, #ff9933, #ffffff, #138808);
    background-attachment: fixed;
}

header {
    background: #ff9933;
    color: white;
    padding: 1rem;
    text-align: center;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.logo {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.5rem;
}

.logo i {
    font-size: 1.5rem;
}

header h1 {
    margin-bottom: 0.5rem;
}

nav a {
    color: white;
    margin: 0 1rem;
    text-decoration: none;
    font-weight: bold;
}

nav a i {
    margin-right: 0.3rem;
}

nav a:hover {
    text-decoration: underline;
}

main {
    max-width: 800px;
    margin: 2rem auto;
    padding: 0 1rem;
    background: rgba(255, 255, 255, 0.9);
    border-radius: 10px;
    padding: 2rem;
}

section {
    margin-bottom: 2rem;
}

.btn {
    display: inline-block;
    padding: 0.5rem 1rem;
    background: #138808;
    color: white;
    text-decoration: none;
    border-radius: 5px;
    transition: background 0.3s;
}

.btn i {
    margin-right: 0.3rem;
}

.btn:hover {
    background: #0f6b06;
}

#quiz-list button {
    display: block;
    width: 100%;
    margin: 0.5rem 0;
    padding: 0.5rem;
    background: #f9f9f9;
    border: 1px solid #ccc;
    border-radius: 5px;
    text-align: left;
    cursor: pointer;
}

#quiz-list button:hover {
    background: #e0f7e0;
}

#quiz-container div {
    margin-bottom: 1rem;
    padding: 1rem;
    background: #f9f9f9;
    border-radius: 5px;
}

#quiz-container label {
    margin-left: 0.5rem;
}

#quiz-container input {
    margin-right: 0.5rem;
}

#submit-quiz {
    padding: 0.5rem 1rem;
    background: #138808;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background 0.3s;
}

#submit-quiz i {
    margin-right: 0.3rem;
}

#submit-quiz:hover {
    background: #0f6b06;
}

#result {
    margin-top: 1rem;
    font-weight: bold;
    padding: 1rem;
    background: #e0f7e0;
    border-radius: 5px;
}

input[type="text"], input[type="email"], input[type="password"], select {
    padding: 0.5rem;
    margin: 0.5rem 0;
    width: 100%;
    max-width: 500px;
    border: 1px solid #ccc;
    border-radius: 5px;
}

button {
    padding: 0.5rem 1rem;
    background: #138808;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background 0.3s;
}

button i {
    margin-right: 0.3rem;
}

button:hover {
    background: #0f6b06;
}

.login-section, .certificate-section, .add-quiz-section {
    text-align: center;
    border: 2px solid #ff9933;
    padding: 2rem;
    border-radius: 10px;
    background: #fff;
    position: relative;
}

.login-section::before, .certificate-section::before, .add-quiz-section::before {
    content: "\f559";
    font-family: "Font Awesome 5 Free";
    font-weight: 900;
    position: absolute;
    top: -20px;
    left: 50%;
    transform: translateX(-50%);
    font-size: 2rem;
    color: #138808;
    background: #fff;
    padding: 0 10px;
}

#certificate-link a, .add-quiz-section a {
    color: #138808;
    text-decoration: none;
    font-weight: bold;
}

#certificate-link a:hover, .add-quiz-section a:hover {
    text-decoration: underline;
}

.login-section label, .add-quiz-section label {
    display: block;
    text-align: left;
    margin: 0.5rem 0 0.2rem;
}

.login-section label i, .add-quiz-section label i {
    margin-right: 0.3rem;
}

#login-message, #form-message {
    margin-top: 1rem;
    font-weight: bold;
    color: #138808;
}

#question-list div {
    margin: 0.5rem 0;
    padding: 0.5rem;
    background: #f9f9f9;
    border-radius: 5px;
}

footer {
    text-align: center;
    padding: 1rem;
    background: #333;
    color: white;
    position: relative;
    bottom: 0;
    width: 100%;
}

<!-- script.js -->
const defaultQuizzes = [
    {
        title: "India General Knowledge",
        questions: [
            {
                type: "multiple",
                question: "What is the capital of India?",
                options: ["Mumbai", "Delhi", "Kolkata", "Chennai"],
                answer: "Delhi"
            },
            {
                type: "fill",
                question: "The holiest river in India is the ______.",
                answer: "Ganges"
            },
            {
                type: "qa",
                question: "Who is known as the Father of the Nation in India?",
                answer: "Mahatma Gandhi"
            }
        ]
    }
];

function initializeStorage() {
    if (!localStorage.getItem('users')) {
        localStorage.setItem('users', JSON.stringify([]));
    }
    if (!localStorage.getItem('quizzes')) {
        localStorage.setItem('quizzes', JSON.stringify(defaultQuizzes));
    }
}

function sendVerificationCode() {
    const email = document.getElementById('email').value.trim();
    const password = document.getElementById('password').value.trim();
    if (!email || !password) {
        document.getElementById('login-message').textContent = 'Please fill all fields!';
        return;
    }
    let users = JSON.parse(localStorage.getItem('users')) || [];
    let user = users.find(u => u.email === email);
    if (!user) {
        user = { email, password, verified: false };
        users.push(user);
        localStorage.setItem('users', JSON.stringify(users));
    }
    const code = Math.floor(100000 + Math.random() * 900000).toString();
    localStorage.setItem('verificationCode', code);
    console.log(`Verification Code for ${email}: ${code}`);
    document.getElementById('login-form').style.display = 'none';
    document.getElementById('verify-form').style.display = 'block';
    document.getElementById('login-message').textContent = 'Check console (F12) for your code.';
}

function verifyAndLogin() {
    const code = document.getElementById('code').value.trim();
    const role = document.getElementById('role').value;
    const storedCode = localStorage.getItem('verificationCode');
    if (code !== storedCode) {
        document.getElementById('login-message').textContent = 'Invalid code!';
        return;
    }
    const email = document.getElementById('email').value.trim();
    let users = JSON.parse(localStorage.getItem('users'));
    let user = users.find(uemore
System: * Today's date and time is 12:30 PM IST on Friday, May 16, 2025.
