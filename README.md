<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>【インタラクティブ解説】ルービックキューブの角がねじれるとなぜ揃わない？</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chosen Palette: Calm Harmony (Beige, Soft Blue, Gentle Red) -->
    <!-- Application Structure Plan: A single-page, guided narrative structure. It starts with the basic concept of corner orientation, introduces the 'mod 3' law, lets the user simulate legal moves to see the law holds, then allows them to illegally twist a corner to see the law break. This experiential flow is more intuitive for understanding a mathematical concept than a static report. -->
    <!-- Visualization & Content Choices: Report Info: Corner orientation sum is a mod 3 invariant. -> Goal: Make this rule tangible. -> Viz/Presentation Method: An interactive 2D diagram of cube corners made with HTML/CSS, and a dynamic text-based 'State Dashboard'. -> Interaction: Buttons simulate legal face turns and an illegal corner twist. The user sees the orientation numbers and the sum change in real-time. -> Justification: This directly simulates the core logic, providing immediate feedback and proof-by-doing. It's more effective than a static chart. -> Library/Method: Vanilla JavaScript for logic and DOM manipulation. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Hiragino Kaku Gothic ProN', 'Meiryo', sans-serif;
            background-color: #FDFBF5;
            color: #383838;
        }
        .cube-face {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-template-rows: repeat(3, 1fr);
            gap: 4px;
            width: 150px;
            height: 150px;
        }
        .piece {
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.25rem;
            font-weight: bold;
            border-radius: 4px;
        }
        .corner { background-color: #FFD700; color: #333; }
        .edge { background-color: #E0E0E0; }
        .center { background-color: #BDBDBD; }
        .orientation-0 { border: 3px solid transparent; }
        .orientation-1 { border: 3px solid #3B82F6; } /* Blue */
        .orientation-2 { border: 3px solid #EF4444; } /* Red */
        .law-ok { color: #16A34A; } /* Green */
        .law-broken { color: #DC2626; } /* Red */
        .section-fade-in {
            opacity: 0;
            animation: fadeIn 1s ease-out forwards;
        }
        @keyframes fadeIn {
            to { opacity: 1; }
        }
    </style>
</head>
<body class="antialiased">

    <main class="container mx-auto p-4 md:p-8 max-w-4xl">

        <header class="text-center mb-12 section-fade-in">
            <h1 class="text-3xl md:text-4xl font-bold text-gray-800 mb-2">なぜ？ルービックキューブの角がねじれると揃わない理由</h1>
            <p class="text-lg text-gray-600">インタラクティブ解説で「絶対の法則」を体感しよう</p>
        </header>

        <!-- Section 1: The Law -->
        <section id="section-law" class="mb-12 p-6 bg-white rounded-xl shadow-lg section-fade-in" style="animation-delay: 0.5s;">
            <h2 class="text-2xl font-bold mb-4 text-center">コーナーパーツの「向き」と絶対法則</h2>
            <p class="mb-6 text-gray-700 leading-relaxed">
                ルービックキューブの8つのコーナーパーツにはそれぞれ「向き」があります。ここでは、正しい向きを<span class="font-bold">「0」</span>、時計回りに120°ねじれた状態を<span class="font-bold text-blue-600">「1」</span>、さらに120°ねじれた状態を<span class="font-bold text-red-600">「2」</span>とします。そして、キューブには絶対に破れない法則があります。
            </p>
            <div class="p-4 bg-amber-100 border-l-4 border-amber-500 rounded-r-lg text-center">
                <p class="text-xl font-semibold text-amber-800">「すべてのコーナーの『向き』の合計値は、必ず3の倍数になる」</p>
            </div>
        </section>

        <!-- Section 2: The Simulator -->
        <section id="section-simulator" class="mb-12 p-6 bg-white rounded-xl shadow-lg section-fade-in" style="animation-delay: 1s;">
            <h2 class="text-2xl font-bold mb-4 text-center">法則をシミュレーターで体感する</h2>
            <div class="flex flex-col lg:flex-row items-center justify-center gap-8">
                
                <!-- Cube Representation -->
                <div class="w-full lg:w-1/2">
                    <p class="text-center mb-2 text-gray-600">上面（U面）のコーナー</p>
                    <div class="flex justify-center gap-4 mb-4">
                        <div id="corner-0" class="piece corner orientation-0" title="コーナー0">0</div>
                        <div id="corner-1" class="piece corner orientation-0" title="コーナー1">0</div>
                    </div>
                     <div class="flex justify-center gap-4">
                        <div id="corner-2" class="piece corner orientation-0" title="コーナー2">0</div>
                        <div id="corner-3" class="piece corner orientation-0" title="コーナー3">0</div>
                    </div>
                    <hr class="my-6 border-gray-300">
                    <p class="text-center mb-2 text-gray-600">下面（D面）のコーナー</p>
                    <div class="flex justify-center gap-4 mb-4">
                        <div id="corner-4" class="piece corner orientation-0" title="コーナー4">0</div>
                        <div id="corner-5" class="piece corner orientation-0" title="コーナー5">0</div>
                    </div>
                     <div class="flex justify-center gap-4">
                        <div id="corner-6" class="piece corner orientation-0" title="コーナー6">0</div>
                        <div id="corner-7" class="piece corner orientation-0" title="コーナー7">0</div>
                    </div>
                </div>

                <!-- Control Panel -->
                <div class="w-full lg:w-1/2 flex flex-col items-center">
                    <div id="status-panel" class="w-full max-w-sm p-4 mb-6 text-center bg-gray-50 rounded-lg shadow-inner transition-all duration-300">
                        <p class="text-lg mb-2">向きの合計値:</p>
                        <p id="sum-display" class="text-4xl font-mono font-bold mb-2">0</p>
                        <p id="law-status" class="text-lg font-bold law-ok">法則は守られています (0 % 3 = 0)</p>
                    </div>

                    <div class="w-full max-w-sm">
                        <p class="text-center font-semibold mb-2">1. 通常の回転を試す</p>
                        <div class="grid grid-cols-2 gap-2 mb-6">
                            <button onclick="performMove('R')" class="p-3 bg-blue-500 text-white font-bold rounded-lg hover:bg-blue-600 transition-colors">右面 (R) 回転</button>
                            <button onclick="performMove('U')" class="p-3 bg-green-500 text-white font-bold rounded-lg hover:bg-green-600 transition-colors">上面 (U) 回転</button>
                            <button onclick="performMove('F')" class="p-3 bg-red-500 text-white font-bold rounded-lg hover:bg-red-600 transition-colors">前面 (F) 回転</button>
                            <button onclick="performMove('L')" class="p-3 bg-orange-500 text-white font-bold rounded-lg hover:bg-orange-600 transition-colors">左面 (L) 回転</button>
                        </div>

                        <p class="text-center font-semibold mb-2">2. 禁断の技を試す</p>
                        <button onclick="twistCorner()" class="w-full p-3 bg-purple-600 text-white font-bold rounded-lg hover:bg-purple-700 transition-colors mb-6">コーナーを1つだけねじる</button>

                        <p class="text-center font-semibold mb-2">3. リセット</p>
                        <button onclick="resetCube()" class="w-full p-3 bg-gray-500 text-white font-bold rounded-lg hover:bg-gray-600 transition-colors">完成状態に戻す</button>
                    </div>
                </div>
            </div>
        </section>

        <!-- Section 3: The Conclusion -->
        <section id="section-conclusion" class="p-6 bg-white rounded-xl shadow-lg section-fade-in" style="animation-delay: 1.5s;">
            <h2 class="text-2xl font-bold mb-4 text-center">結論：なぜ揃わないのか</h2>
            <div id="conclusion-text" class="text-gray-700 leading-relaxed text-lg text-center">
                <p>上のシミュレーターで試したように、通常の回転では「向きの合計が3の倍数」という法則は決して破れません。しかし、コーナーを1つだけ物理的にねじると、合計値が「+1」または「+2」され、法則が破れてしまいます。
                <br><br>
                一度法則が破れた状態（合計が3の倍数でない状態）になると、どれだけ回転させても法則が守られた完成状態（合計が0）には戻れないのです。
                <br><br>
                <span class="font-bold text-xl block mt-4 p-4 bg-rose-100 border-l-4 border-rose-500 rounded-r-lg">解決策はただ一つ：ねじって壊したものは、ねじって直すこと。</span>
                </p>
            </div>
        </section>

    </main>

    <script>
        let orientations = [0, 0, 0, 0, 0, 0, 0, 0];
        const cornerElements = Array.from({ length: 8 }, (_, i) => document.getElementById(`corner-${i}`));
        const sumDisplay = document.getElementById('sum-display');
        const lawStatus = document.getElementById('law-status');
        const statusPanel = document.getElementById('status-panel');

        const moveEffects = {
            'R': { corners: [1, 3, 7, 5], twists: [2, 1, 1, 2] },
            'U': { corners: [0, 2, 3, 1], twists: [0, 0, 0, 0] },
            'F': { corners: [2, 0, 4, 6], twists: [1, 2, 2, 1] },
            'L': { corners: [0, 4, 6, 2], twists: [1, 2, 2, 1] }
        };

        function updateDisplay() {
            let sum = 0;
            orientations.forEach((o, i) => {
                cornerElements[i].textContent = o;
                cornerElements[i].className = `piece corner orientation-${o}`;
                sum += o;
            });

            sumDisplay.textContent = sum;
            const remainder = sum % 3;

            if (remainder === 0) {
                lawStatus.innerHTML = `法則は守られています (${sum} % 3 = 0)`;
                lawStatus.classList.remove('law-broken');
                lawStatus.classList.add('law-ok');
                statusPanel.classList.remove('bg-red-100');
                statusPanel.classList.add('bg-gray-50');
            } else {
                lawStatus.innerHTML = `法則が破られました！ (${sum} % 3 = ${remainder})`;
                lawStatus.classList.remove('law-ok');
                lawStatus.classList.add('law-broken');
                statusPanel.classList.remove('bg-gray-50');
                statusPanel.classList.add('bg-red-100');
            }
        }

        function performMove(move) {
            const effect = moveEffects[move];
            
            // Permute corners
            const tempOrientations = [...orientations];
            for (let i = 0; i < 4; i++) {
                const from = effect.corners[i];
                const to = effect.corners[(i + 1) % 4];
                tempOrientations[to] = orientations[from];
            }
            orientations = tempOrientations;

            // Apply twists
            effect.corners.forEach((cornerIndex, i) => {
                orientations[cornerIndex] = (orientations[cornerIndex] + effect.twists[i]) % 3;
            });
            
            updateDisplay();
        }

        function twistCorner() {
            const cornerToTwist = Math.floor(Math.random() * 8);
            orientations[cornerToTwist] = (orientations[cornerToTwist] + 1) % 3;
            updateDisplay();
        }

        function resetCube() {
            orientations = [0, 0, 0, 0, 0, 0, 0, 0];
            updateDisplay();
        }

        window.onload = () => {
            updateDisplay();
        };
    </script>

</body>
</html>
