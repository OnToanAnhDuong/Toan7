<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Học sinh tự ôn luyện Toán - Bài tập ngẫu nhiên</title>
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
        input[type="text"], input[type="file"] {
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
            margin: 10px auto;
            display: block;
        }
        button:hover {
            background-color: #4cae4c;
        }
        #result, #hintText {
            margin-top: 20px;
            white-space: pre-wrap;
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
            white-space: pre-wrap;
        }
        #problemContainer, #progressContainer {
            position: relative;
            margin-bottom: 20px;
        }
        #randomProblemBtn, #loginBtn {
            display: block;
            width: 50%;
            margin-bottom: 10px;
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
        }
        #randomProblemBtn:hover, #loginBtn:hover {
            background-color: #0056b3;
        }
        #progress {
            background-color: #e9ecef;
            padding: 15px;
            border-radius: 5px;
            margin-bottom: 20px;
        }
        .message-box {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            padding: 20px;
            border-radius: 5px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            z-index: 1000;
            max-width: 80%;
            width: 400px;
        }
        .message-box-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: rgba(0,0,0,0.5);
            z-index: 999;
        }
	#cameraContainer {
    margin-top: 20px;
    text-align: center;
}

#cameraStream {
    width: 100%;
    max-width: 400px;
    border: 1px solid #ddd;
    border-radius: 5px;
}

#captureButton {
    margin-top: 10px;
    padding: 10px 20px;
    background-color: #007bff;
    border: none;
    color: white;
    font-size: 16px;
    cursor: pointer;
}

#captureButton:hover {
    background-color: #0056b3;
}

    </style>

    <!-- Thêm MathJax -->
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
    <h1>Học sinh tự ôn luyện Toán - Bài tập ngẫu nhiên</h1>
    
    <div id="loginContainer">
        <input type="text" id="studentId" placeholder="Nhập mã học sinh">
        <button id="loginBtn">Đăng nhập</button>
    </div>

    <div id="mainContent" style="display: none;">
        
	<div id="selectProblemContainer">
    	<label for="problemIndexInput">Nhập số thứ tự bài cần chọn:</label>
   	 <input type="number" id="problemIndexInput" placeholder="Nhập số thứ tự (1, 2, ...)">
   	 <button id="selectProblemBtn">Hiển thị bài tập</button>
	</div>
        
	<div id="problemContainer">
            <button id="randomProblemBtn">Lấy đề bài ngẫu nhiên</button>
            <label for="problemText">Đề bài:</label>
            <div id="problemText"></div>
        </div>
        
        <label for="studentImage">Ảnh bài làm của học sinh:</label>
        <input type="file" id="studentImage" accept="image/*">
	<label for="cameraStream">Hoặc chụp ảnh từ camera:</label>
<div id="cameraContainer">
    <video id="cameraStream" autoplay playsinline></video>
    <button id="captureButton">Chụp ảnh</button>
    <canvas id="photoCanvas" style="display: none;"></canvas>
    <img id="capturedImage" alt="Ảnh đã chụp" style="margin-top: 10px; max-width: 100%; display: none;">
