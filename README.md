# -YouTube- <!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>YT Downloader</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            font-family: 'Roboto', -apple-system, Segoe UI, sans-serif;
            background: #0f0f0f;
            color: #f1f1f1;
            min-height: 100vh;
        }
        header {
            position: sticky; top: 0; z-index: 100;
            display: flex; align-items: center; justify-content: space-between;
            padding: 0 16px; height: 56px;
            background: #0f0f0f;
            border-bottom: 1px solid #272727;
        }
        .logo { display: flex; align-items: center; gap: 8px; font-weight: 700; font-size: 20px; }
        .logo .icon {
            background: #ff0000; color: white; width: 32px; height: 22px;
            border-radius: 5px; display: flex; align-items: center; justify-content: center;
            font-size: 14px;
        }
        .header-right { display: flex; align-items: center; gap: 12px; }
        .gear {
            width: 40px; height: 40px; border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            cursor: pointer; font-size: 20px; transition: background 0.2s;
        }
        .gear:hover { background: #272727; }
        .badge {
            display: none; background: gold; color: #000; font-size: 11px;
            font-weight: 700; padding: 3px 8px; border-radius: 10px;
        }
        .badge.on { display: inline-block; }

        main { max-width: 900px; margin: 30px auto; padding: 0 16px; }
        h1 { font-size: 24px; margin-bottom: 6px; }
        .subtitle { color: #aaa; font-size: 14px; margin-bottom: 24px; }

        .card {
            background: #181818; border: 1px solid #272727;
            border-radius: 12px; padding: 20px; margin-bottom: 20px;
        }
        label { display: block; font-size: 13px; color: #aaa; margin-bottom: 6px; margin-top: 14px; }
        label:first-child { margin-top: 0; }
        input, select {
            width: 100%; padding: 12px; font-size: 15px;
            background: #121212; color: #f1f1f1;
            border: 1px solid #303030; border-radius: 8px;
            outline: none;
        }
        input:focus, select:focus { border-color: #ff0000; }
        .row { display: flex; gap: 12px; flex-wrap: wrap; }
        .row > div { flex: 1; min-width: 200px; }

        button {
            width: 100%; padding: 14px; margin-top: 18px;
            background: #ff0000; color: white; border: none;
            border-radius: 8px; font-size: 16px; font-weight: 600;
            cursor: pointer; transition: background 0.2s;
        }
        button:hover { background: #cc0000; }
        button:disabled { background: #444; cursor: not-allowed; }

        #status {
            margin-top: 20px; padding: 16px; background: #181818;
            border-radius: 8px; border: 1px solid #272727;
            display: none; white-space: pre-wrap; word-break: break-word;
            font-size: 14px;
        }
        .progress-bar {
            height: 8px; background: #303030; border-radius: 4px;
            overflow: hidden; margin-top: 10px;
        }
        .progress-fill {
            height: 100%; background: #ff0000; width: 0%;
            transition: width 0.3s;
        }
        a { color: #ff0000; font-weight: 600; }

        /* Панель настроек */
        .settings-overlay {
            display: none; position: fixed; inset: 0;
            background: rgba(0,0,0,0.7); z-index: 200;
        }
        .settings-overlay.on { display: block; }
        .settings-panel {
            position: fixed; top: 56px; right: 16px;
            width: 320px; background: #212121;
            border: 1px solid #303030; border-radius: 12px;
            padding: 20px; z-index: 201;
            box-shadow: 0 8px 30px rgba(0,0,0,0.6);
        }
        .settings-panel h3 { font-size: 16px; margin-bottom: 14px; }
        .settings-panel .hint { font-size: 12px; color: #888; margin-top: 8px; }
        .close-btn {
            position: absolute; top: 10px; right: 12px;
            background: none; border: none; color: #aaa;
            font-size: 20px; cursor: pointer; width: auto; padding: 0;
            margin: 0;
        }
        .close-btn:hover { color: #fff; background: none; }
        .premium-active { color: gold; font-weight: 600; font-size: 13px; margin-top: 10px; }
    </style>
</head>
<body>

<header>
    <div class="logo">
        <div class="icon">▶</div>
        <span>YT Downloader</span>
    </div>
    <div class="header-right">
        <span class="badge" id="premiumBadge">PREMIUM</span>
        <div class="gear" onclick="toggleSettings()">⚙️</div>
    </div>
</header>

<div class="settings-overlay" id="overlay" onclick="toggleSettings()"></div>
<div class="settings-panel" id="settingsPanel" style="display:none;">
    <button class="close-btn" onclick="toggleSettings()">✕</button>
    <h3>Настройки</h3>
    <label>Промокод</label>
    <input type="text" id="promoInput" placeholder="Введите промокод">
    <button onclick="applyPromo()">Активировать</button>
    <div class="premium-active" id="premiumStatus" style="display:none;">
        ✅ Premium активирован
    </div>
    <div class="hint">Введите промокод, чтобы открыть 4K/2K и лучшее качество звука.</div>
</div>

<main>
    <h1>Скачать видео с YouTube</h1>
    <div class="subtitle">Вставьте ссылку, выберите качество видео и звука</div>

    <div class="card">
        <label>Ссылка на видео</label>
        <input type="text" id="url" placeholder="https://www.youtube.com/watch?v=...">

        <div class="row">
            <div>
                <label>Качество видео</label>
                <select id="videoFormat">
                    <option value="bestvideo[height<=144]">144p</option>
                    <option value="bestvideo[height<=240]">240p</option>
                    <option value="bestvideo[height<=360]">360p</option>
                    <option value="bestvideo[height<=480]">480p</option>
                    <option value="bestvideo[height<=720]">720p (HD)</option>
                    <option value="bestvideo[height<=1080]">1080p (Full HD)</option>
                    <option value="bestvideo[height<=1440]" class="premium-opt">1440p (2K) — Premium</option>
                    <option value="bestvideo[height<=2160]" class="premium-opt">2160p (4K) — Premium</option>
                    <option value="bestvideo">Максимальное (до 8K)</option>
                    <option value="none">Без видео (только звук)</option>
                </select>
            </div>
            <div>
                <label>Качество звука</label>
                <select id="audioFormat">
                    <option value="bestaudio[abr<=64]">Низкое (~64 kbps)</option>
                    <option value="bestaudio[abr<=128]">Среднее (~128 kbps)</option>
                    <option value="bestaudio[abr<=192]">Высокое (~192 kbps)</option>
                    <option value="bestaudio">Лучшее доступное</option>
                    <option value="bestaudio[abr>=256]" class="premium-opt">Максимальное (256+ kbps) — Premium</option>
                    <option value="none">Без звука</option>
                </select>
            </div>
        </div>

        <button id="btn" onclick="start()">Скачать</button>
        <div id="status"></div>
    </div>
</main>

<script>
    let premium = false;
    let currentTask = null;

    function toggleSettings() {
        const panel = document.getElementById('settingsPanel');
        const overlay = document.getElementById('overlay');
        const isOpen = panel.style.display === 'block';
        panel.style.display = isOpen ? 'none' : 'block';
        overlay.classList.toggle('on', !isOpen);
    }

    function applyPromo() {
        const code = document.getElementById('promoInput').value.trim();
        if (code === '3854') {
            premium = true;
            document.getElementById('premiumBadge').classList.add('on');
            document.getElementById('premiumStatus').style.display = 'block';
            document.querySelectorAll('.premium-opt').forEach(o => o.disabled = false);
            alert('Premium активирован! 4K/2K и максимальный звук доступны.');
        } else {
            alert('Неверный промокод');
        }
    }

    // По умолчанию премиум-опции заблокированы
    document.querySelectorAll('.premium-opt').forEach(o => o.disabled = true);

    async function start() {
        const url = document.getElementById('url').value.trim();
        const videoFormat = document.getElementById('videoFormat').value;
        const audioFormat = document.getElementById('audioFormat').value;
        const btn = document.getElementById('btn');
        const statusDiv = document.getElementById('status');

        if (!url) { alert('Вставь ссылку'); return; }

        // Проверка премиум-опций
        const videoSelect = document.getElementById('videoFormat');
        const audioSelect = document.getElementById('audioFormat');
        const selectedVideo = videoSelect.options[videoSelect.selectedIndex];
        const selectedAudio = audioSelect.options[audioSelect.selectedIndex];

        if ((selectedVideo.classList.contains('premium-opt') ||
             selectedAudio.classList.contains('premium-opt')) && !premium) {
            alert('Это качество доступно только с Premium. Введите промокод в настройках.');
            return;
        }

        btn.disabled = true;
        statusDiv.style.display = 'block';
        statusDiv.innerHTML = 'Отправка запроса...';

        try {
            const res = await fetch('/download', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({
                    url: url,
                    video_format: videoFormat,
                    audio_format: audioFormat
                })
            });
            const data = await res.json();

            if (data.task_id) {
                currentTask = data.task_id;
                statusDiv.innerHTML = 'Задача запущена. Ожидание...' +
                    '<div class="progress-bar"><div class="progress-fill" id="pf"></div></div>';
                pollStatus();
            } else {
                statusDiv.innerHTML = 'Ошибка: ' + (data.error || 'неизвестно');
                btn.disabled = false;
            }
        } catch (e) {
            statusDiv.innerHTML = 'Ошибка соединения: ' + e.message;
            btn.disabled = false;
        }
    }

    async function pollStatus() {
        if (!currentTask) return;
        try {
            const res = await fetch('/status/' + currentTask);
            const data = await res.json();
            const statusDiv = document.getElementById('status');

            if (data.status === 'processing') {
                statusDiv.innerHTML = 'Скачивание: ' + (data.progress || 0).toFixed(1) + '%' +
                    '<div class="progress-bar"><div class="progress-fill" style="width:' +
                    (data.progress || 0) + '%"></div></div>';
                setTimeout(pollStatus, 2000);
            } else if (data.status === 'done') {
                statusDiv.innerHTML = 'Готово! <a href="/file/' +
                    encodeURIComponent(data.file) + '">Скачать файл</a>';
                document.getElementById('btn').disabled = false;
                currentTask = null;
            } else if (data.status === 'error') {
                statusDiv.innerHTML = 'Ошибка:\n' + data.error;
                document.getElementById('btn').disabled = false;
                currentTask = null;
            } else {
                statusDiv.innerHTML = 'Задача не найдена';
                document.getElementById('btn').disabled = false;
            }
        } catch (e) {
            setTimeout(pollStatus, 3000);
        }
    }
</script>
</body>
</html>
