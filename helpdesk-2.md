## 사용자의견 반영

<style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            padding: 40px;
            width: 100%;
            max-width: 500px;
            position: relative;
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }

        .header h1 {
            color: #333;
            font-size: 28px;
            font-weight: 600;
            margin-bottom: 10px;
        }

        .header p {
            color: #666;
            font-size: 16px;
        }

        .form-group {
            margin-bottom: 25px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            color: #333;
            font-weight: 500;
            font-size: 14px;
        }

        select, textarea {
            width: 100%;
            padding: 15px;
            border: 2px solid #e1e5e9;
            border-radius: 12px;
            font-size: 16px;
            transition: border-color 0.3s ease;
            background: #f8f9fa;
        }

        select:focus, textarea:focus {
            outline: none;
            border-color: #667eea;
            background: white;
        }

        textarea {
            resize: vertical;
            min-height: 120px;
            font-family: inherit;
        }

        .submit-btn {
            width: 100%;
            padding: 16px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 12px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.2s ease;
        }

        .submit-btn:hover {
            transform: translateY(-2px);
        }

        .submit-btn:active {
            transform: translateY(0);
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }

        .modal-content {
            background: white;
            border-radius: 20px;
            padding: 40px;
            text-align: center;
            max-width: 400px;
            width: 90%;
            box-shadow: 0 20px 40px rgba(0,0,0,0.2);
        }

        .modal-icon {
            font-size: 48px;
            margin-bottom: 20px;
        }

        .success .modal-icon {
            color: #28a745;
        }

        .error .modal-icon {
            color: #dc3545;
        }

        .modal h3 {
            color: #333;
            font-size: 20px;
            margin-bottom: 15px;
        }

        .modal p {
            color: #666;
            margin-bottom: 25px;
            line-height: 1.5;
        }

        .modal-btn {
            padding: 12px 30px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 500;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        .success .modal-btn {
            background: #28a745;
            color: white;
        }

        .error .modal-btn {
            background: #dc3545;
            color: white;
        }

        .modal-btn:hover {
            opacity: 0.9;
        }

        .loading {
            display: none;
            text-align: center;
            margin-top: 20px;
        }

        .spinner {
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            margin: 0 auto 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
</style>

<div class="container">
       <div class="header">
       <h1>한국역사연표</h1>
       <p>소중한 의견을 들려주세요</p>
</div>

<form id="feedbackForm">
     <div class="form-group">
         <label for="category">분류</label>
         <select id="category" name="category" required>
             <option value="">분류를 선택해주세요</option>
             <option value="bug">버그 및 오류신고</option>
             <option value="feature">아이디어제안</option>
             <option value="case">활용사례공유</option>
             <option value="general">자유의견</option>
         </select>
     </div>

     <div class="form-group">
         <label for="message">의견내용</label>
         <textarea id="message" name="message" placeholder="의견을 자유롭게 작성해주세요..." required></textarea>
     </div>

     <button type="submit" class="submit-btn">보내기</button>
</form>

<div class="loading" id="loading">
     <div class="spinner"></div>
     <p>전송 중...</p>
     </div>
</div>

<!-- 성공 모달 -->
<div class="modal success" id="successModal">
        <div class="modal-content">
            <div class="modal-icon">✅</div>
            <h3>전송 완료</h3>
            <p>개발자에게 발송되었습니다.<br>소중한 의견 감사합니다.</p>
            <button class="modal-btn" onclick="closeSuccessModal()">완료</button>
        </div>
</div>

<!-- 실패 모달 -->
<div class="modal error" id="errorModal">
        <div class="modal-content">
            <div class="modal-icon">❌</div>
            <h3>전송 실패</h3>
            <p id="errorMessage">전송 중 오류가 발생했습니다.</p>
            <button class="modal-btn" onclick="closeErrorModal()">재시도</button>
        </div>
</div>

<script>
        // Google Apps Script 웹앱 URL (실제 URL로 변경 필요)
        const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbxGrVhk4Q5ErtZ7sEwvQTvPS-J15Ie1PupY5HbnizQhMCw0YtpXIWqAfNRn1t2YnHA/exec';

        document.getElementById('feedbackForm').addEventListener('submit', async function(e) {
            e.preventDefault();
            
            const category = document.getElementById('category').value;
            const message = document.getElementById('message').value;
            
            // 필드 검증
            if (!category || !message.trim()) {
                showError('모든 필드를 입력해주세요.');
                return;
            }
            
            // 로딩 표시
            showLoading(true);
            
            try {
                const response = await fetch(GOOGLE_SCRIPT_URL, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/x-www-form-urlencoded',
                    },
                    body: new URLSearchParams({
                        'category': category,
                        'message': message,
                        'timestamp': new Date().toISOString()
                    })
                });
                
                if (response.ok) {
                    showSuccess();
                } else {
                    throw new Error('서버 오류');
                }
            } catch (error) {
                showError('네트워크 오류가 발생했습니다. 잠시 후 다시 시도해주세요.');
            } finally {
                showLoading(false);
            }
        });

        function showLoading(show) {
            document.getElementById('loading').style.display = show ? 'block' : 'none';
        }

        function showSuccess() {
            document.getElementById('successModal').style.display = 'flex';
        }

        function showError(message) {
            document.getElementById('errorMessage').textContent = message;
            document.getElementById('errorModal').style.display = 'flex';
        }

        function closeSuccessModal() {
            document.getElementById('successModal').style.display = 'none';
            // 폼 초기화
            document.getElementById('feedbackForm').reset();
        }

        function closeErrorModal() {
            document.getElementById('errorModal').style.display = 'none';
            // 입력된 내용은 유지
        }

        // 모달 외부 클릭 시 닫기
        document.getElementById('successModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeSuccessModal();
            }
        });

        document.getElementById('errorModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeErrorModal();
            }
        });
</script>
