<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Goodnight</title>
    <style>
        :root {
            --primary-color: #ff6b81;
            --primary-dark: #ff4757;
            --bg-start: #ff9a9e;
            --bg-mid: #fad0c4;
            --overlay: rgba(0, 0, 0, 0.4);
            --text-primary: #fff;
        }

        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(to right, var(--bg-start), var(--bg-mid), var(--bg-mid));
            overflow: hidden;
            color: var(--text-primary);
            text-align: center;
        }

        .message-box {
            background: var(--overlay);
            padding: 40px;
            border-radius: 20px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.5);
            z-index: 2;
            position: relative;
            max-width: 600px;
        }

        h1 {
            font-size: 3em;
            margin-bottom: 20px;
            margin-top: 0;
        }

        p {
            font-size: 1.5em;
            line-height: 1.8;
            margin: 10px 0;
        }

        .heart-beat {
            color: var(--primary-color);
            font-size: 2em;
            animation: beat 1s infinite;
            display: inline-block;
        }

        @keyframes beat {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.3); }
        }

        button {
            margin-top: 20px;
            padding: 12px 24px;
            font-size: 1.2em;
            font-weight: 600;
            background-color: var(--primary-color);
            color: var(--text-primary);
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            font-family: inherit;
        }

        button:hover {
            background-color: var(--primary-dark);
            transform: translateY(-2px);
        }

        button:active {
            transform: translateY(0);
        }

        button:focus-visible {
            outline: 3px solid rgba(255, 255, 255, 0.5);
            outline-offset: 2px;
        }

        #quote {
            opacity: 0;
            transition: opacity 1s ease-in-out;
            min-height: 60px;
            font-style: italic;
            margin: 20px 0;
        }

        #quote.show {
            opacity: 1;
        }

        /* Falling hearts */
        .heart-fall {
            position: fixed;
            top: -50px;
            font-size: 1em;
            color: var(--primary-color);
            pointer-events: none;
            animation: fall linear forwards;
        }

        @keyframes fall {
            0% { 
                transform: translateY(0) rotate(0deg); 
                opacity: 1; 
            }
            100% { 
                transform: translateY(110vh) rotate(360deg); 
                opacity: 0; 
            }
        }

        @media (max-width: 600px) {
            .message-box {
                padding: 25px;
                margin: 20px;
            }

            h1 {
                font-size: 2em;
                margin-bottom: 15px;
            }

            p {
                font-size: 1.1em;
            }

            button {
                padding: 10px 20px;
                font-size: 1em;
            }
        }
    </style>
