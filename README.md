<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تسجيل متدرب جديد</title>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-firestore-compat.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .register-container {
            background-color: white;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 0 15px rgba(0,0,0,0.1);
            width: 350px;
            text-align: center;
        }
        .form-group {
            margin-bottom: 20px;
            text-align: right;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            width: 100%;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        .error {
            color: red;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="register-container">
        <h2>تسجيل متدرب جديد</h2>
        <form id="registerForm">
            <div class="form-group">
                <label for="name">الاسم الكامل</label>
                <input type="text" id="name" required>
            </div>
            <div class="form-group">
                <label for="email">البريد الإلكتروني</label>
                <input type="email" id="email" required>
            </div>
            <div class="form-group">
                <label for="password">كلمة المرور (6 أحرف على الأقل)</label>
                <input type="password" id="password" required minlength="6">
            </div>
            <button type="submit">تسجيل</button>
            <div id="errorMsg" class="error"></div>
        </form>
        <p>لديك حساب بالفعل؟ <a href="login.html">سجل الدخول</a></p>
    </div>

    <script>
        // تهيئة Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyBWdrUtLP4fVKHDmQDC6nUJzBMPA7AmbMc",
            authDomain: "solar-engineer-43cbc.firebaseapp.com",
            projectId: "solar-engineer-43cbc",
            storageBucket: "solar-engineer-43cbc.firebasestorage.app",
            messagingSenderId: "816573344174",
            appId: "1:816573344174:web:b472b052d26b7be1d7003f"
        };
        
        // Initialize Firebase
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        document.getElementById('registerForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const name = document.getElementById('name').value;
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            const errorMsg = document.getElementById('errorMsg');
            
            errorMsg.textContent = '';
            
            // إنشاء حساب جديد في Firebase Authentication
            auth.createUserWithEmailAndPassword(email, password)
                .then((userCredential) => {
                    // نجح التسجيل
                    const user = userCredential.user;
                    
                    // حفظ بيانات إضافية في Firestore
                    return db.collection('trainees').doc(user.uid).set({
                        name: name,
                        email: email,
                        createdAt: firebase.firestore.FieldValue.serverTimestamp(),
                        userType: "trainee",
                        lastLogin: firebase.firestore.FieldValue.serverTimestamp()
                    });
                })
                .then(() => {
                    // إرسال رسالة تحقق إلى البريد الإلكتروني (اختياري)
                    return auth.currentUser.sendEmailVerification();
                })
                .then(() => {
                    // توجيه المتدرب إلى صفحة الكورس بعد التسجيل
                    window.location.href = 'http://login.najyacademia.com';
                })
                .catch((error) => {
                    const errorCode = error.code;
                    const errorMessage = error.message;
                    
                    // عرض رسائل خطأ بالعربية
                    if (errorCode === 'auth/email-already-in-use') {
                        errorMsg.textContent = 'هذا البريد الإلكتروني مستخدم بالفعل';
                    } else if (errorCode === 'auth/weak-password') {
                        errorMsg.textContent = 'كلمة المرور يجب أن تكون 6 أحرف على الأقل';
                    } else if (errorCode === 'auth/invalid-email') {
                        errorMsg.textContent = 'بريد إلكتروني غير صالح';
                    } else {
                        errorMsg.textContent = 'حدث خطأ أثناء التسجيل: ' + errorMessage;
                    }
                });
        });
    </script>
</body>
</html>