</div>

        
        <button id="submitBtn">Chấm Bài</button>
        <button id="hintBtn">Gợi ý</button>

        <div id="result"></div>
    </div>

    <script>
        const SHEET_ID = '175acnaYklfdCc_UJ7B3LJgNaUJpfrIENxn6LN76QADM';
        const SHEET_NAME = 'Toan9';
        const SHEET_URL = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?sheet=${SHEET_NAME}&tq=&tqx=out:json`;

        const API_KEYS = ['AIzaSyCzh6doVzV7Dbmbz60B9pNUQIel2N6KEcI', 'AIzaSyBVQcUrVTtwKeAAsFR8ENM8-kgZl8CsUM0', 'AIzaSyCmY4FdhZ4qSN6HhBtldgQgSNbDlZ4J1ug', 'AIzaSyAkX3rMYxN_-aO95QKMPy-OLIV62esaANU', 'AIzaSyDtmacgYKn1PBgCVWkReF9Kyn6vC4DKZmg', 'AIzaSyAusgvzZkUPT9lHoB7vzZW_frx-Z0xIxU8', 'AIzaSyBBNxoJh9UZXbc4shgRc7nUiJKya3JR2eI', 'AIzaSyAru8K7uUTD85FOCmrNESQmQYh-gfFCOZ8', 'AIzaSyAkDbRl7iBYWhc00KZ9dZL1_l0cobcC0ak', 'AIzaSyAJ9DpLy4uLfbFoyh7IhW9N0uk9YkBEUY4'];

        
        let currentKeyIndex = 0;
        let problems = [];
        let currentProblem = null;
	let completedProblems = 0;  // Khai báo số bài đã giải
        let totalScore = 0;  // Khai báo tổng điểm
        let currentProblemScore = 0; // Điểm của bài hiện tại
	let base64Image = ''; // Đặt ở đầu script để có phạm vi toàn cục
        let currentStudentId = null;
        let currentHint = '';
        let studentName = '';
	let currentProblemIndex = 0; // Bắt đầu từ bài đầu tiên
        function getNextApiKey() {
            const key = API_KEYS[currentKeyIndex];
            currentKeyIndex = (currentKeyIndex + 1) % API_KEYS.length;
            return key;
        }

        async function makeApiRequest(apiUrl, requestBody) {
            let attempts = 0;
            while (attempts < API_KEYS.length) {
                const apiKey = getNextApiKey();
                try {
                    const response = await fetch(`${apiUrl}?key=${apiKey}`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(requestBody)
                    });
                    
                    if (response.ok) {
                        return await response.json();
                    } else if (response.status === 403) {
                        console.log(`API key expired or invalid: ${apiKey}`);
                        attempts++;
                    } else {
                        throw new Error(`HTTP error! status: ${response.status}`);
                    }
                } catch (error) {
                    console.error('Error making API request:', error);
                    attempts++;
                }
            }
            throw new Error('All API keys have been exhausted or are invalid.');
        }

      async function fetchProblems() {
    try {
        const response = await fetch(SHEET_URL);
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        const text = await response.text();
        const jsonData = JSON.parse(text.match(/google\.visualization\.Query\.setResponse\(([\s\S\w]+)\)/)[1]);
        problems = parseGoogleSheetData(jsonData);
        console.log('Đã tải xong bài tập:', problems);
    } catch (error) {
        console.error('Lỗi khi tải bài toán:', error);
        document.getElementById('problemText').textContent = 'Không thể tải bài toán.';
    }
}



        function parseGoogleSheetData(jsonData) {
    const data = jsonData.table.rows;
    return data.map(row => ({
        index: row.c[0]?.v || '', // Cột thứ tự
        problem: row.c[1]?.v.replace(/\r\n|\r|\n/g, '\n') || '' // Cột đề bài
    })).filter(item => item.problem && item.index);
}


        function displayNextProblem() {
    if (problems.length > 0) {
        // Nếu chỉ số hiện tại vượt quá số bài, quay lại bài đầu tiên (tuỳ chọn)
        if (currentProblemIndex >= problems.length) {
            currentProblemIndex = 0;
        }

        // Lấy bài tập theo thứ tự
        currentProblem = problems[currentProblemIndex];
        currentProblemIndex++; // Tăng chỉ số lên bài tiếp theo

        document.getElementById('problemText').innerHTML = formatProblemText(currentProblem.problem);
        MathJax.typesetPromise([document.getElementById('problemText')]).catch(function (err) {
            console.error('MathJax rendering error:', err);
        });
    } else {
        document.getElementById('problemText').textContent = 'Không có bài toán nào.';
    }
}
function displayProblemByIndex(index) {
    if (problems.length === 0) {
        document.getElementById('problemText').textContent = 'Danh sách bài tập chưa được tải. Vui lòng thử lại.';
        return;
    }

    const selectedProblem = problems.find(problem => parseInt(problem.index) === parseInt(index));
    if (selectedProblem) {
        document.getElementById('problemText').innerHTML = formatProblemText(selectedProblem.problem);
        MathJax.typesetPromise([document.getElementById('problemText')]).catch(function (err) {
            console.error('MathJax rendering error:', err);
        });
    } else {
        document.getElementById('problemText').textContent = `Không tìm thấy bài tập với số thứ tự ${index}.`;
    }
}


        function formatProblemText(problemText) {
            return problemText.replace(/\n/g, '<br>').replace(/([a-d]\))/g, '<br>$1');
        }
function checkCameraAccess() {
    navigator.mediaDevices.enumerateDevices()
        .then(devices => {
            const videoDevices = devices.filter(device => device.kind === 'videoinput');
            if (videoDevices.length === 0) {
                alert('Không tìm thấy thiết bị camera.');
            }
        })
        .catch(error => console.error('Lỗi khi kiểm tra thiết bị camera:', error));
}


        // Hàm cập nhật số bài đã làm và điểm trung bình
        function updateProgress(newScore) {
            completedProblems++;
            totalScore += newScore;
            let averageScore = totalScore / completedProblems;
            document.getElementById("completedProblems").textContent = completedProblems;
            document.getElementById("averageScore").textContent = averageScore.toFixed(2);
        }

        // Xử lý khi học sinh giải bài và bấm chấm bài
        document.getElementById('submitBtn').addEventListener('click', function() {
            // Giả sử điểm của bài hiện tại đã được tính là currentProblemScore
            updateProgress(currentProblemScore);
        });

        // Xử lý khi học sinh đăng nhập
        document.getElementById('loginBtn').addEventListener('click', function() {
            const studentId = document.getElementById('studentId').value;
            if (studentId) {
                currentStudentId = studentId;
                document.getElementById('loginContainer').style.display = 'none';
                document.getElementById('mainContent').style.display = 'block';
            } else {
                alert('Vui lòng nhập mã học sinh');
            }
        });

        // Lấy bài toán ngẫu nhiên
        async function fetchProblems() {
    try {
        const response = await fetch(SHEET_URL);
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        const text = await response.text();
        const jsonData = JSON.parse(text.match(/google\.visualization\.Query\.setResponse\(([\s\S\w]+)\)/)[1]);
        problems = parseGoogleSheetData(jsonData);
        console.log('Đã tải xong bài tập:', problems);
    } catch (error) {
        console.error('Error fetching problems:', error);
        document.getElementById('problemText').textContent = 'Lỗi khi tải bài toán. Vui lòng thử lại sau.';
    }
}

        async function generateSimilarProblem(originalProblem) {
            const apiUrl = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-002:generateContent';
            const promptText = `
            Bạn hãy tạo một bài toán tương tự bài sau bằng cách thay đổi các số liệu một cách phù hợp, nhưng giữ nguyên cấu trúc và dạng toán:

            Bài toán gốc:
            ${originalProblem}

            Bài toán mới:
            `;
            const requestBody = {
                contents: [
                    {
                        parts: [
                            { text: promptText }
                        ]
                    }
                ]
            };
            
            try {
                const data = await makeApiRequest(apiUrl, requestBody);
                return data.candidates[0].content.parts[0].text.trim();
            } catch (error) {
                console.error('Lỗi:', error);
                return `Đã xảy ra lỗi: ${error.message}`;
            }
        }

        async function generateHint(problemText) {
            const apiUrl = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-002:generateContent';
            const promptText = `
            Đề bài:
            ${problemText}

            Hãy đưa ra một gợi ý ngắn gọn để giúp học sinh giải bài toán này. Gợi ý nên:
            1. Không cung cấp đáp án trực tiếp
            2. Hướng dẫn học sinh về hướng giải quyết hoặc công thức cần sử dụng
            3. Khuyến khích học sinh suy nghĩ độc lập
            4. Phân chia gợi ý theo cấu trúc của đề bài (nếu có các phần a, b, c)

            Gợi ý:
            `;
            const requestBody = {
                contents: [
                    {
                        parts: [
                            { text: promptText }
                        ]
                    }
                ]
            };
            
            try {
                const data = await makeApiRequest(apiUrl, requestBody);
                let hint = data.candidates[0].content.parts[0].text.trim();
                hint = hint.replace(/([a-d]\))/g, '\n$1');
                return hint;
            } catch (error) {
                console.error('Lỗi:', error);
                return `Đã xảy ra lỗi khi tạo gợi ý: ${error.message}`;
            }
        }

        async function gradeWithGemini(base64Image, problemText, studentId) {
            const apiUrl = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-pro-002:generateContent';
            const promptText = `
            Học sinh: ${studentId}
            Đề bài:
            ${problemText}
             Hãy thực hiện các bước sau:
            1. Nhận diện và gõ lại bài làm của học sinh từ hình ảnh thành văn bản một cách chính xác, tất cả công thức Toán viết dưới dạng Latex, bọc trong dấu $, không tự suy luận nội dung hình ảnh, chỉ gõ lại chính xác các nội dung nhận diện được từ hình ảnh
            2. Giải bài toán và cung cấp lời giải chi tiết cho từng phần.
            3. So sánh bài làm của học sinh với đáp án đúng, chấm chi tiết từng bước làm đến kết quả
            4. Chấm điểm bài làm của học sinh trên thang điểm 10, cho 0 điểm với bài giải không đúng yêu cầu đề bài. Giải thích chi tiết cách tính điểm cho từng phần.
            5. Đưa ra nhận xét chi tiết và đề xuất cải thiện.
            6. Kiểm tra lại kết quả chấm điểm và đảm bảo tính nhất quán giữa bài làm, lời giải, và điểm số.
            Kết quả trả về cần có định dạng sau:
            Bài làm của học sinh: [Bài làm được nhận diện từ hình ảnh]
            Lời giải chi tiết: [Lời giải từng bước]
            Chấm điểm: [Giải thích cách chấm điểm cho từng phần]
            Điểm số: [Điểm trên thang điểm 10]
            Nhận xét: [Nhận xét chi tiết]
            Đề xuất cải thiện: [Các đề xuất cụ thể]
            Chú ý:
            - Điểm số phải là một số từ 0 đến 10, có thể có một chữ số thập phân.
            - Hãy đảm bảo tính chính xác và khách quan trong việc chấm điểm và nhận xét.
            - Nếu có sự không nhất quán giữa bài làm và điểm số, hãy giải thích rõ lý do.
            `;

            const requestBody = {
                contents: [
                    {
                        parts: [
                            { text: promptText },
                            { inline_data: { mime_type: "image/jpeg", data: base64Image } }
                        ]
                    }
                ]
            };
            
            try {
                const data = await makeApiRequest(apiUrl, requestBody);
                const response = data?.candidates?.[0]?.content?.parts?.[0]?.text;
                if (!response) {
                    throw new Error('Không nhận được phản hồi hợp lệ từ API');
                }
                const studentAnswer = response.match(/Bài làm của học sinh: ([\s\S]*?)(?=\nLời giải chi tiết:)/)?.[1]?.trim() || '';
                const feedback = response.replace(/Bài làm của học sinh: [\s\S]*?\n/, '');
                const score = parseFloat(response.match(/Điểm số: (\d+(\.\d+)?)/)?.[1] || '0');
                return { studentAnswer, feedback, score };

            } catch (error) {
                console.error('Lỗi:', error);
                return { studentAnswer: '', feedback: `Đã xảy ra lỗi: ${error.message}`, score: 0 };
            }
        }

        async function submitToGoogleForm(score, studentId, problemText, studentAnswer, feedback, studentName) {
            const formId = '1FAIpQLSd4HefrKz-FAyo4YCttFzI9j9wEYQ7IVL38uZe8EwMtTj6KCw';
            const entryName = 'entry.854745128';
            const entryProblem = 'entry.1086866640';
            const entryAnswer = 'entry.939840295';
            const entryFeedback = 'entry.34713471';
	    const entryScore = 'entry.413593378';
	    const entryTen = 'entry.1135916403';

            const formData = new URLSearchParams();
            formData.append(entryName, `${studentId}`);
            formData.append(entryProblem, problemText || 'Không có đề bài');
            formData.append(entryAnswer, studentAnswer || 'Không có bài làm');
            formData.append(entryFeedback, feedback || 'Không có phản hồi');
	    formData.append(entryScore, score || '0');
	    formData.append(entryTen, `${studentName}`);
 

            try {
                const response = await fetch(`https://docs.google.com/forms/d/e/${formId}/formResponse`, {
                    method: 'POST',
                    mode: 'no-cors',
                    body: formData
                });
                console.log('Dữ liệu đã được gửi đến Google Form');
                return true;
            } catch (error) {
                console.error('Lỗi khi gửi dữ liệu đến Google Form:', error);
                return false;
            }
        }

        function getBase64(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = () => resolve(reader.result.split(',')[1]);
                reader.onerror = error => reject(error);
            });
        }

        async function displayRandomProblem() {
            if (problems.length > 0) {
                const randomIndex = Math.floor(Math.random() * problems.length);
                currentProblem = problems[randomIndex];
                let problemText = currentProblem.problem;
                problemText = await generateSimilarProblem(problemText);
                problemText = formatProblemText(problemText);
                document.getElementById('problemText').innerHTML = problemText;
                currentHint = await generateHint(problemText);
                MathJax.typesetPromise([document.getElementById('problemText')]).catch(function (err) {
                    console.error('MathJax rendering error:', err);
                });
            } else {
                document.getElementById('problemText').textContent = 'Không có bài toán nào.';
            }
        }

        async function checkStudentId(studentId) {
            const progressUrl = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?sheet=StudentProgress&tq=&tqx=out:json`;
            try {
                const response = await fetch(progressUrl);
                const text = await response.text();
                const jsonData = JSON.parse(text.match(/google\.visualization\.Query\.setResponse\(([\s\S\w]+)\)/)[1]);
                const rows = jsonData.table.rows;
                
                const studentRow = rows.find(row => row.c[0]?.v?.toString() === studentId);
                if (studentRow) {
                    studentName = studentRow.c[3]?.v || '';
                    return true;
                }
                return false;
            } catch (error) {
                console.error('Lỗi khi kiểm tra mã học sinh:', error);
                return false;
            }
        }

        async function updateProgress(score) {
    if (!currentStudentId) {
        console.error('No currentStudentId provided.');
        return;
    }

    console.log('Current Student ID:', currentStudentId);

    const progressUrl = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?sheet=StudentProgress&tq=&tqx=out:json`;
    try {
        const response = await fetch(progressUrl);
        if (!response.ok) {
            throw new Error(`Failed to fetch progress: ${response.statusText}`);
        }

        const text = await response.text();
        console.log('Response from Google Sheet:', text);

        const jsonData = JSON.parse(text.match(/google\.visualization\.Query\.setResponse\(([\s\S\w]+)\)/)[1]);
        const rows = jsonData.table.rows;
        console.log('Parsed Rows:', rows);

        let studentRow = rows.find(row => row.c[0]?.v.trim().toLowerCase() === currentStudentId.trim().toLowerCase());
        if (studentRow) {
            console.log('Student Row Data:', studentRow);

            let completedProblems = parseInt(studentRow.c[1]?.v || '0') + 1; // Số bài đã làm
            let totalScore = parseFloat(studentRow.c[2]?.v || '0') + score; // Tổng điểm
            let averageScore = totalScore / completedProblems; // Điểm trung bình

            console.log('Computed Values:', { completedProblems, totalScore, averageScore });

            // Cập nhật giao diện
            const completedElem = document.getElementById('completedProblems');
            const averageElem = document.getElementById('averageScore');
            if (completedElem && averageElem) {
                completedElem.textContent = completedProblems; // Hiển thị số bài
                averageElem.textContent = averageScore.toFixed(2); // Hiển thị điểm trung bình
            } else {
                console.error('Progress elements not found in DOM.');
            }

            // Cập nhật Google Sheet
            await updateGoogleSheetData(currentStudentId, completedProblems, totalScore);

        } else {
            console.error('Student ID not found in Google Sheet.');
            document.getElementById('completedProblems').textContent = '0';
            document.getElementById('averageScore').textContent = '0';
        }
   	 } catch (error) {
        console.error('Error updating progress:', error.message, error.stack);
   	 }
	}



        function showMessageBox(message) {
            const overlay = document.createElement('div');
            overlay.className = 'message-box-overlay';
            
            const messageBox = document.createElement('div');
            messageBox.className = 'message-box';
            messageBox.innerHTML = `
                <p>${message}</p>
                <button onclick="this.parentElement.parentElement.remove()">Đóng</button>
            `;
            
            overlay.appendChild(messageBox);
            document.body.appendChild(overlay);
        }

       document.getElementById('submitBtn').addEventListener('click', async () => {
    const problemText = document.getElementById('problemText')?.innerHTML?.trim();
    const studentFileInput = document.getElementById('studentImage');

    if (!problemText) {
        alert('Vui lòng đợi đề bài được tải.');
        return;
    }

    if (!base64Image && !studentFileInput?.files?.length) {
        alert('Vui lòng chọn hoặc chụp ảnh bài làm của học sinh.');
        return;
    }

    // Ưu tiên ảnh từ camera, nếu không có thì sử dụng ảnh tải lên từ file
    const imageToProcess = base64Image || (studentFileInput.files.length > 0 ? await getBase64(studentFileInput.files[0]) : null);

    if (!imageToProcess) {
        alert('Không thể lấy ảnh bài làm. Vui lòng thử lại.');
        return;
    }

    try {
        document.getElementById('result').innerText = 'Đang xử lý...';

        // Gửi ảnh để chấm bài
        const { studentAnswer, feedback, score } = await gradeWithGemini(imageToProcess, problemText, currentStudentId);

        const submitted = await submitToGoogleForm(score, currentStudentId, problemText, studentAnswer, feedback, studentName);

        if (submitted) {
            document.getElementById('result').innerHTML = feedback;
            MathJax.typesetPromise([document.getElementById('result')]).catch(err => console.error('MathJax rendering error:', err));
            await updateProgress(score);
        } else {
            throw new Error('Không thể gửi dữ liệu đến Google Form.');
        }
    } catch (error) {
        console.error('Lỗi:', error);
        document.getElementById('result').innerText = `Đã xảy ra lỗi: ${error.message}. Vui lòng thử lại sau.`;
    }
});

        document.getElementById('randomProblemBtn').addEventListener('click', () => {
            displayRandomProblem();
        });

        document.getElementById('hintBtn').addEventListener('click', () => {
            if (currentHint) {
                showMessageBox(currentHint);
            } else {
                alert("Chưa có gợi ý cho bài toán này.");
            }
        });

        document.getElementById('loginBtn').addEventListener('click', async () => {
            const studentId = document.getElementById('studentId').value.trim();
            if (studentId) {
                const isValidStudent = await checkStudentId(studentId);
                if (isValidStudent) {
                    currentStudentId = studentId;
                    document.getElementById('loginContainer').style.display = 'none';
                    document.getElementById('mainContent').style.display = 'block';
                    document.getElementById('randomProblemBtn').textContent = `Lấy đề bài ngẫu nhiên (${currentStudentId})`;
                    await fetchProblems();
                    await updateProgress(0);
                } else {
                    alert('Mã học sinh không hợp lệ. Vui lòng thử lại.');
                }
            } else {
                alert('Vui lòng nhập mã học sinh');
            }
        });

	document.getElementById('selectProblemBtn').addEventListener('click', () => {
    	const problemIndexInput = document.getElementById('problemIndexInput').value;
   	 if (problemIndexInput) {
        displayProblemByIndex(problemIndexInput);
   	 } else {
        alert('Vui lòng nhập số thứ tự bài cần chọn.');
    	}
	});
	document.addEventListener('DOMContentLoaded', () => {
    const video = document.getElementById('cameraStream');
    const captureButton = document.getElementById('captureButton');
    const canvas = document.getElementById('photoCanvas');
    const img = document.getElementById('capturedImage');
    let base64Image = '';

    checkCameraAccess(); // Kiểm tra thiết bị
    startCamera(); // Bắt đầu camera

    async function startCamera() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: true });
            video.srcObject = stream;
        } catch (err) {
            console.error('Lỗi khi mở camera:', err);
            if (err.name === 'NotAllowedError') {
                alert('Bạn chưa cấp quyền truy cập camera.');
            } else if (err.name === 'NotFoundError') {
                alert('Không tìm thấy thiết bị camera.');
            } else {
                alert('Lỗi không xác định. Vui lòng thử lại.');
            }
        }
    }

    function checkCameraAccess() {
        navigator.mediaDevices.enumerateDevices()
            .then(devices => {
                const videoDevices = devices.filter(device => device.kind === 'videoinput');
                if (videoDevices.length === 0) {
                    alert('Không tìm thấy thiết bị camera.');
                }
            })
            .catch(error => console.error('Lỗi khi kiểm tra thiết bị camera:', error));
    }

    captureButton.addEventListener('click', () => {
        const context = canvas.getContext('2d');
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        context.drawImage(video, 0, 0, canvas.width, canvas.height);
        base64Image = canvas.toDataURL('image/png');
        img.src = base64Image;
        img.style.display = 'block';
        console.log('Ảnh chụp (Base64):', base64Image);
    });
});



            // Các đoạn mã ngăn chặn xem mã nguồn và bảo vệ nội dung
        (function() {
            // Vô hiệu hóa nhấp chuột phải
            document.addEventListener('contextmenu', function(e) {
                e.preventDefault();
                return false;
            });

            // Vô hiệu hóa các phím tắt
            document.addEventListener('keydown', function(e) {
                // Vô hiệu hóa F12
                if (e.key === 'F12' || e.keyCode === 123) {
                    e.preventDefault();
                    return false;
                }
                
                if (e.ctrlKey) {
                    // Vô hiệu hóa Ctrl+U
                    if (e.key === 'u' || e.keyCode === 'U'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                    // Vô hiệu hóa Ctrl+S
                    if (e.key === 's' || e.keyCode === 'S'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                    // Vô hiệu hóa Ctrl+P
                    if (e.key === 'p' || e.keyCode === 'P'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                    // Vô hiệu hóa Ctrl+A
                    if (e.key === 'a' || e.keyCode === 'A'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                    // Vô hiệu hóa Ctrl+C
                    if (e.key === 'c' || e.keyCode === 'C'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                }

                if (e.ctrlKey && e.shiftKey) {
                    // Vô hiệu hóa Ctrl+Shift+I
                    if (e.key === 'i' || e.keyCode === 'I'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                    // Vô hiệu hóa Ctrl+Shift+J
                    if (e.key === 'j' || e.keyCode === 'J'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                    // Vô hiệu hóa Ctrl+Shift+C
                    if (e.key === 'c' || e.keyCode === 'C'.charCodeAt(0)) {
                        e.preventDefault();
                        return false;
                    }
                }
            });

            // Vô hiệu hóa kéo thả
            document.addEventListener('dragstart', function(e) {
                e.preventDefault();
                return false;
            });

            // Vô hiệu hóa cắt
            document.addEventListener('cut', function(e) {
                e.preventDefault();
                return false;
            });

            // Vô hiệu hóa lưu trang
            window.addEventListener('beforeunload', function(e) {
                e.preventDefault();
                return false;
            });

            // Vô hiệu hóa công cụ phát triển
            setInterval(function() {
                debugger;
            }, 100);

            // Kiểm tra và vô hiệu hóa devtools
            let devtools = function() {};
            devtools.toString = function() {
                this.opened = true;
                return '';
            };
            
            setInterval(function() {
                console.log(devtools);
                console.clear();
            }, 1000);

            // Vô hiệu hóa view-source
            if(window.location.href.indexOf('view-source:') !== -1) {
                window.location.href = window.location.href.replace('view-source:', '');
            }

            // Ngăn chặn inspect element
            document.addEventListener('keydown', function(e) {
                if(e.key === 'F12' || 
                   (e.ctrlKey && e.shiftKey && (e.key === 'I' || e.key === 'i' || e.key === 'J' || e.key === 'j' || e.key === 'C' || e.key === 'c'))) {
                    e.preventDefault();
                }
            });

            // Vô hiệu hóa phím Print Screen
            document.addEventListener('keyup', function(e) {
                if(e.key === 'PrintScreen') {
                    navigator.clipboard.writeText('');
                }
            });

            // Thêm watermark để ngăn chặn chụp màn hình (tùy chọn)
            function addWatermark() {
                const watermark = document.createElement('div');
                watermark.style.cssText = `
                    position: fixed;
                    top: 50%;
                    left: 50%;
                    transform: translate(-50%, -50%) rotate(-45deg);
                    font-size: 60px;
                    opacity: 0.1;
                    pointer-events: none;
                    z-index: 9999;
                    white-space: nowrap;
                `;
                watermark.textContent = 'BÀI KIỂM TRA - KHÔNG SAO CHÉP';
                document.body.appendChild(watermark);
            }
            addWatermark();
        })();

    </script>
</body>
</html>