</head>
<body>
    <div class="message-box">
        <h1>goodnight</h1>
        <p>
            Sleep well and rest easy tonight.<br>
            Sweet dreams ahead for you.<br>
            Wishing you a peaceful night's sleep.<br>
            Take care, and we'll talk tomorrow. <span class="heart-beat" aria-label="heart">❤️</span>
        </p>
        <p id="quote" role="status" aria-live="polite"></p>
        <button id="quoteBtn" aria-label="Generate a new sweet message">New Sweet Message</button>
    </div>

    <script>
        // Configuration constants
        const CONFIG = {
            TYPE_SPEED: 28,
            FADE_DELAY: 350,
            AUTO_INTERVAL: 12000,
            HEART_INTERVAL: 500,
            HEART_LIFETIME: 10000,
            MIN_HEART_SIZE: 10,
            MAX_HEART_SIZE: 30,
            MIN_FALL_DURATION: 5,
            MAX_FALL_DURATION: 10,
            STORAGE_ORDER: 'goodnight_quote_order',
            STORAGE_INDEX: 'goodnight_quote_index'
        };

        // Sweet quotes
        const QUOTES = [
            "Sleep well, I'll be thinking about you tonight.",
            "Sweet dreams, you've got a great day ahead tomorrow.",
            "Good night, I really like getting to know you.",
            "Sleep tight, there's something special about you.",
            "Night, I can't stop thinking about your laugh.",
            "Get some rest, I'd love to chat more with you.",
            "Sleep well, you are amazing, you know that?",
            "Good night, I genuinely enjoy talking with you.",
            "Rest easy, you're on my mind.",
            "Night, you make everything more interesting.",
            "Sweet dreams, I'm curious to learn more about you.",
            "Sleep peacefully, you deserve all the best.",
            "Good night, I find myself thinking about you all the time.",
            "Rest well, I think I'm getting to like you more each day.",
            "Night, there's something I really like about your vibe.",
            "Sleep tight, I hope we can chat again soon.",
            "Good night, I hope you sleep well.",
            "Rest well, there's something different about you and I love it.",
            "Night, I'm genuinely interested in getting to know you."
        ];

        // Application state
        const app = {
            quoteElement: document.getElementById('quote'),
            quoteBtn: document.getElementById('quoteBtn'),
            order: [],
            orderIndex: 0,
            isTyping: false
        };

        /**
         * Fisher-Yates shuffle algorithm
         * @param {array} array - Array to shuffle
         * @returns {array} Shuffled array
         */
        function shuffle(array) {
            const arr = [...array];
            for (let i = arr.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [arr[i], arr[j]] = [arr[j], arr[i]];
            }
            return arr;
        }

        /**
         * Retrieve or initialize shuffled quote order from localStorage
         * @returns {array} Indices in randomized order
         */
        function initializeOrder() {
            try {
                const stored = localStorage.getItem(CONFIG.STORAGE_ORDER);
                if (stored) {
                    const parsed = JSON.parse(stored);
                    if (Array.isArray(parsed) && parsed.length === QUOTES.length) {
                        return parsed;
                    }
                }
            } catch (e) {
                console.warn('Failed to load quote order from storage', e);
            }

            // Create new shuffled order
            const newOrder = shuffle(Array.from({ length: QUOTES.length }, (_, i) => i));
            try {
                localStorage.setItem(CONFIG.STORAGE_ORDER, JSON.stringify(newOrder));
                localStorage.setItem(CONFIG.STORAGE_INDEX, '0');
            } catch (e) {
                console.warn('Failed to save quote order to storage', e);
            }
            return newOrder;
        }

        /**
         * Load current quote index from localStorage
         * @returns {number} Current index in the order
         */
        function loadIndex() {
            try {
                const stored = localStorage.getItem(CONFIG.STORAGE_INDEX);
                const index = parseInt(stored, 10);
                return !isNaN(index) && index >= 0 ? index : 0;
            } catch (e) {
                console.warn('Failed to load quote index', e);
                return 0;
            }
        }

        /**
         * Type out text character by character
         * @param {string} text - Text to type
         * @param {number} charIndex - Current character index
         */
        function typeQuote(text, charIndex = 0) {
            if (charIndex === 0) {
                app.quoteElement.textContent = '';
            }

            if (charIndex < text.length) {
                app.quoteElement.textContent += text.charAt(charIndex);
                setTimeout(() => typeQuote(text, charIndex + 1), CONFIG.TYPE_SPEED);
            } else {
                app.isTyping = false;
            }
        }

        /**
         * Generate and display a new quote
         */
        function generateQuote() {
            if (app.isTyping) return; // Prevent overlapping animations
            app.isTyping = true;

            app.quoteElement.classList.remove('show');

            setTimeout(() => {
                // Get next quote index
                const quoteIndex = app.order[app.orderIndex % app.order.length];
                const quote = QUOTES[quoteIndex];

                // Update index
                app.orderIndex = (app.orderIndex + 1) % app.order.length;
                try {
                    localStorage.setItem(CONFIG.STORAGE_INDEX, String(app.orderIndex));
                } catch (e) {
                    console.warn('Failed to save quote index', e);
                }

                // Reshuffle if cycle is complete
                if (app.orderIndex === 0) {
                    app.order = shuffle(Array.from({ length: QUOTES.length }, (_, i) => i));
                    try {
                        localStorage.setItem(CONFIG.STORAGE_ORDER, JSON.stringify(app.order));
                    } catch (e) {
                        console.warn('Failed to save shuffled order', e);
                    }
                }

                // Display quote with fade-in animation
                app.quoteElement.classList.add('show');
                typeQuote(quote);
            }, CONFIG.FADE_DELAY);
        }

        /**
         * Create a falling heart animation
         */
        function createHeart() {
            const heart = document.createElement('div');
            heart.className = 'heart-fall';
            heart.textContent = '❤️';
            heart.style.left = Math.random() * window.innerWidth + 'px';
            heart.style.fontSize = (CONFIG.MIN_HEART_SIZE + Math.random() * (CONFIG.MAX_HEART_SIZE - CONFIG.MIN_HEART_SIZE)) + 'px';
            const duration = CONFIG.MIN_FALL_DURATION + Math.random() * (CONFIG.MAX_FALL_DURATION - CONFIG.MIN_FALL_DURATION);
            heart.style.animationDuration = duration + 's';

            document.body.appendChild(heart);

            // Clean up after animation
            setTimeout(() => {
                if (heart.parentElement) {
                    heart.remove();
                }
            }, CONFIG.HEART_LIFETIME);
        }

        /**
         * Initialize the application
         */
        function init() {
            // Load or create quote order
            app.order = initializeOrder();
            app.orderIndex = loadIndex();

            // Attach event listener to button
            app.quoteBtn.addEventListener('click', generateQuote);

            // Generate initial quote
            generateQuote();

            // Auto-generate quotes
            setInterval(generateQuote, CONFIG.AUTO_INTERVAL);

            // Create falling hearts
            setInterval(createHeart, CONFIG.HEART_INTERVAL);
        }

        // Start application when DOM is ready
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
    </script>
</body>
</html>
