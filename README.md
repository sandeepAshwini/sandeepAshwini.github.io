<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QuickSpeak TTS</title>
  <style>
    body { font-family: system-ui, sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; background: #f4f7f6; }
    .card { background: white; padding: 2rem; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); width: 100%; max-width: 500px; }
    textarea { width: 100%; height: 150px; padding: 10px; border: 1px solid #ddd; border-radius: 8px; resize: none; box-sizing: border-box; }
    .controls { margin-top: 20px; display: grid; gap: 15px; }
    .row { display: flex; flex-direction: column; gap: 5px; }
    button { background: #007bff; color: white; border: none; padding: 12px; border-radius: 8px; cursor: pointer; font-weight: bold; }
    button:hover { background: #0056b3; }
  </style>
</head>
<body>
  <div class="card">
    <h2>QuickSpeak</h2>
    <textarea id="text-input" placeholder="Type something here..."></textarea>

    <div class="controls">
      <div class="row">
        <label>Language/Voice</label>
        <select id="voice-select"></select>
      </div>

      <div class="row">
        <label>Speed: <span id="speed-val">1</span>x</label>
        <input type="range" id="rate" min="0.5" max="2" value="1" step="0.1">
      </div>

      <button id="speak-btn">Play Audio</button>
    </div>
  </div>

  <script>
    const synth = window.speechSynthesis;
    const voiceSelect = document.querySelector('#voice-select');
    const speakBtn = document.querySelector('#speak-btn');
    const textInput = document.querySelector('#text-input');
    const rateInput = document.querySelector('#rate');
    const speedVal = document.querySelector('#speed-val');

    let voices = [];

    // Populate the list of available system voices
    function populateVoiceList() {
      voices = synth.getVoices();
      voiceSelect.innerHTML = voices
        .map(voice => `<option value="${voice.name}">${voice.name} (${voice.lang})</option>`)
        .join('');
    }

    populateVoiceList();
    if (speechSynthesis.onvoiceschanged !== undefined) {
      speechSynthesis.onvoiceschanged = populateVoiceList;
    }

    // Update speed label
    rateInput.addEventListener('input', () => speedVal.textContent = rateInput.value);

    // Speak function
    speakBtn.addEventListener('click', () => {
      if (synth.speaking) { synth.cancel(); } // Stop if already playing

      const utterThis = new SpeechSynthesisUtterance(textInput.value);
      const selectedVoice = voices.find(v => v.name === voiceSelect.value);

      utterThis.voice = selectedVoice;
      utterThis.rate = rateInput.value;

      synth.speak(utterThis);
    });
  </script>
</body>
</html>
