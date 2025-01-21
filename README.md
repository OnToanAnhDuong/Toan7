<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>ÔN LUYỆN TOÁN THCS - TRUNG TÂM ÁNH DƯƠNG</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 30px;
            line-height: 1.6;
        }
        h1 {
            color: #333;
            text-align: center;
        }
        input[type="text"], input[type="file"], input[type="number"] {
            width: 100%;
            margin-bottom: 15px;
            padding: 8px;
            box-sizing: border-box;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        button {
            padding: 10px 20px;
            background-color: #007bff;
            border: none;
            color: white;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background-color: #0056b3;
        }
        #result {
            margin-top: 20px;
            background-color: #f8f8f8;
            padding: 15px;
            border-radius: 5px;
            white-space: pre-wrap;
        }
        #problemText {
            font-size: 18px;
            margin-bottom: 20px;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #f9f9f9;
            min-height: 100px;
            white-space: pre-wrap;
        }
        #cameraAndImageContainer {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            gap: 20px;
            margin-top: 20px;
        }
        #cameraStream, #capturedImage {
            width: auto;
            height: auto;
            aspect-ratio: 2 / 3;
            border: 1px solid #ddd;
            border-radius: 5px;
            max-height: 300px;
        }
        #topControls, #bottomControls {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 20px;
        }
    </style>
    <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
    <script>
        window.MathJax = {
            tex: {
                inlineMath: [['$', '$'], ['\(', '\)']]
            },
            svg: {
                fontCache: 'global'
            }
        };
    </script>
    <script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
    </script>
</head>
<body>
    <h1>ÔN LUYỆN TOÁN THCS - TRUNG TÂM ÁNH DƯƠNG</h1>
    <div id="loginContainer">
        <input type="text" id="studentId" placeholder="Nhập mã học sinh">
        <button id="loginBtn">Đăng nhập</button>
    </div>
    <div id="mainContent" style="display: none;">
        <div id="topControls">
            <input type="number" id="problemIndexInput" placeholder="Nhập số thứ tự (1, 2, ...)">
            <button id="selectProblemBtn">Hiển thị bài tập</button>
            <button id="randomProblemBtn">Lấy bài tập ngẫu nhiên</button>
        </div>
        <div id="problemText"></div>
        <div id="bottomControls">
            <button id="hintBtn">Gợi ý</button>
            <button id="submitBtn">Chấm Bài</button>
            <button id="deleteAllBtn">Xóa Màn Hình</button>
        </div>
        <div id="cameraAndImageContainer">
            <video id="cameraStream" autoplay playsinline></video>
            <img id="capturedImage" alt="Ảnh đã chụp" style="display: none;">
        </div>
        <div id="result"></div>
    </div>
    <script>
        const API_KEYS = [
            "AIzaSyCzh6doVzV7Dbmbz60B9pNUQIel2N6KEcI",
            "AIzaSyBVQcUrVTtwKeAAsFR8ENM8-kgZl8CsUM0",
            "AIzaSyCmY4FdhZ4qSN6HhBtldgQgSNbDlZ4J1ug"
        ];
        let currentKeyIndex = 0;

        function getNextApiKey() {
            const apiKey = API_KEYS[currentKeyIndex];
            currentKeyIndex = (currentKeyIndex + 1) % API_KEYS.length;
            return apiKey;
        }

        const SHEET_ID = '175acnaYklfdCc_UJ7B3LJgNaUJpfrIENxn6LN76QADM';
        const SHEET_NAME = 'Toan6';

        async function fetchProblems() {
            const SHEET_URL = `https://sheets.googleapis.com/v4/spreadsheets/${SHEET_ID}/values/${SHEET_NAME}?key=${getNextApiKey()}`;
            try {
                const response = await fetch(SHEET_URL);
                if (!response.ok) throw new Error('Không thể tải bài tập từ Google Sheet');
                const data = await response.json();
                if (!data.values || data.values.length <= 1) {
                    console.error('Không có dữ liệu bài tập hợp lệ!');
                    return [];
                }
                const rows = data.values.slice(1); // Bỏ qua tiêu đề
                console.log('Bài tập đã tải:', rows);
                return rows;
            } catch (error) {
                console.error('Lỗi khi tải bài tập:', error);
                return [];
            }
        }

        async function displayProblemByIndex(index) {
            const problems = await fetchProblems();
            if (!problems || problems.length === 0) {
                document.getElementById('problemText').textContent = 'Không có bài tập nào!';
                return;
            }
            const selectedProblem = problems[index - 1];
            if (selectedProblem && selectedProblem.length > 1) {
                document.getElementById('problemText').innerHTML = selectedProblem[1];
                await MathJax.typesetPromise([document.getElementById('problemText')]);
            } else {
                document.getElementById('problemText').textContent = 'Không tìm thấy bài tập với số thứ tự đã chọn!';
            }
        }

        document.getElementById('loginBtn').addEventListener('click', () => {
            const studentId = document.getElementById('studentId').value.trim();
            if (studentId) {
                document.getElementById('loginContainer').style.display = 'none';
                document.getElementById('mainContent').style.display = 'block';
            } else {
                alert('Vui lòng nhập mã học sinh!');
            }
        });

        document.getElementById('selectProblemBtn').addEventListener('click', async () => {
            const index = document.getElementById('problemIndexInput').value.trim();
            if (index) {
                await displayProblemByIndex(parseInt(index));
            } else {
                alert('Vui lòng nhập số thứ tự bài tập!');
            }
        });

        document.getElementById('deleteAllBtn').addEventListener('click', () => {
            document.getElementById('problemText').innerHTML = '';
            document.getElementById('result').innerHTML = '';
            document.getElementById('capturedImage').style.display = 'none';
        });

        document.getElementById('submitBtn').addEventListener('click', () => {
            const problemText = document.getElementById('problemText').textContent.trim();
            if (problemText === '') {
                alert('Vui lòng chọn bài tập trước khi chấm bài!');
                return;
            }
            alert('Chức năng chấm bài đang được thực hiện!');
        });
    </script>
</body>
</html>
