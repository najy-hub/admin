<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>تسجيل الدخول - رحلة المهندس</title>
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --primary-color: #4361ee;
      --secondary-color: #3f37c9;
      --accent-color: #4895ef;
      --white: #ffffff;
      --light-gray: #f8f9fa;
    }

    
    body {
      font-family: 'Cairo', sans-serif;
      background: var(--light-gray);
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }
    
    .login-container {
      background: var(--white);
      border-radius: 10px;
      box-shadow: 0 4px 20px rgba(0,0,0,0.1);
      padding: 30px;
      width: 100%;
      max-width: 400px;
    }
    
    .login-header {
      text-align: center;
      margin-bottom: 30px;
    }
    
    .login-header h1 {
      color: var(--primary-color);
      margin: 0 0 10px;
    }
    
    .login-form .form-group {
      margin-bottom: 20px;
    }
    
    .login-form label {
      display: block;
      margin-bottom: 8px;
      font-weight: bold;
    }
    
    .login-form input {
      width: 100%;
      padding: 12px;
      border: 1px solid #ddd;
      border-radius: 5px;
      font-size: 16px;
      font-family: 'Cairo', sans-serif;
    }
    
    .login-form button {
      background: var(--primary-color);
      color: white;
      border: none;
      padding: 12px;
      border-radius: 5px;
      width: 100%;
      font-size: 16px;
      font-family: 'Cairo', sans-serif;
      cursor: pointer;
      transition: background 0.3s;
    }
    
    .login-form button:hover {
      background: var(--secondary-color);
    }
    
    .error-message {
      color: #ff4444;
      text-align: center;
      margin-top: 15px;
      display: none;
    }
    
    @media (max-width: 480px) {
      .login-container {
        margin: 20px;
        padding: 20px;
      }
    }
  </style>
</head>
<body>
  <div class="login-container">
    <div class="login-header">
      <h1>تسجيل الدخول</h1>
      <p>منصة رحلة المهندس المحترف</p>
    </div>
    
    <form class="login-form" id="loginForm">
      <div class="form-group">
        <label for="email">البريد الإلكتروني</label>
        <input type="email" id="email" required>
      </div>
      
      <div class="form-group">
        <label for="password">كلمة المرور</label>
        <input type="password" id="password" required>
      </div>
      
      <button type="submit">تسجيل الدخول</button>
      <div class="error-message" id="errorMessage"></div>
    </form>
  </div>

  <script>
    <!DOCTYPE html>
<html lang="ar" dir="rtl">

<body>

  <script>
  // 1. تعريف العناصر أولاً
 const API_URL = 'https://script.google.com/macros/s/AKfycbzlshYfATBPjFJyYSkXcaxu9FI2Pxs462dKBSEq4TYFlsPhsfTtv0ykPYfCSGA8cXN_Xg/exec';
    const loginForm = document.getElementById('loginForm');
    const submitBtn = document.getElementById('submitBtn');
    const errorElement = document.getElementById('errorMessage');

    function setButtonState(loading) {
      submitBtn.disabled = loading;
      submitBtn.textContent = loading ? 'جاري تسجيل الدخول...' : 'تسجيل الدخول';
    }

    async function login(email, password) {
      try {
        setButtonState(true);
        errorElement.style.display = 'none';

        const response = await fetch(API_URL, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            email: email,
            password: password
          }),
          redirect: 'follow'
        });

        const data = await response.json();

        if (data.status === "FAILED") {
          throw new Error(data.message || "بيانات الدخول غير صحيحة");
        }

        // حفظ بيانات المستخدم
        localStorage.setItem('userData', JSON.stringify({
          email: data.email,
          unlockedWeeks: data.unlockedWeeks,
          completedWeeks: data.completedWeeks,
          currentWeek: data.currentWeek
        }));

        window.location.href = 'https://course.najyacademia.com/';
      } catch (error) {
        console.error('Login error:', error);
        errorElement.textContent = error.message || 'حدث خطأ أثناء تسجيل الدخول';
        errorElement.style.display = 'block';
      } finally {
        setButtonState(false);
      }
    }

    loginForm.addEventListener('submit', async (e) => {
      e.preventDefault();
      const email = document.getElementById('email').value.trim();
      const password = document.getElementById('password').value;
      
      if (!email || !password) {
        errorElement.textContent = 'يرجى ملء جميع الحقول';
        errorElement.style.display = 'block';
        return;
      }
      
      await login(email, password);
    });

    // التحقق من تسجيل الدخول المسبق
    if (localStorage.getItem('userData')) {
      window.location.href = 'https://course.najyacademia.com/';
    }
    </script>
</body>
</html>
