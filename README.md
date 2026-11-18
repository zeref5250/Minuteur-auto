<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Minuteur de tâches</title>
    <meta name="theme-color" content="#0b1020">
    <meta name="description" content="Organise tes tâches avec un minuteur et passe automatiquement à la suivante.">
    <link rel="manifest" href="./manifest.webmanifest">
    <link rel="icon" href="./minuteur-icon.svg" type="image/svg+xml">
    <style>
        :root {
            font-family: Arial, sans-serif;
            color: #20233a;
            background: #f4f6fb;
        }

        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            padding: 32px 16px;
        }

        main {
            width: min(760px, 100%);
            margin: 0 auto;
        }

        h1, h2, p {
            margin-top: 0;
        }

        h1 {
            margin-bottom: 8px;
            font-size: clamp(1.8rem, 5vw, 2.5rem);
        }

        .intro, .muted {
            color: #62677d;
        }

        .intro {
            margin-bottom: 24px;
        }

        .install-help {
            margin-bottom: 20px;
            padding: 14px 16px;
            border: 1px solid #dfe3f2;
            border-radius: 10px;
            background: #fff;
            color: #555b70;
            line-height: 1.5;
        }

        .install-help p {
            margin-bottom: 10px;
        }

        .install-help p:last-child {
            margin-bottom: 0;
        }

        .install-button {
            margin-bottom: 12px;
        }

        .install-button[hidden] {
            display: none;
        }

        .panel, .timer-card {
            margin-bottom: 18px;
            padding: 22px;
            border: 1px solid #e0e4ef;
            border-radius: 14px;
            background: #fff;
            box-shadow: 0 6px 20px rgb(34 42 74 / 5%);
        }

        .task-form {
            display: grid;
            grid-template-columns: minmax(160px, 1fr) 110px 110px auto;
            gap: 12px;
            align-items: end;
        }

        label {
            display: grid;
            gap: 7px;
            font-size: 0.9rem;
            font-weight: 700;
        }

        input, select {
            width: 100%;
            min-height: 44px;
            padding: 10px 12px;
            border: 1px solid #cbd1e0;
            border-radius: 8px;
            font: inherit;
        }

        input:focus-visible, button:focus-visible {
            outline: 3px solid #a5b4fc;
            outline-offset: 2px;
        }

        button {
            min-height: 42px;
            padding: 9px 13px;
            border: 0;
            border-radius: 8px;
            font: inherit;
            font-weight: 700;
            cursor: pointer;
        }

        button:disabled {
            cursor: not-allowed;
            opacity: 0.5;
        }

        .primary-button {
            color: #fff;
            background: #4f46e5;
        }

        .primary-button:hover:not(:disabled) {
            background: #4338ca;
        }

        .secondary-button {
            color: #3730a3;
            background: #eef0ff;
        }

        .remove-button {
            color: #a12636;
            background: #fff0f1;
        }

        .timer-card {
            text-align: center;
        }

        .timer-label {
            margin-bottom: 8px;
            color: #62677d;
        }

        .timer-display {
            margin: 0 0 8px;
            font-size: clamp(3.5rem, 14vw, 6rem);
            font-variant-numeric: tabular-nums;
            font-weight: 700;
            letter-spacing: 0.04em;
        }

        .timer-task {
            min-height: 1.5em;
            margin-bottom: 18px;
            font-size: 1.15rem;
            font-weight: 700;
            overflow-wrap: anywhere;
        }

        .timer-controls {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 10px;
        }

        .loop-setting {
            display: flex;
            grid-template-columns: none;
            justify-content: flex-start;
            align-items: center;
            gap: 9px;
            margin-top: 16px;
            font-weight: 400;
        }

        .loop-setting input {
            width: 18px;
            min-height: 18px;
            height: 18px;
            margin: 0;
            accent-color: #4f46e5;
        }

        .sound-setting {
            margin-top: 10px;
        }

        .sound-options {
            display: grid;
            gap: 12px;
            margin-top: 14px;
            text-align: left;
        }

        .sound-options[hidden], .sound-field[hidden] {
            display: none;
        }

        .sound-field {
            display: grid;
            gap: 7px;
        }

        .sound-help {
            margin: 0;
            color: #62677d;
            font-size: 0.9rem;
            font-weight: 400;
            line-height: 1.5;
        }

        .finish-link {
            display: inline-block;
            margin-top: 14px;
            padding: 10px 14px;
            border-radius: 8px;
            color: #fff;
            background: #4f46e5;
            font-weight: 700;
            text-decoration: none;
        }

        .finish-link[hidden] {
            display: none;
        }

        .finish-link:hover {
            background: #4338ca;
        }

        .task-list {
            display: grid;
            gap: 10px;
            margin: 0;
            padding: 0;
            list-style: none;
        }

        .task-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 12px;
            padding: 14px;
            border: 1px solid #e5e8f0;
            border-radius: 10px;
        }

        .task-row.is-current {
            border-color: #818cf8;
            background: #f4f4ff;
        }

        .task-details {
            min-width: 0;
        }

        .task-name {
            margin: 0 0 5px;
            font-weight: 700;
            overflow-wrap: anywhere;
        }

        .task-duration {
            color: #62677d;
            font-size: 0.95rem;
        }

        .empty-state, .notice {
            padding: 14px;
            border-radius: 8px;
        }

        .empty-state {
            color: #62677d;
            background: #f6f7fb;
            text-align: center;
        }

        .notice {
            margin-bottom: 16px;
            color: #7a241f;
            background: #fff0ed;
        }

        .notice[hidden], .empty-state[hidden] {
            display: none;
        }

        :root {
            color: #eef2ff;
            background: #080b18;
            font-family: Inter, "Segoe UI", Arial, sans-serif;
        }

        body {
            position: relative;
            min-height: 100vh;
            overflow-x: hidden;
            padding-top: 52px;
            background:
                radial-gradient(ellipse at 50% -20%, rgb(91 78 216 / 27%), transparent 58%),
                linear-gradient(145deg, #0b1020 0%, #111329 52%, #0b1020 100%);
        }

        body::before {
            position: fixed;
            z-index: 0;
            inset: 0;
            background-image:
                linear-gradient(rgb(255 255 255 / 2.5%) 1px, transparent 1px),
                linear-gradient(90deg, rgb(255 255 255 / 2.5%) 1px, transparent 1px);
            background-size: 44px 44px;
            content: "";
            mask-image: linear-gradient(to bottom, #000 0%, transparent 78%);
            pointer-events: none;
        }

        body::after {
            position: fixed;
            z-index: 0;
            top: 28%;
            left: -170px;
            width: 360px;
            height: 360px;
            border-radius: 50%;
            background: rgb(99 102 241 / 13%);
            content: "";
            filter: blur(85px);
            pointer-events: none;
        }

        main {
            position: relative;
            z-index: 1;
            width: min(820px, 100%);
        }

        h1 {
            color: #fff;
            font-size: clamp(2.2rem, 6vw, 3.2rem);
            letter-spacing: -0.055em;
        }

        h2 {
            color: #f3f4ff;
            letter-spacing: -0.025em;
        }

        .eyebrow {
            margin-bottom: 10px;
            color: #a5b4fc;
            font-size: 0.75rem;
            font-weight: 800;
            letter-spacing: 0.2em;
            text-transform: uppercase;
        }

        .intro, .muted, .timer-label, .task-duration {
            color: #a4abc3;
        }

        .panel, .timer-card, .install-help {
            border: 1px solid rgb(190 198 255 / 12%);
            border-radius: 20px;
            background: linear-gradient(145deg, rgb(24 29 55 / 94%), rgb(15 19 39 / 94%));
            box-shadow:
                0 24px 60px rgb(0 0 0 / 28%),
                inset 0 1px 0 rgb(255 255 255 / 4%);
            color: #eef2ff;
            backdrop-filter: blur(18px);
        }

        .panel {
            padding: 24px;
        }

        .timer-card {
            position: relative;
            overflow: hidden;
            padding: 34px 24px 24px;
            border-color: rgb(165 180 252 / 23%);
            background:
                radial-gradient(ellipse at 50% 0%, rgb(99 102 241 / 22%), transparent 58%),
                linear-gradient(145deg, #171b37, #101429 75%);
        }

        .timer-card::before {
            position: absolute;
            top: -105px;
            left: calc(50% - 105px);
            width: 210px;
            height: 210px;
            border: 1px solid rgb(165 180 252 / 11%);
            border-radius: 50%;
            box-shadow: 0 0 0 22px rgb(165 180 252 / 3%), 0 0 0 48px rgb(165 180 252 / 2%);
            content: "";
            pointer-events: none;
        }

        .timer-card > * {
            position: relative;
        }

        .timer-label {
            color: #a5b4fc;
            font-size: 0.8rem;
            font-weight: 800;
            letter-spacing: 0.14em;
            text-transform: uppercase;
        }

        .timer-display {
            color: #fff;
            font-size: clamp(4.5rem, 18vw, 7rem);
            letter-spacing: -0.07em;
            line-height: 1;
            text-shadow: 0 0 42px rgb(129 140 248 / 25%);
        }

        .timer-task {
            color: #d7dcf3;
        }

        .timer-progress {
            width: min(360px, 100%);
            height: 7px;
            overflow: hidden;
            margin: 0 auto 22px;
            border-radius: 999px;
            background: rgb(255 255 255 / 9%);
        }

        .timer-progress span {
            display: block;
            width: 100%;
            height: 100%;
            border-radius: inherit;
            background: linear-gradient(90deg, #818cf8, #c4b5fd);
            box-shadow: 0 0 16px rgb(129 140 248 / 50%);
            transition: width 0.25s linear;
        }

        label {
            color: #e2e6f8;
        }

        input, select {
            border-color: rgb(190 198 255 / 16%);
            background: #0e1327;
            color: #f3f4ff;
        }

        input::placeholder {
            color: #737c9c;
        }

        input[type="checkbox"] {
            accent-color: #818cf8;
        }

        input[type="file"] {
            padding: 7px;
            font-size: 0.9rem;
        }

        input[type="file"]::file-selector-button {
            margin-right: 10px;
            padding: 7px 10px;
            border: 0;
            border-radius: 6px;
            background: #252b4c;
            color: #e2e6ff;
            font: inherit;
            cursor: pointer;
        }

        input:focus-visible, select:focus-visible, button:focus-visible, a:focus-visible {
            outline-color: #a5b4fc;
        }

        button {
            transition: transform 0.18s ease, background-color 0.18s ease, box-shadow 0.18s ease;
        }

        button:hover:not(:disabled) {
            transform: translateY(-1px);
        }

        .primary-button, .finish-link {
            background: linear-gradient(135deg, #7067f0, #5749d8);
            box-shadow: 0 8px 20px rgb(79 70 229 / 25%);
        }

        .primary-button:hover:not(:disabled), .finish-link:hover {
            background: linear-gradient(135deg, #8279ff, #6759ea);
        }

        .secondary-button {
            background: #252b4c;
            color: #e0e5ff;
        }

        .secondary-button:hover:not(:disabled) {
            background: #30385f;
        }

        .remove-button {
            background: #402431;
            color: #ffc2ca;
        }

        .install-help {
            padding: 16px 18px;
            color: #bdc4dc;
        }

        .install-help strong {
            color: #f1f3ff;
        }

        .sound-options {
            padding: 15px;
            border: 1px solid rgb(190 198 255 / 10%);
            border-radius: 12px;
            background: rgb(8 11 24 / 32%);
        }

        .sound-help {
            color: #9ca5c0;
        }

        .task-list {
            gap: 9px;
        }

        .task-row {
            border-color: rgb(190 198 255 / 10%);
            background: rgb(8 11 24 / 34%);
            transition: border-color 0.2s ease, background-color 0.2s ease, transform 0.2s ease;
        }

        .task-row:hover {
            transform: translateY(-1px);
            border-color: rgb(165 180 252 / 26%);
        }

        .task-row.is-current {
            border-color: rgb(129 140 248 / 60%);
            background: linear-gradient(110deg, rgb(99 102 241 / 18%), rgb(129 140 248 / 5%));
            box-shadow: inset 3px 0 #818cf8;
        }

        .task-name {
            color: #f2f4ff;
        }

        .task-duration, .sound-help {
            line-height: 1.5;
        }

        .empty-state {
            border: 1px dashed rgb(190 198 255 / 18%);
            background: rgb(8 11 24 / 28%);
            color: #a4abc3;
        }

        .notice {
            border: 1px solid rgb(251 146 160 / 22%);
            background: #321d2b;
            color: #ffc5cb;
        }

        .install-button {
            margin-bottom: 14px;
        }

        @media (max-width: 640px) {
            body {
                padding: 22px 12px;
            }

            .timer-card, .panel {
                padding: 20px 16px;
            }

            .task-form {
                grid-template-columns: 1fr 1fr;
            }

            .task-form label:first-child {
                grid-column: 1 / -1;
            }

            .task-form button {
                grid-column: 1 / -1;
            }

            .task-row {
                align-items: flex-start;
            }
        }

        @media (prefers-reduced-motion: reduce) {
            *, *::before, *::after {
                scroll-behavior: auto !important;
                transition-duration: 0.01ms !important;
            }
        }
    </style>
</head>
<body>
    <main>
        <p class="eyebrow">Espace de concentration</p>
        <h1>Minuteur de tâches</h1>
        <p class="intro">Organise tes tâches par durée et passe automatiquement à la suivante.</p>
        <button class="primary-button install-button" id="install-button" type="button" hidden>
            Installer l’application
        </button>
        <aside class="install-help" aria-label="Installation sur l'écran d'accueil">
            <p><strong>Installer sur l’écran d’accueil :</strong> le navigateur doit ouvrir cette page depuis un site HTTPS (ou depuis localhost sur l’ordinateur), et non depuis un fichier local.</p>
            <p>Sur iPhone ou iPad, ouvre la page dans Safari, touche <strong>Partager</strong>, puis <strong>Sur l’écran d’accueil</strong>. Sur Android, utilise le bouton d’installation s’il apparaît, ou le menu du navigateur et choisis <strong>Installer l’application</strong>.</p>
        </aside>

        <p class="notice" id="notice" role="alert" hidden></p>

        <section class="timer-card" aria-label="Minuteur">
            <p class="timer-label" id="timer-label" aria-live="polite">Prêt à commencer</p>
            <p class="timer-display" id="timer-display" role="timer" aria-label="Temps restant">00:00</p>
            <div class="timer-progress" role="progressbar" aria-label="Progression de la tâche"
                 aria-valuemin="0" aria-valuemax="100" aria-valuenow="100">
                <span id="timer-progress-bar"></span>
            </div>
            <p class="timer-task" id="timer-task">Ajoute une tâche pour commencer</p>
            <div class="timer-controls">
                <button class="primary-button" id="start-button" type="button">Démarrer</button>
                <button class="secondary-button" id="reset-button" type="button">Réinitialiser</button>
                <button class="secondary-button" id="skip-button" type="button">Passer à la suivante</button>
            </div>
            <label class="loop-setting" for="loop-input">
                <input id="loop-input" type="checkbox">
                Recommencer la liste en boucle
            </label>
            <label class="loop-setting sound-setting" for="sound-input">
                <input id="sound-input" type="checkbox" checked>
                Jouer un son à la fin de chaque tâche
            </label>
            <div class="sound-options" id="sound-options">
                <label for="sound-type">
                    Type de son
                    <select id="sound-type">
                        <option value="tone">Sonnerie intégrée</option>
                        <option value="file">Fichier audio sur cet appareil</option>
                        <option value="url">Lien direct vers un fichier audio</option>
                        <option value="platform">Lien Spotify ou autre plateforme</option>
                    </select>
                </label>
                <div class="sound-field" id="sound-file-field">
                    <label for="sound-file">Choisir un fichier audio</label>
                    <input id="sound-file" type="file" accept="audio/*">
                    <span class="sound-help" id="sound-file-name">Aucun fichier sélectionné. Les formats disponibles dépendent du navigateur.</span>
                    <button class="secondary-button" id="remove-sound-file" type="button" hidden>
                        Supprimer le fichier enregistré
                    </button>
                </div>
                <label class="sound-field" id="sound-url-field" for="sound-url" hidden>
                    URL directe du fichier audio
                    <input id="sound-url" type="url" inputmode="url"
                           placeholder="https://exemple.com/son.mp3">
                    <span class="sound-help">Utilise un lien direct vers un fichier audio (MP3, OGG, WAV…). Un lien vers une page web ne suffit pas.</span>
                </label>
                <label class="sound-field" id="sound-platform-field" for="sound-platform-url" hidden>
                    Lien de la musique
                    <input id="sound-platform-url" type="url" inputmode="url"
                           placeholder="https://open.spotify.com/track/...">
                    <span class="sound-help">Spotify, YouTube et les autres plateformes bloquent généralement la lecture automatique depuis une autre page. À la fin du minuteur, un bouton permettra d’ouvrir ce lien pour lancer la musique.</span>
                </label>
            </div>
            <a class="finish-link" id="finish-link" href="#" target="_blank"
               rel="noopener noreferrer" hidden>Ouvrir la musique</a>
        </section>

        <section class="panel" aria-labelledby="add-title">
            <h2 id="add-title">Ajouter une tâche</h2>
            <form class="task-form" id="task-form">
                <label for="task-name">
                    Nom de la tâche
                    <input id="task-name" name="name" type="text" maxlength="120" required
                           placeholder="Ex. Réviser un chapitre">
                </label>
                <label for="task-minutes">
                    Minutes
                    <input id="task-minutes" name="minutes" type="number" min="0" max="5999"
                           step="1" value="5" required>
                </label>
                <label for="task-seconds">
                    Secondes
                    <input id="task-seconds" name="seconds" type="number" min="0" max="59"
                           step="1" value="0" required>
                </label>
                <button class="primary-button" type="submit">Ajouter</button>
            </form>
        </section>

        <section class="panel" aria-labelledby="list-title">
            <h2 id="list-title">Ma liste</h2>
            <ul class="task-list" id="task-list"></ul>
            <p class="empty-state" id="empty-state">Aucune tâche. Ajoute-en une ci-dessus.</p>
        </section>
    </main>

    <script>
        const STORAGE_KEY = "minuteur-taches-v1";
        const form = document.querySelector("#task-form");
        const nameInput = document.querySelector("#task-name");
        const minutesInput = document.querySelector("#task-minutes");
        const secondsInput = document.querySelector("#task-seconds");
        const taskList = document.querySelector("#task-list");
        const emptyState = document.querySelector("#empty-state");
        const notice = document.querySelector("#notice");
        const timerLabel = document.querySelector("#timer-label");
        const timerDisplay = document.querySelector("#timer-display");
        const timerProgress = document.querySelector(".timer-progress");
        const timerProgressBar = document.querySelector("#timer-progress-bar");
        const timerTask = document.querySelector("#timer-task");
        const startButton = document.querySelector("#start-button");
        const resetButton = document.querySelector("#reset-button");
        const skipButton = document.querySelector("#skip-button");
        const loopInput = document.querySelector("#loop-input");
        const soundInput = document.querySelector("#sound-input");
        const soundOptions = document.querySelector("#sound-options");
        const soundTypeInput = document.querySelector("#sound-type");
        const soundFileInput = document.querySelector("#sound-file");
        const soundFileName = document.querySelector("#sound-file-name");
        const removeSoundFileButton = document.querySelector("#remove-sound-file");
        const soundUrlInput = document.querySelector("#sound-url");
        const platformUrlInput = document.querySelector("#sound-platform-url");
        const finishLink = document.querySelector("#finish-link");
        const installButton = document.querySelector("#install-button");
        const audioElement = new Audio();

        let tasks = [];
        let currentIndex = 0;
        let remainingSeconds = 0;
        let deadline = null;
        let intervalId = null;
        let isRunning = false;
        let audioContext = null;
        let installPrompt = null;
        let selectedAudioFile = null;
        let audioObjectUrl = null;
        let databasePromise = null;
        let audioLoadToken = 0;

        window.addEventListener("beforeinstallprompt", event => {
            event.preventDefault();
            installPrompt = event;
            installButton.hidden = false;
        });

        installButton.addEventListener("click", async () => {
            if (!installPrompt) return;
            await installPrompt.prompt();
            installPrompt = null;
            installButton.hidden = true;
        });

        window.addEventListener("appinstalled", () => {
            installPrompt = null;
            installButton.hidden = true;
        });

        const isLocalServer = window.location.protocol === "http:" &&
            ["localhost", "127.0.0.1", "[::1]"].includes(window.location.hostname);
        if ("serviceWorker" in navigator &&
            (window.location.protocol === "https:" || isLocalServer)) {
            navigator.serviceWorker.register("./service-worker.js").catch(error => {
                showNotice("Le mode hors ligne n’a pas pu être activé. Vérifie que le site est servi en HTTPS.");
            });
        }

        function showNotice(message) {
            notice.textContent = message;
            notice.hidden = false;
        }

        function openAudioDatabase() {
            if (!("indexedDB" in window)) {
                return Promise.reject(new Error("Le stockage audio n'est pas disponible dans ce navigateur."));
            }
            if (databasePromise) return databasePromise;

            databasePromise = new Promise((resolve, reject) => {
                const request = indexedDB.open("minuteur-taches-audio", 1);
                request.onupgradeneeded = () => {
                    if (!request.result.objectStoreNames.contains("sounds")) {
                        request.result.createObjectStore("sounds");
                    }
                };
                request.onsuccess = () => resolve(request.result);
                request.onerror = () => reject(request.error || new Error("Impossible d'ouvrir le stockage audio."));
            }).catch(error => {
                databasePromise = null;
                throw error;
            });
            return databasePromise;
        }

        async function storeAudioFile(file) {
            const database = await openAudioDatabase();
            await new Promise((resolve, reject) => {
                const transaction = database.transaction("sounds", "readwrite");
                transaction.objectStore("sounds").put(file, "finish-sound");
                transaction.oncomplete = resolve;
                transaction.onerror = () => reject(transaction.error || new Error("Impossible d'enregistrer le fichier audio."));
                transaction.onabort = () => reject(transaction.error || new Error("L'enregistrement du fichier audio a été annulé."));
            });
        }

        async function readAudioFile() {
            const database = await openAudioDatabase();
            return new Promise((resolve, reject) => {
                const request = database.transaction("sounds", "readonly")
                    .objectStore("sounds")
                    .get("finish-sound");
                request.onsuccess = () => resolve(request.result || null);
                request.onerror = () => reject(request.error || new Error("Impossible de lire le fichier audio enregistré."));
            });
        }

        async function clearStoredAudio() {
            const database = await openAudioDatabase();
            await new Promise((resolve, reject) => {
                const transaction = database.transaction("sounds", "readwrite");
                transaction.objectStore("sounds").delete("finish-sound");
                transaction.oncomplete = resolve;
                transaction.onerror = () => reject(transaction.error || new Error("Impossible de supprimer le fichier audio."));
                transaction.onabort = () => reject(transaction.error || new Error("La suppression du fichier audio a été annulée."));
            });
        }

        function setSelectedAudioFile(file) {
            selectedAudioFile = file;
            if (audioObjectUrl) URL.revokeObjectURL(audioObjectUrl);
            audioObjectUrl = file ? URL.createObjectURL(file) : null;
            soundFileName.textContent = file
                ? `Fichier sélectionné : ${file.name}`
                : "Aucun fichier sélectionné. Les formats disponibles dépendent du navigateur.";
            removeSoundFileButton.hidden = !file;
        }

        function normalizeHttpsUrl(value) {
            let url;
            try {
                url = new URL(value.trim());
            } catch (error) {
                throw new Error("Saisis une URL valide.");
            }
            if (url.protocol !== "https:") {
                throw new Error("Utilise un lien HTTPS pour cette source audio.");
            }
            return url.href;
        }

        function normalizePlatformUrl(value) {
            const trimmed = value.trim();
            if (/^spotify:(track|album|playlist|episode|show):[a-zA-Z0-9]+$/i.test(trimmed)) {
                const [, type, id] = trimmed.split(":");
                return `https://open.spotify.com/${type.toLowerCase()}/${id}`;
            }
            return normalizeHttpsUrl(trimmed);
        }

        async function restoreSelectedAudio() {
            if (soundTypeInput.value !== "file") return;
            try {
                const file = await readAudioFile();
                if (file instanceof Blob) setSelectedAudioFile(file);
            } catch (error) {
                showNotice("Le fichier audio enregistré n'a pas pu être chargé. Sélectionne-le à nouveau.");
            }
        }

        function updateSoundOptions() {
            soundOptions.hidden = !soundInput.checked;
            document.querySelector("#sound-file-field").hidden = !soundInput.checked || soundTypeInput.value !== "file";
            document.querySelector("#sound-url-field").hidden = !soundInput.checked || soundTypeInput.value !== "url";
            document.querySelector("#sound-platform-field").hidden = !soundInput.checked || soundTypeInput.value !== "platform";
            saveSettings();
        }

        function saveSettings() {
            try {
                localStorage.setItem(STORAGE_KEY, JSON.stringify({
                    tasks,
                    loop: loopInput.checked,
                    soundEnabled: soundInput.checked,
                    soundType: soundTypeInput.value,
                    soundUrl: soundUrlInput.value.trim(),
                    platformUrl: platformUrlInput.value.trim()
                }));
                if (notice.textContent === "Les modifications sont visibles, mais n'ont pas pu être enregistrées dans ce navigateur.") {
                    notice.hidden = true;
                }
            } catch (error) {
                showNotice("Les modifications sont visibles, mais n'ont pas pu être enregistrées dans ce navigateur.");
            }
        }

        function loadSettings() {
            try {
                const saved = localStorage.getItem(STORAGE_KEY);
                if (!saved) return;
                const data = JSON.parse(saved);
                if (!data || !Array.isArray(data.tasks) || !data.tasks.every(task =>
                    task &&
                    typeof task.id === "string" &&
                    typeof task.name === "string" &&
                    Number.isSafeInteger(task.durationSeconds) &&
                    task.durationSeconds > 0
                ) || typeof data.loop !== "boolean") {
                    throw new Error("Données de minuteur invalides");
                }
                tasks = data.tasks;
                loopInput.checked = data.loop;
                soundInput.checked = data.soundEnabled !== false;
                if (["tone", "file", "url", "platform"].includes(data.soundType)) {
                    soundTypeInput.value = data.soundType;
                }
                if (typeof data.soundUrl === "string") soundUrlInput.value = data.soundUrl;
                if (typeof data.platformUrl === "string") platformUrlInput.value = data.platformUrl;
            } catch (error) {
                tasks = [];
                showNotice("Les données enregistrées sont illisibles. Le minuteur démarre avec une liste vide.");
            }
        }

        function formatTime(totalSeconds) {
            const safeSeconds = Math.max(0, Math.ceil(totalSeconds));
            const minutes = Math.floor(safeSeconds / 60);
            const seconds = safeSeconds % 60;
            return `${String(minutes).padStart(2, "0")}:${String(seconds).padStart(2, "0")}`;
        }

        function stopInterval() {
            if (intervalId !== null) {
                window.clearInterval(intervalId);
                intervalId = null;
            }
        }

        async function prepareAudio() {
            if (!soundInput.checked) return true;
            if (soundTypeInput.value !== "tone") return true;
            if (!window.AudioContext) {
                showNotice("La sonnerie n'est pas prise en charge par ce navigateur.");
                return true;
            }

            try {
                audioContext = audioContext || new AudioContext();
                if (audioContext.state === "suspended") await audioContext.resume();
                if (audioContext.state !== "running") {
                    showNotice("Le navigateur n'a pas autorisé la lecture de la sonnerie.");
                    return true;
                }
                return true;
            } catch (error) {
                showNotice("Impossible d'activer la sonnerie dans ce navigateur.");
                return true;
            }
        }

        function playFinishSound() {
            finishLink.hidden = true;
            if (!soundInput.checked) return;

            if (soundTypeInput.value === "platform") {
                try {
                    finishLink.href = normalizePlatformUrl(platformUrlInput.value);
                    finishLink.textContent = `Ouvrir la musique (${new URL(finishLink.href).hostname})`;
                    finishLink.hidden = false;
                } catch (error) {
                    showNotice(error.message || "Saisis un lien HTTPS valide vers ta musique.");
                }
                return;
            }

            let source;
            if (soundTypeInput.value === "file") {
                if (!selectedAudioFile || !audioObjectUrl) {
                    showNotice("Choisis un fichier audio avant de démarrer le minuteur.");
                    return;
                }
                source = audioObjectUrl;
            } else if (soundTypeInput.value === "url") {
                try {
                    source = normalizeHttpsUrl(soundUrlInput.value);
                } catch (error) {
                    showNotice(error.message || "Saisis une URL HTTPS valide vers un fichier audio.");
                    return;
                }
            } else {
                playBuiltInFinishSound();
                return;
            }

            audioElement.pause();
            audioElement.currentTime = 0;
            audioElement.src = source;
            audioElement.play().catch(error => {
                showNotice("Impossible de lire ce fichier audio. Vérifie son format, son lien et les autorisations de lecture.");
            });
        }

        function playBuiltInFinishSound() {
            if (!audioContext || audioContext.state !== "running") return;
            const startAt = audioContext.currentTime;
            const notes = [
                { frequency: 660, start: 0, duration: 0.18 },
                { frequency: 880, start: 0.2, duration: 0.18 },
                { frequency: 990, start: 0.4, duration: 0.32 }
            ];

            for (const note of notes) {
                const oscillator = audioContext.createOscillator();
                const gain = audioContext.createGain();
                const noteStart = startAt + note.start;
                const noteEnd = noteStart + note.duration;

                oscillator.type = "sine";
                oscillator.frequency.setValueAtTime(note.frequency, noteStart);
                gain.gain.setValueAtTime(0.0001, noteStart);
                gain.gain.exponentialRampToValueAtTime(0.18, noteStart + 0.025);
                gain.gain.exponentialRampToValueAtTime(0.0001, noteEnd);
                oscillator.connect(gain);
                gain.connect(audioContext.destination);
                oscillator.start(noteStart);
                oscillator.stop(noteEnd);
            }
        }

        function updateDisplay() {
            timerDisplay.textContent = formatTime(remainingSeconds);
            const currentTask = tasks[currentIndex];
            const progress = currentTask
                ? Math.min(100, Math.max(0, remainingSeconds / currentTask.durationSeconds * 100))
                : 0;
            timerProgressBar.style.width = `${progress}%`;
            timerProgress.setAttribute("aria-valuenow", String(Math.round(progress)));
            timerTask.textContent = currentTask
                ? currentTask.name
                : (tasks.length ? "Toutes les tâches sont terminées" : "Ajoute une tâche pour commencer");
            startButton.textContent = isRunning ? "Pause" : "Démarrer";
            startButton.disabled = tasks.length === 0;
            resetButton.disabled = tasks.length === 0;
            skipButton.disabled = tasks.length < 2;
            document.title = isRunning
                ? `${formatTime(remainingSeconds)} · ${currentTask.name} — Minuteur`
                : "Minuteur de tâches";
        }

        function renderTasks() {
            taskList.replaceChildren();
            emptyState.hidden = tasks.length > 0;

            tasks.forEach((task, index) => {
                const item = document.createElement("li");
                item.className = "task-row";
                if (index === currentIndex) item.classList.add("is-current");
                item.dataset.id = task.id;

                const details = document.createElement("div");
                details.className = "task-details";
                const name = document.createElement("p");
                name.className = "task-name";
                name.textContent = task.name;
                const duration = document.createElement("span");
                duration.className = "task-duration";
                duration.textContent = formatTime(task.durationSeconds);

                const removeButton = document.createElement("button");
                removeButton.type = "button";
                removeButton.className = "remove-button";
                removeButton.dataset.action = "remove";
                removeButton.textContent = "Supprimer";
                removeButton.setAttribute("aria-label", `Supprimer : ${task.name}`);

                details.append(name, duration);
                item.append(details, removeButton);
                taskList.append(item);
            });

            updateDisplay();
        }

        function setCurrentTask(index) {
            currentIndex = index;
            remainingSeconds = tasks[index].durationSeconds;
            renderTasks();
        }

        function finishCurrentTask() {
            stopInterval();
            remainingSeconds = 0;
            playFinishSound();
            const nextIndex = currentIndex + 1;

            if (nextIndex < tasks.length) {
                setCurrentTask(nextIndex);
                timerLabel.textContent = "Tâche suivante";
                startTimer();
                return;
            }

            if (loopInput.checked && tasks.length > 0) {
                setCurrentTask(0);
                timerLabel.textContent = "Nouvelle boucle";
                startTimer();
                return;
            }

            isRunning = false;
            timerLabel.textContent = "Toutes les tâches sont terminées";
            renderTasks();
        }

        function tick() {
            if (deadline === null) return;
            remainingSeconds = Math.max(0, Math.ceil((deadline - Date.now()) / 1000));
            updateDisplay();
            if (deadline <= Date.now()) finishCurrentTask();
        }

        function startTimer() {
            if (tasks.length === 0) return;
            if (currentIndex >= tasks.length) setCurrentTask(0);
            if (remainingSeconds <= 0) {
                remainingSeconds = tasks[currentIndex].durationSeconds;
            }
            isRunning = true;
            deadline = Date.now() + remainingSeconds * 1000;
            timerLabel.textContent = "En cours";
            stopInterval();
            intervalId = window.setInterval(tick, 200);
            renderTasks();
        }

        function pauseTimer() {
            tick();
            if (!isRunning) return;
            stopInterval();
            isRunning = false;
            deadline = null;
            timerLabel.textContent = "En pause";
            renderTasks();
        }

        function resetTimer() {
            stopInterval();
            isRunning = false;
            deadline = null;
            audioElement.pause();
            audioElement.currentTime = 0;
            finishLink.hidden = true;
            if (tasks.length > 0) {
                currentIndex = 0;
                remainingSeconds = tasks[0].durationSeconds;
                timerLabel.textContent = "Prêt à commencer";
            } else {
                currentIndex = 0;
                remainingSeconds = 0;
                timerLabel.textContent = "Prêt à commencer";
            }
            renderTasks();
        }

        function advanceTask() {
            if (tasks.length < 2) return;
            const nextIndex = currentIndex + 1 < tasks.length ? currentIndex + 1 : 0;
            setCurrentTask(nextIndex);
            timerLabel.textContent = isRunning ? "Tâche suivante" : "Prêt à commencer";
            if (isRunning) {
                deadline = Date.now() + remainingSeconds * 1000;
                stopInterval();
                intervalId = window.setInterval(tick, 200);
            }
        }

        form.addEventListener("submit", event => {
            event.preventDefault();
            const name = nameInput.value.trim();
            const minutes = minutesInput.valueAsNumber;
            const seconds = secondsInput.valueAsNumber;
            const durationSeconds = minutes * 60 + seconds;

            if (!name || !Number.isInteger(minutes) || minutes < 0 ||
                !Number.isInteger(seconds) || seconds < 0 || seconds > 59 ||
                durationSeconds <= 0) {
                showNotice("Saisis un nom et une durée supérieure à zéro (secondes entre 0 et 59).");
                return;
            }

            tasks.push({
                id: `${Date.now()}-${Math.random().toString(36).slice(2)}`,
                name,
                durationSeconds
            });
            if (tasks.length === 1) {
                currentIndex = 0;
                remainingSeconds = durationSeconds;
                timerLabel.textContent = "Prêt à commencer";
            }
            saveSettings();
            renderTasks();
            form.reset();
            minutesInput.value = "5";
            secondsInput.value = "0";
            nameInput.focus();
        });

        soundInput.addEventListener("change", () => {
            if (!soundInput.checked) {
                audioElement.pause();
                finishLink.hidden = true;
            }
            updateSoundOptions();
        });

        soundTypeInput.addEventListener("change", () => {
            audioElement.pause();
            finishLink.hidden = true;
            updateSoundOptions();
        });
        soundUrlInput.addEventListener("change", saveSettings);
        platformUrlInput.addEventListener("change", saveSettings);

        soundFileInput.addEventListener("change", async () => {
            const file = soundFileInput.files[0];
            if (!file) return;

            const validAudio = file.type.startsWith("audio/") ||
                /\.(aac|flac|m4a|mp3|oga|ogg|opus|wav|weba|webm)$/i.test(file.name);
            if (!validAudio) {
                soundFileInput.value = "";
                showNotice("Choisis un fichier audio pris en charge.");
                return;
            }

            const loadToken = ++audioLoadToken;
            try {
                await storeAudioFile(file);
                if (loadToken !== audioLoadToken) return;
                setSelectedAudioFile(file);
                soundTypeInput.value = "file";
                updateSoundOptions();
                notice.hidden = true;
            } catch (error) {
                showNotice("Le fichier audio n'a pas pu être enregistré sur cet appareil.");
            }
        });

        removeSoundFileButton.addEventListener("click", async () => {
            ++audioLoadToken;
            try {
                await clearStoredAudio();
                audioElement.pause();
                setSelectedAudioFile(null);
                soundFileInput.value = "";
                soundTypeInput.value = "tone";
                updateSoundOptions();
                notice.hidden = true;
            } catch (error) {
                showNotice("Le fichier audio enregistré n'a pas pu être supprimé.");
            }
        });

        taskList.addEventListener("click", event => {
            const button = event.target.closest("button[data-action='remove']");
            if (!button) return;
            const item = button.closest("li[data-id]");
            const removedIndex = tasks.findIndex(task => task.id === item.dataset.id);
            if (removedIndex < 0) return;

            const removedCurrentTask = removedIndex === currentIndex;
            tasks.splice(removedIndex, 1);
            if (removedIndex < currentIndex) currentIndex--;

            if (tasks.length === 0) {
                resetTimer();
            } else if (removedCurrentTask) {
                if (currentIndex >= tasks.length) currentIndex = 0;
                remainingSeconds = tasks[currentIndex].durationSeconds;
                if (isRunning) {
                    deadline = Date.now() + remainingSeconds * 1000;
                    stopInterval();
                    intervalId = window.setInterval(tick, 200);
                } else {
                    timerLabel.textContent = "Prêt à commencer";
                }
            }

            saveSettings();
            renderTasks();
        });

        startButton.addEventListener("click", async () => {
            if (isRunning) pauseTimer();
            else if (await prepareAudio()) startTimer();
        });

        resetButton.addEventListener("click", resetTimer);
        skipButton.addEventListener("click", advanceTask);
        loopInput.addEventListener("change", saveSettings);

        loadSettings();
        updateSoundOptions();
        restoreSelectedAudio();
        if (tasks.length > 0) remainingSeconds = tasks[0].durationSeconds;
        renderTasks();
    </script>
</body>
</html>
