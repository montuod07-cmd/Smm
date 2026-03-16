<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Music Generator</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #121212;
            color: #ffffff;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 50px;
        }
        .container {
            background-color: #1e1e1e;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.5);
            width: 100%;
            max-width: 500px;
            text-align: center;
        }
        input, select, button {
            width: 90%;
            padding: 12px;
            margin: 10px 0;
            border-radius: 5px;
            border: none;
            font-size: 16px;
        }
        input, select {
            background-color: #2d2d2d;
            color: white;
        }
        button {
            background-color: #6200ea;
            color: white;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.3s;
        }
        button:hover {
            background-color: #7c4dff;
        }
        button:disabled {
            background-color: #555;
            cursor: not-allowed;
        }
        #audio-player {
            width: 100%;
            margin-top: 20px;
            display: none;
        }
        .loader {
            display: none;
            margin: 20px auto;
            border: 4px solid #f3f3f3;
            border-top: 4px solid #6200ea;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .status {
            font-size: 14px;
            color: #aaaaaa;
            margin-top: 10px;
        }
    </style>
</head>
<body>

    <div class="container">
        <h2>AI Music Generator</h2>
        <p>Powered by Hugging Face (MusicGen)</p>

        <input type="text" id="prompt" placeholder="Describe the music (e.g., Upbeat Bollywood dance track)..." value="Upbeat Hindi Bollywood style dance track with tabla and sitar">
        
        <button id="generate-btn" onclick="generateMusic()">Generate Music (Instrumental)</button>
        
        <div class="loader" id="loader"></div>
        <div class="status" id="status-text"></div>

        <audio id="audio-player" controls></audio>
    </div>

    <script>
        // WARNING: Exposing your API key in frontend code is a security risk.
        // For production, move this logic to a backend server.
        const API_KEY = "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"; 
        const API_URL = "https://api-inference.huggingface.co/models/facebook/musicgen-small";

        async function generateMusic() {
            const promptInput = document.getElementById('prompt').value;
            const generateBtn = document.getElementById('generate-btn');
            const loader = document.getElementById('loader');
            const statusText = document.getElementById('status-text');
            const audioPlayer = document.getElementById('audio-player');

            if (!promptInput) {
                alert("Please enter a music description.");
                return;
            }

            // Update UI for loading state
            generateBtn.disabled = true;
            loader.style.display = "block";
            audioPlayer.style.display = "none";
            statusText.innerText = "Connecting to AI... Generating audio usually takes 1-3 minutes.";

            try {
                const response = await fetch(API_URL, {
                    method: "POST",
                    headers: {
                        "Authorization": `Bearer ${API_KEY}`,
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({ inputs: promptInput })
                });

                if (!response.ok) {
                    const errorData = await response.json();
                    if (errorData.error && errorData.error.includes('currently loading')) {
                         throw new Error(`The model is currently waking up. Estimated time: ${errorData.estimated_time} seconds. Please try again in a moment.`);
                    }
                    throw new Error(errorData.error || "Failed to generate music.");
                }

                // The API returns the audio file as a Blob
                const audioBlob = await response.blob();
                const audioUrl = URL.createObjectURL(audioBlob);

                // Set the audio player source and display it
                audioPlayer.src = audioUrl;
                audioPlayer.style.display = "block";
                statusText.innerText = "Music generated successfully! (Instrumental only)";

            } catch (error) {
                statusText.innerText = `Error: ${error.message}`;
            } finally {
                // Reset UI
                generateBtn.disabled = false;
                loader.style.display = "none";
            }
        }
    </script>
</body>
</html>
