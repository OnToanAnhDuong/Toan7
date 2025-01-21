<!DOCTYPE html>
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
        h1, h2 {
            color: #333;
            text-align: center;
        }
        label {
            font-weight: bold;
            display: block;
            margin-top: 10px;
        }
        input[type="text"], input[type="number"], input[type="file"] {
            width: 100%;
            margin-bottom: 15px;
            padding: 8px;
            box-sizing: border-box;
        }
        button {
            padding: 10px 20px;
            background-color: #5cb85c;
            border: none;
            color: white;
            font-size: 16px;
            cursor: pointer;
            display: block;
            margin: 10px auto;
        }
        button:hover {
            background-color: #4cae4c;
        }
        #result, #hintText {
            margin-top: 20px;
            background-color: #f8f8f8;
            padding: 15px;
            border-radius: 5px;
        }
        #problemText {
            font-size: 18px;
            margin-bottom: 20px;
            border: 1px solid #ddd;
            padding: 10px;
            border-radius: 5px;
            background-color: #f9f9f9;
            min-height: 100px;
        }
        #cameraAndImageContainer {
            display: flex;
            justify-content: space-between;
            gap: 20px;
            margin-top: 20px;
        }
        #videoContainer, #imageContainer {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #cameraStream {
            width: 100%;
            height: auto;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        #capturedImage {
            max-width: 100%;
            margin-top: 10px;
            display: none;
        }
    </style>
    <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
    <script>
        window.MathJax = {
            tex: {
                inlineMath: [['$', '$'], ['\\(', '\\)']]
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
    <h1>ÔN LUYỆN TOÁN LỚP 6 - TRUNG TÂM ÁNH DƯƠNG</h1>

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

        <div id="problemContainer">
            <label for="problemText">Đề bài:</label>
            <div id="problemText"></div>
        </div>

        <div id="bottomControls">
            <button id="submitBtn">Chấm Bài</button>
            <button id="hintBtn">Gợi ý</button>
            <button id="deleteAllBtn" class="delete">Xóa tất cả</button>
        </div>

        <div id="result"></div>

        <div id="cameraAndImageContainer">
            <div id="videoContainer">
                <video id="cameraStream" autoplay playsinline></video>
                <button id="captureButton">Chụp ảnh</button>
            </div>

            <div id="imageContainer">
                <canvas id="photoCanvas" style="display: none;"></canvas>
                <img id="capturedImage" alt="Ảnh đã chụp">
            </div>
        </div>
    </div>

    <script>
        const SHEET_ID = '175acnaYklfdCc_UJ7B3LJgNaUJpfrIENxn6LN76QADM';
        const SHEET_NAME = 'Toan6';
        const SHEET_URL = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?sheet=${SHEET_NAME}&tq=&tqx=out:json`;
        const API_KEYS = [
            'AIzaSyCzh6doVzV7Dbmbz60B9pNUQIel2N6KEcI',
            'AIzaSyBVQcUrVTtwKeAAsFR8ENM8-kgZl8CsUM0',
            'AIzaSyCmY4FdhZ4qSN6HhBtldgQgSNbDlZ4J1ug',
            'AIzaSyAkX3rMYxN_-aO95QKMPy-OLIV62esaANU',
            'AIzaSyDtmacgYKn1PBgCVWkReF9Kyn6vC4DKZmg'
        ];

        let currentProblemIndex = 0;
        let problems = [];
        let currentStudentId = '';
        let base64Image = '';
        let currentApiKeyIndex = 0;

        function getNextApiKey() {
            const apiKey = API_KEYS[currentApiKeyIndex];
            currentApiKeyIndex = (currentApiKeyIndex + 1) % API_KEYS.length;
            return apiKey;
        }

        async function fetchProblems() {
            try {
                const response = await fetch(SHEET_URL);
                const text = await response.text();
                const jsonData = JSON.parse(text.match(/google\.visualization\.Query\.setResponse\(([\s\S\w]+)\)/)[1]);
                problems = jsonData.table.rows.map(row => ({
                    index: row.c[0]?.v,
                    problem: row.c[1]?.v.replace(/\n/g, '<br>')
                }));
            } catch (error) {
                console.error('Lỗi khi tải bài toán:', error);
            }
        }

        async function generateHint(problemText) {
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-002:generateContent?key=${getNextApiKey()}`;
            const promptText = `Đề bài:\n${problemText}\n\nHãy đưa ra gợi ý ngắn gọn để giúp học sinh giải bài toán này.`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ contents: [{ parts: [{ text: promptText }] }] })
                });
                const data = await response.json();
                return data?.candidates?.[0]?.content?.parts?.[0]?.text || 'Không có gợi ý.';
            } catch (error) {
                console.error('Lỗi khi tạo gợi ý:', error);
                return 'Lỗi khi tạo gợi ý.';
            }
        }

        async function gradeWithGemini(base64Image, problemText, studentId) {
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-pro-002:generateContent?key=${getNextApiKey()}`;
            const promptText = `Học sinh: ${studentId}\nĐề bài:\n${problemText}\nHãy thực hiện các bước sau:\n1. Nhận diện bài làm.\n2. Giải bài.\n3. So sánh và chấm điểm.\n4. Đưa ra nhận xét chi tiết.`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [
                            { parts: [{ text: promptText }] },
                            { inline_data: { mime_type: 'image/jpeg', data: base64Image } }
                        ]
                    })
                });
                const data = await response.json();
                return data?.candidates?.[0]?.content?.parts?.[0]?.text || 'Không có kết quả chấm bài.';
            } catch (error) {
                console.error('Lỗi khi chấm bài:', error);
                return 'Lỗi khi chấm bài.';
            }
        }

        function displayProblem(index) {
            const problem = problems.find(p => parseInt(p.index) === index);
            document.getElementById('problemText').innerHTML = problem ? problem.problem : 'Không tìm thấy bài toán.';
        }

        function startCamera() {
            navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
                document.getElementById('cameraStream').srcObject = stream;
            }).catch(console.error);
        }

        document.getElementById('loginBtn').addEventListener('click', () => {
            currentStudentId = document.getElementById('studentId').value;
            if (currentStudentId) {
                document.getElementById('loginContainer').style.display = 'none';
                document.getElementById('mainContent').style.display = 'block';
                fetchProblems();
                startCamera();
            } else {
                alert('Vui lòng nhập mã học sinh');
            }
        });

        document.getElementById('randomProblemBtn').addEventListener('click', () => {
            const randomIndex = Math.floor(Math.random() * problems.length);
            displayProblem(randomIndex);
        });

        document.getElementById('selectProblemBtn').addEventListener('click', () => {
            const index = parseInt(document.getElementById('problemIndexInput').value);
            displayProblem(index);
        });

        document.getElementById('captureButton').addEventListener('click', () => {
            const video = document.getElementById('cameraStream');
            const canvas = document.getElementById('photoCanvas');
            const context = canvas.getContext('2d');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            context.drawImage(video, 0, 0, canvas.width, canvas.height);
            base64Image = canvas.toDataURL('image/jpeg').split(',')[1];
            document.getElementById('capturedImage').src = `data:image/jpeg;base64,${base64Image}`;
            document.getElementById('capturedImage').style.display = 'block';
        });

        document.getElementById('submitBtn').addEventListener('click', async () => {
            const problemText = document.getElementById('problemText').innerHTML;
            if (!base64Image || !problemText) {
                alert('Vui lòng nhập đủ dữ liệu.');
                return;
            }
            const feedback = await gradeWithGemini(base64Image, problemText, currentStudentId);
            document.getElementById('result').innerHTML = feedback;
        });

        document.getElementById('hintBtn').addEventListener('click', async () => {
            const problemText = document.getElementById('problemText').innerHTML;
            const hint = await generateHint(problemText);
            alert(hint);
        });

        document.getElementById('deleteAllBtn').addEventListener('click', () => {
            document.getElementById('problemText').innerHTML = '';
            document.getElementById('capturedImage').src = '';
            document.getElementById('capturedImage').style.display = 'none';
            base64Image = '';
        });

        (function protectContent() {
            document.addEventListener('contextmenu', e => e.preventDefault());
            document.addEventListener('keydown', e => {
                if (e.key === 'F12' || (e.ctrlKey && ['U', 'S', 'P', 'A', 'C'].includes(e.key))) {
                    e.preventDefault();
                }
            });
            setInterval(() => debugger, 100);
        })();
    </script>
</body>
</html>
