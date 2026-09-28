<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Scaledrone WebRTC Audio Streamer</title>
  <script type="text/javascript" src="https://cdn.scaledrone.com/scaledrone.min.js"></script>
  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background-color: #121212;
      color: #ffffff;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }

    body.mic-active {
      background-color: #000000;
    }

    .container {
      background: #1e1e1e;
      padding: 2rem;
      border-radius: 12px;
      box-shadow: 0 8px 24px rgba(0,0,0,0.5);
      text-align: center;
      max-width: 400px;
      width: 90%;
    }

    button {
      background-color: #007aff;
      color: white;
      border: none;
      padding: 12px 24px;
      font-size: 1rem;
      font-weight: 600;
      border-radius: 8px;
      cursor: pointer;
      margin: 10px 5px;
      transition: background-color 0.2s ease;
    }

    button:hover {
      background-color: #0056b3;
    }

    .device-btn {
      display: block;
      width: 100%;
      background-color: #2a2a2a;
      border: 1px solid #444;
      margin: 8px 0;
      padding: 14px;
      text-align: left;
      border-radius: 8px;
      color: #fff;
    }

    .device-btn.active {
      background-color: #34c759;
      color: #000;
      font-weight: bold;
      border-color: #34c759;
    }

    .hidden {
      display: none !important;
    }

    .status {
      font-size: 0.85rem;
      color: #8e8e93;
      margin-top: 15px;
    }
  </style>
