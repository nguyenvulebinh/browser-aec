# Browser-based Acoustic Echo Cancellation (AEC) Demo

A comprehensive demonstration of browser-based acoustic echo cancellation using WebRTC loopback technique. This self-contained demo shows how to prevent locally generated audio (like TTS voices) from interfering with microphone input in web applications.

## 🌐 Live Demo

**[Try the Demo →](https://nguyenvulebinh.github.io/browser-aec/)**

## 🎯 What This Demo Shows

This interactive demonstration illustrates how voice assistants and audio applications can use browser-built echo cancellation to remove their own generated audio from microphone input in real-time. The demo is completely self-contained with no external dependencies.

### Key Features:
- **Real-time AEC Toggle**: Compare echo cancellation ON vs OFF instantly
- **Local TTS Sample**: Uses a built-in TTS voice for realistic testing
- **Live Audio Visualization**: See microphone input and generated audio waveforms
- **Recording Playback**: Hear the processed microphone audio after AEC
- **Cross-browser Support**: Works in Chrome, Firefox, Safari (with compatibility notes)

## 🔧 The Technical Problem

When building voice assistants or audio applications, locally generated audio (TTS responses, notification sounds, etc.) can be picked up by the microphone, causing:
- False wake word triggers
- Interference with voice recognition
- Audio feedback loops
- Poor user experience

Browser's built-in echo cancellation only works with audio arriving through WebRTC connections, not locally generated audio.

## 💡 The WebRTC Loopback Solution

This demo implements a clever technique using WebRTC peer connections:

### How It Works:

1. **Create WebRTC Loopback**: Set up two peer connections (`pc1 ↔ pc2`) that connect to each other
2. **Route Local Audio**: Send locally generated audio through the WebRTC connection
3. **AEC Recognition**: Browser AEC now "sees" this as "remote participant audio"
4. **Automatic Cancellation**: AEC automatically removes this audio from microphone input

### Code Flow:
```
Local Audio → Web Audio API → MediaStream → WebRTC → Browser AEC → Clean Microphone
```

## 🚀 Implementation Guide

### Core Components:

1. **Microphone Setup with AEC**:
```javascript
const micStream = await navigator.mediaDevices.getUserMedia({
    audio: {
        echoCancellation: true,  // Enable browser AEC
        noiseSuppression: true,
        autoGainControl: true
    }
});
```

2. **WebRTC Peer Connection Loopback**:
```javascript
const pc1 = new RTCPeerConnection();
const pc2 = new RTCPeerConnection();

// Set up ICE candidate exchange
pc1.onicecandidate = e => pc2.addIceCandidate(e.candidate);
pc2.onicecandidate = e => pc1.addIceCandidate(e.candidate);

// Handle incoming audio (the key to AEC)
pc2.ontrack = e => {
    remoteAudio.srcObject = e.streams[0]; // This makes AEC "aware"
};
```

3. **Audio Routing Through WebRTC**:
```javascript
// Convert Web Audio to MediaStream
const destination = audioContext.createMediaStreamDestination();
audioSource.connect(gainNode).connect(destination);

// Send through WebRTC
const track = destination.stream.getAudioTracks()[0];
pc1.addTrack(track, destination.stream);

// Renegotiate connection for new track
pc1.createOffer().then(offer => {
    // Standard WebRTC offer/answer exchange
});
```

## 📱 Browser Compatibility

| Browser | AEC Support | Recording | Playback | Notes |
|---------|-------------|-----------|----------|-------|
| Chrome | ✅ Full | ✅ WebM | ✅ WebM | Best experience |
| Firefox | ✅ Full | ✅ WebM | ✅ WebM | Full support |
| Safari | ✅ AEC | ✅ WebM | ⚠️ Limited | Download to play recordings |
| Edge | ✅ Full | ✅ WebM | ✅ WebM | Same as Chrome |

## 🎓 Educational Value

This demo serves as:
- **Working Example**: Fully functional implementation you can use
- **Learning Resource**: Comprehensive code comments explaining each step
- **Testing Tool**: Interactive way to understand AEC behavior
- **Development Guide**: Template for implementing in your own projects

## 🛠️ Usage in Your Projects

This simplified demo demonstrates the core WebRTC AEC technique. To use in your projects:

### For Voice Assistants:
- Load your TTS audio into Web Audio API
- Route TTS audio through WebRTC peer connection loopback before playing
- Enable AEC in microphone constraints
- Use this technique to prevent self-triggering

### For Audio Applications:
- Apply to any locally generated audio that might interfere with microphone
- Great for music applications, notification sounds, or audio feedback prevention

### For Video Conferencing:
- Enhance existing WebRTC applications with better echo control
- Useful for custom audio processing pipelines

## 📝 Technical Notes

### Self-Contained Demo:
- No external dependencies - includes local TTS sample
- Works offline once loaded
- Single HTML file with embedded audio

### Why WebRTC?
Browser AEC was designed for video conferencing where:
- Microphone captures your voice ✅
- Remote participants speak through WebRTC ✅
- AEC cancels remote voices from your microphone ✅

Our technique makes locally generated audio appear as "remote participant audio" to the AEC system.

### Performance Considerations:
- Minimal latency added by WebRTC processing
- Uses browser's optimized native AEC algorithms
- No additional CPU overhead for echo cancellation logic

### Limitations:
- Requires HTTPS for microphone access (when served from web server)
- WebRTC adds slight audio delay (normal for real-time processing)
- Safari has limited audio format support for playback

## 📚 Learn More

- [WebRTC API Documentation](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)
- [Web Audio API Guide](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [MediaDevices.getUserMedia()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)

## 🤝 Contributing

This is an educational demonstration. Feel free to:
- Fork and experiment
- Submit improvements
- Share with others learning about audio processing
- Use as a foundation for your own projects

## 📄 License

MIT License - Feel free to use this code in your own projects.

---

**Built with ❤️ to demonstrate the power of browser-based audio processing** 