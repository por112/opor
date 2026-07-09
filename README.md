<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Talking Forest - English Sentence Game</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Chakra+Petch:wght@400;700&display=swap');
        body {
            font-family: 'Chakra+Petch', sans-serif;
            background-color: #1a2e1a;
        }
        canvas {
            image-rendering: pixelated;
        }
    </style>
</head>
<body class="flex flex-col items-center justify-center min-h-screen p-4 text-white select-none">

    <header class="text-center mb-4">
        <h1 class="text-4xl font-bold text-green-4xl drop-shadow-[0_4px_4px_rgba(0,0,0,0.8)] text-green-400">🌳 The Talking Forest 🌳</h1>
        <p class="text-sm text-gray-3xl">ฝึกแต่งประโยคภาษาอังกฤษ (Subject + Verb + Object)</p>
    </header>

    <main class="relative bg-stone-900 border-4 border-amber-700 rounded-2xl overflow-hidden shadow-2xl w-full max-w-4xl aspect-[16/9] flex flex-col">
        
        <div id="start-screen" class="absolute inset-0 z-10 bg-gradient-to-b from-green-900 to-emerald-950 flex flex-col items-center justify-center p-6 text-center">
            <h2 class="text-3xl font-bold text-amber-4xl mb-2">ยินดีต้อนรับสู่นักสำรวจตัวน้อย!</h2>
            <p class="text-gray-300 mb-6">เลือกตัวละครและระดับความยากเพื่อเริ่มการผจญภัยในป่าต้องมนต์</p>
            
            <div class="mb-6">
                <p class="text-lg font-semibold mb-2 text-amber-300">1. เลือกคู่หูของคุณ:</p>
                <div class="flex gap-4 justify-center">
                    <button onclick="selectCharacter('🐒', 'ลิงน้อย (Monkey)')" id="char-btn-0" class="char-btn p-4 bg-stone-800 border-2 border-amber-600 rounded-xl text-4xl hover:bg-amber-900 transition transform hover:scale-105 active:scale-95">🐒</button>
                    <button onclick="selectCharacter('🦜', 'นกแก้ว (Parrot)')" id="char-btn-1" class="char-btn p-4 bg-stone-800 border-2 border-transparent rounded-xl text-4xl hover:bg-amber-900 transition transform hover:scale-105 active:scale-95">🦜</button>
                    <button onclick="selectCharacter('🐯', 'เสือโคร่ง (Tiger)')" id="char-btn-2" class="char-btn p-4 bg-stone-800 border-2 border-transparent rounded-xl text-4xl hover:bg-amber-900 transition transform hover:scale-105 active:scale-95">🐯</button>
                    <button onclick="selectCharacter('🤠', 'นักสำรวจ (Explorer)')" id="char-btn-3" class="char-btn p-4 bg-stone-800 border-2 border-transparent rounded-xl text-4xl hover:bg-amber-900 transition transform hover:scale-105 active:scale-95">🤠</button>
                </div>
            </div>

            <div class="mb-8">
                <p class="text-lg font-semibold mb-2 text-amber-300">2. เลือกระดับความยาก:</p>
                <div class="flex gap-4 justify-center">
                    <button onclick="selectDifficulty('medium')" id="diff-btn-medium" class="px-6 py-2 bg-green-700 text-white font-bold rounded-lg border-2 border-white hover:bg-green-600 transition">ปานกลาง (Medium)</button>
                    <button onclick="selectDifficulty('hard')" id="diff-btn-hard" class="px-6 py-2 bg-red-800 text-white font-bold rounded-lg border-2 border-transparent hover:bg-red-700 transition">ท้าทาย (Hard)</button>
                </div>
            </div>

            <button onclick="startGame()" class="px-12 py-4 bg-yellow-500 hover:bg-yellow-400 text-stone-900 font-extrabold text-2xl rounded-full shadow-lg transform hover:scale-105 active:scale-95 transition">
                เริ่มผจญภัยเลย! 🚀
            </button>
        </div>

        <div id="game-screen" class="hidden relative w-full h-full flex flex-col justify-between">
            <div class="p-3 bg-stone-900/80 backdrop-blur-sm border-b-2 border-amber-800 flex justify-between items-center z-5">
                <div class="flex items-center gap-4">
                    <span id="hud-char" class="text-2xl">🐒</span>
                    <div>
                        <div class="text-xs text-gray-400">พลังใจ</div>
                        <div id="hud-hearts" class="text-xl text-red-500">❤️❤️❤️</div>
                    </div>
                </div>
                <div class="text-center">
                    <div class="text-xs text-gray-400">ด่านที่ (Stage)</div>
                    <div id="hud-stage" class="text-lg font-bold text-yellow-400">1 / 5</div>
                </div>
                <div class="text-right flex gap-4">
                    <div>
                        <div class="text-xs text-gray-400">คะแนนปัจจุบัน</div>
                        <div id="hud-score" class="text-lg font-bold text-green-4xl text-green-400">0</div>
                    </div>
                    <div>
                        <div class="text-xs text-gray-400">คะแนนสูงสุด</div>
                        <div id="hud-highscore" class="text-lg font-bold text-amber-4xl text-amber-400">0</div>
                    </div>
                </div>
            </div>

            <div class="relative flex-1 w-full bg-emerald-950">
                <canvas id="gameCanvas" class="w-full h-full block"></canvas>
                <div class="absolute top-2 left-1/2 transform -translate-x-1/2 bg-stone-950/80 px-4 py-1 rounded-full text-xs text-emerald-300 border border-emerald-800">
                    💡 จงเรียงคำศัพท์ให้ถูกต้องตามโครงสร้าง: ประธาน (Subject) + กริยา (Verb) + กรรม (Object)
                </div>
            </div>

            <div class="p-4 bg-stone-950/90 border-t-4 border-amber-800 flex flex-col gap-3 min-h-[160px] justify-center items-center z-5">
                <div id="answer-zone" class="w-full max-w-2xl min-h-[50px] p-2 bg-stone-900 border-2 border-dashed border-stone-700 rounded-xl flex justify-center items-center gap-2 flex-wrap">
                    <span class="text-stone-500 text-sm">คลิกการ์ดคำศัพท์ด้านล่างเพื่อเรียงประโยคที่นี่</span>
                </div>

                <div id="cards-zone" class="flex gap-3 justify-center items-center flex-wrap">
                    </div>

                <div class="flex gap-2 w-full max-w-xs">
                    <button onclick="clearAnswer()" class="flex-1 py-1 bg-stone-700 hover:bg-stone-600 rounded-lg text-sm font-semibold transition">ล้างคำตอบ</button>
                    <button onclick="checkAnswer()" class="flex-1 py-1 bg-amber-600 hover:bg-amber-500 rounded-lg text-sm font-bold text-stone-950 bg-gradient-to-r from-yellow-400 to-amber-500 transition">ส่งคำตอบ 🌟</button>
                </div>
            </div>
        </div>

        <div id="end-screen" class="hidden absolute inset-0 z-20 bg-stone-950/95 flex flex-col items-center justify-center p-6 text-center">
            <div id="end-icon" class="text-7xl mb-4">🏆</div>
            <h2 id="end-title" class="text-4xl font-bold text-yellow-400 mb-2">ยินดีด้วย! คุณค้นพบสมบัติแล้ว</h2>
            <p id="end-body" class="text-gray-300 max-w-md mb-6">คุณช่วยให้คู่หูเดินทางผ่านป่าผู้พูดได้สำเร็จและเก่งภาษาอังกฤษขึ้นมาก!</p>
            
            <div class="bg-stone-900 p-4 rounded-xl border border-stone-800 mb-6 w-full max-w-xs">
                <div class="text-sm text-gray-400">คะแนนที่คุณทำได้</div>
                <div id="end-score" class="text-3xl font-bold text-green-400 mb-2">0</div>
                <div class="text-xs text-amber-4xl border-t border-stone-800 pt-2 text-amber-500">
                    คะแนนสูงสุดในคลัง: <span id="end-highscore">0</span>
                </div>
            </div>

            <button onclick="returnToMenu()" class="px-8 py-3 bg-gradient-to-r from-green-600 to-emerald-600 hover:from-green-500 hover:to-emerald-500 font-bold rounded-xl shadow-md transition">
                กลับหน้าหลัก / เล่นอีกครั้ง 🔄
            </button>
        </div>

    </main>

    <footer class="mt-4 text-xs text-gray-500 max-w-xl text-center">
        *เหมาะสำหรับเปิดบนหน้าจอโปรเจคเตอร์หรือแท็บเล็ตในชั้นเรียน ระบบจะบันทึก High Score ลงในเบราว์เซอร์นั้นๆ อัตโนมัติ โดยไม่ต้องเชื่อมต่อระบบฐานข้อมูลภายนอก*
    </footer>

    <script>
        // คลังโจทย์วิชาภาษาอังกฤษ (S + V + O) แบ่งตามระดับความยาก
        const questionBank = {
            medium: [
                { correct: ["I", "like", "apples"], pool: ["like", "I", "apples"] },
                { correct: ["He", "plays", "football"], pool: ["plays", "football", "He"] },
                { correct: ["She", "eats", "rice"], pool: ["rice", "She", "eats"] },
                { correct: ["We", "love", "cats"], pool: ["cats", "We", "love"] },
                { correct: ["They", "read", "books"], pool: ["read", "They", "books"] },
                { correct: ["Monkeys", "eat", "bananas"], pool: ["eat", "bananas", "Monkeys"] },
                { correct: ["A", "dog", "barks"], pool: ["barks", "dog", "A"] },
                { correct: ["Birds", "can", "fly"], pool: ["fly", "can", "Birds"] }
            ],
            hard: [
                { correct: ["The", "teacher", "writes", "a", "lesson"], pool: ["writes", "teacher", "a", "The", "lesson"] },
                { correct: ["My", "mother", "cooks", "yummy", "food"], pool: ["cooks", "mother", "food", "yummy", "My"] },
                { correct: ["Big", "elephants", "drink", "clean", "water"], pool: ["drink", "elephants", "water", "clean", "Big"] },
                { correct: ["Students", "study", "English", "every", "day"], pool: ["English", "every", "study", "day", "Students"] },
                { correct: ["A", "brave", "explorer", "finds", "gold"], pool: ["explorer", "gold", "brave", "A", "finds"] }
            ]
        };

        // สถานะตัวแปรหลักของระบบเกม (Game State)
        let chosenCharacter = '🐒';
        let chosenDifficulty = 'medium';
        let currentStage = 1;
        const totalStages = 5; // ชนะ 5 ด่านถือว่าถึงสมบัติกลางป่า
        let hearts = 3;
        let score = 0;
        let consecutiveCorrect = 0; // นับข้อถูกต่อเนื่องเพื่อเพิ่มหัวใจ

        let activeQuestions = [];
        let currentQuestionIndex = 0;
        let selectedWords = [];

        // ตัวแปรสำหรับระบบการวาดกราฟิก (Canvas)
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        let animationFrameId;
        let playerX = 50;
        let targetPlayerX = 50;
        let environmentObjects = [];

        // เริ่มตั้งค่าหน้าจอ Canvas ให้ตอบสนองความละเอียดที่ถูกต้อง
        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }

        // ฟังก์ชันสร้างวัตถุประกอบฉากในป่า (สุ่มต้นไม้/หญ้า)
        function generateEnvironment() {
            environmentObjects = [];
            // สร้างต้นไม้พื้นหลังตามระยะทาง
            for (let i = 0; i < 15; i++) {
                environmentObjects.push({
                    x: Math.random() * canvas.width * 2,
                    y: canvas.height - 40 - (Math.random() * 40),
                    size: 30 + Math.random() * 40,
                    color: ['#0f5132', '#146c43', '#198754'][Math.floor(Math.random() * 3)]
                });
            }
        }

        // ระบบแอนิเมชัน วาดภาพในป่าลงบน Canvas
        function drawGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // 1. วาดท้องฟ้า/พื้นหลังส่วนบน
            let gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#115e59');
            gradient.addColorStop(1, '#064e3b');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 2. วาดพื้นดิน
            ctx.fillStyle = '#78350f';
            ctx.fillRect(0, canvas.height - 40, canvas.width, 40);
            ctx.fillStyle = '#15803d';
            ctx.fillRect(0, canvas.height - 45, canvas.width, 5);

            // คำนวณการเคลื่อนที่แบบนุ่มนวล (Lerp) ของตัวละครหลัก
            playerX += (targetPlayerX - playerX) * 0.08;

            // 3. วาดต้นไม้ประกอบฉาก (เลื่อนสวนทางเมื่อตัวละครเดิน)
            environmentObjects.forEach(obj => {
                let shiftedX = obj.x - (playerX * 0.5); // บรรยากาศแบบสระว่ายน้ำตื้นลึก Parallax
                // วาดลำต้น
                ctx.fillStyle = '#451a03';
                ctx.fillRect(shiftedX - 4, obj.y, 8, canvas.height - obj.y);
                // วาดพุ่มใบไม้
                ctx.fillStyle = obj.color;
                ctx.beginPath();
                ctx.arc(shiftedX, obj.y, obj.size, 0, Math.PI * 2);
                ctx.fill();
            });

            // 4. วาดกล่องสมบัติที่ปลายทางขวาสุดของแต่ละด่าน
            let treasureX = canvas.width - 80;
            ctx.font = '40px Arial';
            ctx.fillText('🎁', treasureX, canvas.height - 50);

            // 5. วาดตัวละครผู้เล่นตามพิกัด
            ctx.font = '50px Arial';
            ctx.textAlign = 'center';
            ctx.fillText(chosenCharacter, playerX, canvas.height - 48);

            // สั่งทำซ้ำลูปแอนิเมชัน
            animationFrameId = requestAnimationFrame(drawGame);
        }

        // ฟังก์ชันเลือกตัวละครจากเมนู
        function selectCharacter(emoji, name) {
            chosenCharacter = emoji;
            // เปลี่ยนกรอบไฮไลต์ปุ่มที่เลือก
            document.querySelectorAll('.char-btn').forEach((btn, idx) => {
                btn.classList.remove('border-amber-600', 'bg-amber-950');
                if(btn.innerText === emoji) {
                    btn.classList.add('border-amber-600', 'bg-amber-950');
                }
            });
        }

        // ฟังก์ชันเลือกระดับความยากจากเมนู
        function selectDifficulty(level) {
            chosenDifficulty = level;
            const medBtn = document.getElementById('diff-btn-medium');
            const hardBtn = document.getElementById('diff-btn-hard');
            if (level === 'medium') {
                medBtn.className = "px-6 py-2 bg-green-700 text-white font-bold rounded-lg border-2 border-white hover:bg-green-600 transition";
                hardBtn.className = "px-6 py-2 bg-red-800 text-white font-bold rounded-lg border-2 border-transparent hover:bg-red-700 transition";
            } else {
                medBtn.className = "px-6 py-2 bg-green-700 text-white font-bold rounded-lg border-2 border-transparent hover:bg-green-600 transition";
                hardBtn.className = "px-6 py-2 bg-red-800 text-white font-bold rounded-lg border-2 border-white hover:bg-red-700 transition";
            }
        }

        // เริ่มต้นการรันเกมย้ายจากหน้าหลักเข้าสู่เกม
        function startGame() {
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('game-screen').classList.remove('hidden');
            document.getElementById('end-screen').classList.add('hidden');

            // รีเซ็ตค่าสถิติต่างๆ ของการเริ่มเล่นใหม่
            currentStage = 1;
            hearts = 3;
            score = 0;
            consecutiveCorrect = 0;
            playerX = 50;
            targetPlayerX = 50;

            // โหลด High Score เก่าเก็บไว้แสดงผลจาก LocalStorage ล่าสุด
            let savedHighScore = localStorage.getItem('forest_highscore_' + chosenDifficulty) || 0;
            document.getElementById('hud-highscore').innerText = savedHighScore;

            // สุ่มเตรียมเซ็ตโจทย์คำถามตามระดับความยากที่ผู้ใช้เลือก
            let fullBank = [...questionBank[chosenDifficulty]];
            activeQuestions = [];
            // สุ่มดึงข้อสอบออกมา 5 ข้อสำหรับ 5 ด่าน
            for(let i=0; i<totalStages; i++) {
                if(fullBank.length === 0) fullBank = [...questionBank[chosenDifficulty]]; // วนลูปถ้าคลังหมด
                let randomIndex = Math.floor(Math.random() * fullBank.length);
                activeQuestions.push(fullBank.splice(randomIndex, 1)[0]);
            }

            currentQuestionIndex = 0;
            
            // ตั้งค่าระบบภาพ
            resizeCanvas();
            generateEnvironment();
            cancelAnimationFrame(animationFrameId);
            drawGame();

            loadStageQuestion();
            updateHUD();
        }

        // อัปเดตสถานะแถบข้อมูลผู้เล่นด้านบน (HUD)
        function updateHUD() {
            document.getElementById('hud-char').innerText = chosenCharacter;
            document.getElementById('hud-stage').innerText = `${currentStage} / ${totalStages}`;
            document.getElementById('hud-score').innerText = score;
            
            // แสดงรูปหัวใจตามจำนวนพลังชีวิตคงเหลือ
            let heartString = '';
            for(let i=0; i<3; i++) {
                heartString += (i < hearts) ? '❤️' : '🖤';
            }
            document.getElementById('hud-hearts').innerText = heartString;
        }

        // โหลดและจัดการเตรียมคำถามประจำด่าน
        function loadStageQuestion() {
            selectedWords = [];
            let question = activeQuestions[currentQuestionIndex];
            
            // นำคำศัพท์ใน Pool มาสุ่มลำดับคำเพื่อไม่ให้ซ้ำกันตอนเริ่มต้นโจทย์
            let shuffledPool = [...question.pool].sort(() => Math.random() - 0.5);

            // แสดงผลการสร้างปุ่มการ์ดคำศัพท์
            const cardsZone = document.getElementById('cards-zone');
            cardsZone.innerHTML = '';
            shuffledPool.forEach((word, index) => {
                let btn = document.createElement('button');
                btn.innerText = word;
                btn.id = `word-card-${index}`;
                btn.className = "px-5 py-3 bg-amber-100 text-stone-900 font-bold rounded-xl border-b-4 border-amber-400 shadow hover:bg-white active:translate-y-1 transition text-lg";
                btn.onclick = () => selectWord(word, btn.id);
                cardsZone.appendChild(btn);
            });

            renderAnswerZone();
        }

        // วาดและประมวลผลคำที่ถูกเลือกเข้าไปในพื้นที่ตอบข้อสอบ
        function renderAnswerZone() {
            const answerZone = document.getElementById('answer-zone');
            if (selectedWords.length === 0) {
                answerZone.innerHTML = '<span class="text-stone-500 text-sm">คลิกการ์ดคำศัพท์ด้านล่างเพื่อเรียงประโยคที่นี่</span>';
                return;
            }

            answerZone.innerHTML = '';
            selectedWords.forEach((item, index) => {
                let span = document.createElement('span');
                span.innerText = item.word;
                span.className = "px-4 py-2 bg-gradient-to-r from-emerald-500 to-green-600 text-white font-bold rounded-lg border border-emerald-300 shadow cursor-pointer text-lg transform hover:scale-105 active:scale-95 transition";
                // กดที่คำในกล่องตอบเพื่อยกเลิกคำนั้นได้ด้วย
                span.onclick = () => deselectWord(index);
                answerZone.appendChild(span);
            });
        }

        // จัดการเหตุการณ์เมื่อกดคำศัพท์ด้านล่าง
        function selectWord(word, elementId) {
            let cardBtn = document.getElementById(elementId);
            // ซ่อนปุ่มคำศัพท์ด้านล่างชั่วคราวเมื่อถูกเลือกขึ้นมาข้างบน
            cardBtn.classList.add('opacity-30', 'pointer-events-none');
            selectedWords.push({ word: word, originId: elementId });
            renderAnswerZone();
        }

        // ยกเลิกคำศัพท์จากกล่องคำตอบลงมาข้างล่าง
        function deselectWord(index) {
            let removedItem = selectedWords.splice(index, 1)[0];
            let originBtn = document.getElementById(removedItem.originId);
            if(originBtn) {
                originBtn.classList.remove('opacity-30', 'pointer-events-none');
            }
            renderAnswerZone();
        }

        // ปุ่มเคลียร์คำตอบทั้งหมดรวดเดียว
        function clearAnswer() {
            selectedWords.forEach(item => {
                let originBtn = document.getElementById(item.originId);
                if(originBtn) originBtn.classList.remove('opacity-30', 'pointer-events-none');
            });
            selectedWords = [];
            renderAnswerZone();
        }

        // ตรวจสอบคำตอบหลังจากกดส่ง
        function checkAnswer() {
            let currentQuestion = activeQuestions[currentQuestionIndex];
            let userSentence = selectedWords.map(item => item.word);

            // ตรวจสอบความถูกต้องโดยการเทียบอาร์เรย์แบบเรียงลำดับ
            let isCorrect = userSentence.length === currentQuestion.correct.length &&
                            userSentence.every((val, i) => val === currentQuestion.correct[i]);

            if (isCorrect) {
                // กรณีตอบถูก
                score += (chosenDifficulty === 'hard') ? 20 : 10;
                consecutiveCorrect++;
                alert("ถูกต้องแล้ว! 🎉 เก่งมากประโยคสมบูรณ์");

                // เงื่อนไขโบนัส: ตอบถูกครบ 3 ข้อติดต่อกัน ได้ใจคืน 1 ดวง (สูงสุดไม่เกิน 3)
                if (consecutiveCorrect >= 3) {
                    if (hearts < 3) {
                        hearts++;
                        alert("❤️ สุดยอด! ตอบถูกต่อเนื่องครบ 3 ข้อ ได้รับพลังใจเพิ่ม 1 ดวง!");
                    }
                    consecutiveCorrect = 0; // รีเซ็ตรอบโบนัสใหม่
                }

                // สั่งให้ตัวละครเดินบนหน้าจอ Canvas ไปข้างหน้าหาเป้าหมายกล่องของขวัญประจำด่าน
                targetPlayerX = (canvas.width / totalStages) * currentStage + 50;
                if(targetPlayerX > canvas.width - 100) targetPlayerX = canvas.width - 100;

                // ตรวจเงื่อนไขการเคลียร์ด่านหรือจบด่านสุดท้าย
                setTimeout(() => {
                    if (currentStage >= totalStages) {
                        endGame(true); // ชนะเกมทั้งหมด
                    } else {
                        currentStage++;
                        currentQuestionIndex++;
                        playerX = 50; // รีเซ็ตตำแหน่งเริ่มต้นเดินใหม่เพื่อแอนิเมชันด่านต่อไป
                        targetPlayerX = 50;
                        generateEnvironment();
                        loadStageQuestion();
                        updateHUD();
                    }
                }, 1000);

            } else {
                // กรณีตอบผิด
                hearts--;
                consecutiveCorrect = 0; // รีเซ็ตการนับโบนัสต่อเนื่องทันทีเมื่อพลาด
                alert("คำตอบยังไม่ถูกต้อง ❌ ลองพยายามเรียงคำศัพท์ใหม่อีกครั้งนะ!");
                
                updateHUD();
                clearAnswer(); // คืนการ์ดคำศัพท์ให้เด็กลองคิดทำใหม่

                // ตรวจสอบพลังชีวิตหมด = แพ้เกมจบลงทันที
                if (hearts <= 0) {
                    endGame(false);
                }
            }
        }

        // สิ้นสุดและสรุปผลคะแนนสูงสุดลงใน LocalStorage
        function endGame(isWin) {
            cancelAnimationFrame(animationFrameId);
            document.getElementById('game-screen').classList.add('hidden');
            document.getElementById('end-screen').classList.remove('hidden');

            const endIcon = document.getElementById('end-icon');
            const endTitle = document.getElementById('end-title');
            const endBody = document.getElementById('end-body');
            const endScoreDisplay = document.getElementById('end-score');
            const endHighScoreDisplay = document.getElementById('end-highscore');

            // บันทึกและดึงข้อมูลคะแนนสูงสุดผ่านระบบ LocalStorage
            let currentHighScore = parseInt(localStorage.getItem('forest_highscore_' + chosenDifficulty)) || 0;
            if (score > currentHighScore) {
                currentHighScore = score;
                localStorage.setItem('forest_highscore_' + chosenDifficulty, currentHighScore);
            }

            endScoreDisplay.innerText = score;
            endHighScoreDisplay.innerText = currentHighScore;

            if (isWin) {
                endIcon.innerText = "🏆";
                endTitle.innerText = "ภารกิจสำเร็จ! คุณคือผู้พิทักษ์ป่า";
                endBody.innerText = "ยินดีด้วยนะ! คุณพาสหายตัวน้อยฝ่าฟันอุปสรรคและเรียงประโยคภาษาอังกฤษได้ถูกต้อง จนค้นพบขุมทรัพย์มหัศจรรย์สำเร็จ!";
                endTitle.className = "text-4xl font-bold text-yellow-400 mb-2";
            } else {
                endIcon.innerText = "💀";
                endTitle.innerText = "พลังชีวิตหมดลงแล้ว!";
                endBody.innerText = "ป่าแห่งคำพูดมีความท้าทายมากเกินไปในรอบนี้ แต่ไม่ต้องเสียใจไปนะ! ลองใหม่อีกรอบเพื่อพัฒนาภาษาอังกฤษให้เก่งขึ้นกันเถอะ!";
                endTitle.className = "text-4xl font-bold text-red-500 mb-2";
            }
        }

        // ฟังก์ชันกลับสู่หน้าจอหลัก
        function returnToMenu() {
            document.getElementById('start-screen').classList.remove('hidden');
            document.getElementById('game-screen').classList.add('hidden');
            document.getElementById('end-screen').classList.add('hidden');
        }

        // บล็อกป้อนกันหน้าจอเบราว์เซอร์ปรับขนาดกลางคันให้จัดขนาด Canvas อัตโนมัติ
        window.addEventListener('resize', () => {
            if(!document.getElementById('game-screen').classList.contains('hidden')) {
                resizeCanvas();
            }
        });
    </script>
</body>
</html>