</head>
<body>

  <!-- Mode Selection View -->
  <div id="selection-view" class="container">
    <h2>Select Role</h2>
    <p>Choose whether this device acts as an audio transmitter or listener.</p>
    <button onclick="startMode('mic')">Mic (Transmitter)</button>
    <button onclick="startMode('listener')">Listener (Receiver)</button>
  </div>

  <!-- Mic Active View (Blacked out) -->
  <div id="mic-view" class="hidden"></div>

  <!-- Listener View -->
  <div id="listener-view" class="container hidden">
    <h2>Available Microphones</h2>
    <p>Select any device to listen live:</p>
    <div id="device-list">
      <div class="status">Connecting to Scaledrone signaling...</div>
    </div>
    <div id="connection-status" class="status">Not connected</div>
    <audio id="remoteAudio" autoplay playsinline controls style="width: 100%; margin-top: 15px;"></audio>
  </div>

  <script>
    const CHANNEL_ID = 'Sw5scfH0QXEhMUkf';
    const ROOM_NAME = 'observable-audio-room';

    // Multiple STUN servers ensure NAT/Firewall traversal across different networks
    const configuration = {
      iceServers: [
        { urls: 'stun:stun.l.google.com:19302' },
        { urls: 'stun:stun1.l.google.com:19302' },
        { urls: 'stun:stun2.l.google.com:19302' },
        { urls: 'stun:stun.cloudflare.com:3478' }
      ]
    };

    let drone;
    let room;
    let role;
    let localStream;
    let pc;
    let selectedMicTargetId = null;

    function onError(error) {
      console.error('WebRTC/Scaledrone Error:', error);
    }

    function sendMessage(message, targetId) {
      drone.publish({
        room: ROOM_NAME,
        message: { ...message, target: targetId }
      });
    }

    function startMode(selectedRole) {
      role = selectedRole;
      document.getElementById('selection-view').classList.add('hidden');

      if (role === 'mic') {
        document.body.classList.add('mic-active');
        document.getElementById('mic-view').classList.remove('hidden');
        initMic();
      } else {
        document.getElementById('listener-view').classList.remove('hidden');
        initListener();
      }
    }

    function initDrone(userData) {
      drone = new ScaleDrone(CHANNEL_ID, { data: userData });

      drone.on('open', error => {
        if (error) return onError(error);

        room = drone.subscribe(ROOM_NAME);

        room.on('open', error => {
          if (error) onError(error);
        });

        room.on('members', members => {
          if (role === 'listener') renderDeviceList(members);
        });

        room.on('member_join', () => {
          if (role === 'listener' && room) {
            renderDeviceList(room.members);
          }
        });

        room.on('member_leave', member => {
          if (role === 'listener') {
            if (member.id === selectedMicTargetId && pc) {
              pc.close();
              document.getElementById('connection-status').textContent = 'Selected Mic disconnected.';
            }
            if (room) renderDeviceList(room.members);
          }
        });

        startListeningToSignals();
      });
    }

    // --- MIC MODE ---
    function initMic() {
      // Constraints tuned specifically for clean live audio transfer
      navigator.mediaDevices.getUserMedia({ 
        audio: {
          echoCancellation: true,
          noiseSuppression: false,
          autoGainControl: true
        }, 
        video: false 
      }).then(stream => {
        localStream = stream;
        const micName = `Mic ${Math.floor(1000 + Math.random() * 9000)}`;
        initDrone({ name: micName, type: 'mic' });
      }).catch(err => {
        alert('Microphone access denied: ' + err.message);
      });
    }

    // --- LISTENER MODE ---
    function initListener() {
      initDrone({ name: 'Listener', type: 'listener' });
    }

    function renderDeviceList(members) {
      const listContainer = document.getElementById('device-list');
      listContainer.innerHTML = '';

      const mics = (members || []).filter(m => m.clientData && m.clientData.type === 'mic');

      if (mics.length === 0) {
        listContainer.innerHTML = '<div class="status">No active microphones found.</div>';
        return;
      }

      mics.forEach(mic => {
        const btn = document.createElement('button');
        btn.className = 'device-btn';
        if (mic.id === selectedMicTargetId) btn.classList.add('active');
        btn.textContent = `🎤 ${mic.clientData.name}`;
        btn.onclick = () => connectToMic(mic.id, mic.clientData.name);
        listContainer.appendChild(btn);
      });
    }

    // --- WEBRTC CONNECTION SETUP ---
    function connectToMic(targetId, name) {
      if (pc) {
        pc.close();
      }

      selectedMicTargetId = targetId;
      document.getElementById('connection-status').textContent = `Connecting to ${name}...`;

      const remoteAudio = document.getElementById('remoteAudio');

      // Force-play audio element to bypass browser autoplay blocks
      remoteAudio.play().catch(() => {});

      pc = new RTCPeerConnection(configuration);

      pc.onicecandidate = event => {
        if (event.candidate) {
          sendMessage({ candidate: event.candidate }, targetId);
        }
      };

      // Set stream directly when audio track arrives
      pc.ontrack = event => {
        if (event.streams && event.streams[0]) {
          remoteAudio.srcObject = event.streams[0];
        } else {
          remoteAudio.srcObject = new MediaStream([event.track]);
        }

        remoteAudio.play().then(() => {
          document.getElementById('connection-status').textContent = `Streaming live from ${name}`;
        }).catch(() => {
          document.getElementById('connection-status').textContent = `Connected! Tap play on the audio player below.`;
        });
      };

      // Request audio-only reception
      pc.addTransceiver('audio', { direction: 'recvonly' });

      pc.createOffer().then(offer => {
        return pc.setLocalDescription(offer);
      }).then(() => {
        sendMessage({ sdp: pc.localDescription }, targetId);
      }).catch(onError);
    }

    // --- SIGNALING LISTENER ---
    function startListeningToSignals() {
      room.on('data', (message, client) => {
        if (!client || client.id === drone.clientId) return;
        if (message.target && message.target !== drone.clientId) return;

        if (role === 'mic') {
          if (message.sdp && message.sdp.type === 'offer') {
            if (pc) pc.close();

            pc = new RTCPeerConnection(configuration);

            pc.onicecandidate = event => {
              if (event.candidate) {
                sendMessage({ candidate: event.candidate }, client.id);
              }
            };

            // Attach mic track to stream
            if (localStream) {
              localStream.getAudioTracks().forEach(track => {
                pc.addTrack(track, localStream);
              });
            }

            pc.setRemoteDescription(new RTCSessionDescription(message.sdp))
              .then(() => pc.createAnswer())
              .then(answer => pc.setLocalDescription(answer))
              .then(() => {
                sendMessage({ sdp: pc.localDescription }, client.id);
              })
              .catch(onError);

          } else if (message.candidate && pc) {
            pc.addIceCandidate(new RTCIceCandidate(message.candidate)).catch(onError);
          }

        } else if (role === 'listener') {
          if (message.sdp && message.sdp.type === 'answer') {
            pc.setRemoteDescription(new RTCSessionDescription(message.sdp)).catch(onError);
          } else if (message.candidate && pc) {
            pc.addIceCandidate(new RTCIceCandidate(message.candidate)).catch(onError);
          }
        }
      });
    }
  </script>
</body>
</html>
