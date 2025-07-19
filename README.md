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
    // تهيئة Firebase
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
    document.getElementById('addTraineeBtn').addEventListener('click', function() {
        const name = document.getElementById('traineeName').value;
        const email = document.getElementById('traineeEmail').value;
        const password = document.getElementById('traineePassword').value;
        
        auth.createUserWithEmailAndPassword(email, password)
            .then((userCredential) => {
                return db.collection('trainees').doc(userCredential.user.uid).set({
                    name: name,
                    email: email,
                    createdAt: firebase.firestore.FieldValue.serverTimestamp(),
                    userType: "trainee"
                });
            })
            .then(() => {
                alert('تم إضافة المتدرب بنجاح');
                loadTrainees();
            })
            .catch((error) => {
                alert('حدث خطأ: ' + error.message);
            });
    });
    
    // عرض قائمة المتدربين
    function loadTrainees() {
        db.collection('trainees').get()
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
