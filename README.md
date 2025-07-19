<!-- صفحة للمسؤول فقط -->
<template id="traineeTemplate">
    <div class="trainee-card">
        <h3 data-name></h3>
        <p data-email></p>
        <p>تاريخ التسجيل: <span data-createdAt></span></p>
    </div>
</template>

<div id="adminDashboard">
    <h1>إدارة المتدربين</h1>
    
    <div class="add-trainee">
        <h2>إضافة متدرب جديد</h2>
        <input type="text" id="traineeName" placeholder="الاسم الكامل">
        <input type="email" id="traineeEmail" placeholder="البريد الإلكتروني">
        <input type="password" id="traineePassword" placeholder="كلمة المرور">
        <button id="addTraineeBtn">إضافة متدرب</button>
    </div>
    
    <div id="traineesList"></div>
</div>

<script>
    // تهيئة Firebaseconst firebaseConfig = {
  apiKey: "AIzaSyBWdrUtLP4fVKHDmQDC6nUJzBMPA7AmbMc", // مفتاح API الخاص بك
  authDomain: "solar-engineer-43cbc.firebaseapp.com",
  projectId: "solar-engineer-43cbc",
  storageBucket: "solar-engineer-43cbc.firebasestorage.app",
  messagingSenderId: "816573344174",
  appId: "1:816573344174:web:b472b052d26b7be1d7003f"
};

// تهيئة Firebase
firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();
    // تحقق من صلاحيات المسؤول
    auth.onAuthStateChanged((user) => {
        if (user) {
            db.collection('admins').doc(user.uid).get()
                .then((doc) => {
                    if (doc.exists) {
                        loadTrainees();
                    } else {
                        window.location.href = 'unauthorized.html';
                    }
                });
        } else {
            window.location.href = 'login.html';
        }
    });
    
    // إضافة متدرب جديد
 document.getElementById('addTraineeBtn').addEventListener('click', async function() {
    const name = document.getElementById('traineeName').value;
    const email = document.getElementById('traineeEmail').value;
    const password = document.getElementById('traineePassword').value;

    try {
        // 1. إنشاء المستخدم في Authentication
        const userCredential = await auth.createUserWithEmailAndPassword(email, password);
        
        // 2. حفظ بيانات إضافية في Firestore
        await db.collection('Trainee').doc(userCredential.user.uid).set({
            name: name,
            email: email,
            createdAt: firebase.firestore.FieldValue.serverTimestamp(),
            userType: "trainee",
            status: "active"
        });

        // 3. إرسال رسالة التحقق (اختياري)
        await userCredential.user.sendEmailVerification();

        alert('تم إضافة المتدرب بنجاح');
        loadTrainees();
        
        // مسح الحقول بعد الإضافة
        document.getElementById('traineeName').value = '';
        document.getElementById('traineeEmail').value = '';
        document.getElementById('traineePassword').value = '';
        
    } catch (error) {
        console.error("Error adding trainee:", error);
        alert(`حدث خطأ: ${error.message}`);
    }
});
    
    // عرض قائمة المتدربين
    function loadTrainees() {
        db.collection('Trainee').get()
            .then((querySnapshot) => {
                const template = document.getElementById('traineeTemplate').content;
                const container = document.getElementById('traineesList');
                container.innerHTML = '';
                
                querySnapshot.forEach((doc) => {
                    const trainee = doc.data();
                    const clone = template.cloneNode(true);
                    
                    clone.querySelector('[data-name]').textContent = trainee.name;
                    clone.querySelector('[data-email]').textContent = trainee.email;
                    clone.querySelector('[data-createdAt]').textContent = 
                        trainee.createdAt.toDate().toLocaleDateString();
                    
                    container.appendChild(clone);
                });
            });
    }
</script>
