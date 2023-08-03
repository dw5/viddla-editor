<template>

<div id="editorUI" class="dark-mode" ref="editorUI" >
  <div class="box" id="editor-box" >
    <div class="feature-buttons">
      <!-- Add your buttons for cut and trim, blur, and placeholder here -->
      <button>1. Cut and Trim</button>
      <button>2. Blur</button>
      <button>3. Audioswap</button>
    </div>
    <div id="video-player">
        <video id="video-player" controls :src="videoUrl" ref="videoPlayer"></video>
    </div>
  </div>
  <div class="box" id="time-box" style="display: flex;">
    <div class="feature-buttons">
        <input id="current-time" v-model="currentTime" @input="updateVideoTime" style="background-color: #111;" autocomplete="off" role="timer" type="text" spellcheck="false" maxlength="7">
    </div>
    <div style="margin-left: auto;">
        ZOOM
    </div>
    <!-- initzoom=(video.duration / viewport width)/5, howmanytomake=video.duration*(initzoom*thetimeszoomedin) ??-->
  </div>
  <div class="box" id="timeline-box">
    <!-- Sub-timeline for Blur Objects -->
    <div class="timeline-content-wrapper">
      <div class="timeline-container">
        <div class="timeline-header">Blurs</div>
        <div class="timeline-content-item">
          <div class="timeline-blur">
            <div class="blur-object" style="left: 20px;">Blur 2</div>
          </div>
          <div class="timeline-blur">
            <div class="blur-object" style="left: 20px;">Blur 3</div>
          </div>
        </div>
      </div>
      <!-- Timeline for audio -->
      <div class="timeline-container">
        <div class="timeline-header">Audio</div>
        <div class="timeline-audio">audio https://github.com/katspaugh/wavesurfer.js yes could support video ref: https://wilsonlmh.github.io/fiveLoadSub/
          <!-- Add your audio waveform canvas or placeholder image here -->
          <div id="waveform"></div>
        </div>
      </div>
      <!-- Timeline for video -->
      <div class="timeline-container">
        <div class="timeline-header">Video</div>
        <div class="timeline-video">
          <!-- Add your video object with thumbnails here -->
          Video Thumbnails https://github.com/Rajesh-Royal/vThumb.js
        </div>
      </div>
    </div>
  </div>
</div>

</template>
<script type="module">
import WaveSurfer from 'wavesurfer.js';
export default {
    mounted() {
    // Get a reference to the video player element
    const videoPlayer = this.$refs.videoPlayer;

    // Listen to the 'timeupdate' event of the video player
    videoPlayer.addEventListener('timeupdate', this.updateCurrentTime);
    this.loadAudioWaveform();
  },
  props: {
    videoUrl: {
      type: String,
      default: null,
    },
  },
  computed: {
    editorId() {
      return this.videoUrl ? 'editorUI' : null;
    },
  },
  methods: {
    async loadAudioWaveform() {

      const wavesurfer = WaveSurfer.create({
        container: '#waveform',
        waveColor: '#f81337',
        progressColor: '#000',
        media: document.querySelector('video'),
      });

      await wavesurfer.load(audioURL);
    },
    updateCurrentTime(event) {

    function padTime(value) {
      return value.toString().padStart(2, '0');
    };
        
      const timeInSeconds = event.target.currentTime; //this.videoPlayer.currentTime;
      const hours = Math.floor(timeInSeconds / 3600);
      const minutes = Math.floor((timeInSeconds % 3600) / 60);
      const seconds = Math.floor(timeInSeconds % 60);
      const frames = Math.floor((timeInSeconds % 1) * 30);

      const formattedTime = padTime(hours) + ':' + padTime(minutes) + ':' + padTime(seconds) + ':' + padTime(frames);
      
      // Retrieve the current time from the video player and update the data property
      this.currentTime = formattedTime;
    },
    seekToTime() {
      if (!this.userInputTime) return;

      // Split the input value into hours, minutes, seconds, and frames
      const [hours, minutes, seconds, frames] = this.userInputTime.split(':').map(Number);

      // Calculate the total time in seconds based on user input
      const totalTimeInSeconds = hours * 3600 + minutes * 60 + seconds + frames / this.getFramesPerSecond();

      // Update the video player's current time
      this.videoPlayer.currentTime = totalTimeInSeconds;
    },
  },
  data() {
    return {
      currentTime: '00:00:00:00',
    };
  },
};

</script>

<style>
    /* Common styles for both light and dark modes */
    body {
      margin: 0;
      padding: 0;
    }
    .box {
      padding: 20px;
      overflow: hidden;
    }

    /* Light mode styles */
    #editorUI.light-mode {
      background-color: #f0f0f0;
    }
    .box.light-mode {
      background-color: #fff;
      color: #000;
      border: 1px solid #ccc;
    }
    .timeline-object.light-mode,
    .timeline-video.light-mode,
    .timeline-audio.light-mode {
      background-color: #f9f9f9;
    }

    /* Dark mode styles */
    #editorUI.dark-mode {
      background-color: #222;
      color: #fff;
    }
    .box.dark-mode {
      background-color: #333;
      border: 1px solid #555;
    }
    .timeline-object.dark-mode,
    .timeline-video.dark-mode,
    .timeline-audio.dark-mode {
      background-color: #444;
    }
    #video-player.dark-mode {
      background-color: #111;
    }

    /* Add additional styling for your video player */
    #video-player {
      /*flex: 1;*/
      height: 400px;
      margin-left: auto;
    }

    .timeline-container{
        display:flex;
        flex-direction:row;
    }

    /* Add your CSS styling for buttons and other elements here */
    .feature-buttons {
      margin-bottom: 10px;
	  display:grid;
    }
    .feature-buttons button {
      margin-right: 10px;
      background-color: #ccc;
      color: #000;
      border: none;
      padding: 8px 16px;
      border-radius: 4px;
      cursor: pointer;
    }

    /* Add your CSS for draggable blur objects here */
    .timeline {
      position: relative;
      height: 50px;
      margin-bottom: 10px;
      cursor: move;
    }
    .timeline:before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      height: 100%;
      border-top: 1px solid #ccc;
    }
    .blur-object {
      position: absolute;
      height: 100%;
      background-color: #007bff;
      color: #fff;
      text-align: center;
      line-height: 50px;
      cursor: move;
    }

    /* Sub-timeline for blurred objects */
    .sub-timeline {
      position: absolute;
      top: 100%;
      left: 0;
      width: 100%;
      height: 50px;
    }
    .sub-timeline:before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      height: 100%;
      border-top: 1px solid #ccc;
    }
    .sub-timeline .blur-object {
      position: absolute;
      height: 100%;
      width: 20px;
      background-color: #007bff;
      color: #fff;
      text-align: center;
      line-height: 50px;
      cursor: move;
    }

    /* Add your CSS for blur object with thumbnails here */
    .timeline-blur {
      position: relative;
      height: 100px;
      margin-bottom: 10px;
	  background-color:rgb(21, 75, 110);
    }

    /* Add your CSS for video object with thumbnails here */
    .timeline-video {
      position: relative;
      height: 100px;
      margin-bottom: 10px;
	  background-color:black;
      width: 100%;
    }

    /* Add your CSS for audio waveform canvas or placeholder image here */
    .timeline-audio {
      position: relative;
      height: 80px;
      margin-bottom: 10px;
      background-color:green;
      width: 100%;
    }

    /* Add CSS for timeline header */
    .timeline-header {
      font-weight: bold;
      margin-bottom: 5px;
      margin-right: 15px;
    }

    /* Add CSS for the content wrapper */
    .timeline-content-wrapper {
      /*display: flex;
      flex-wrap: wrap;*/
    }

    /* Add CSS for timeline content items */
    .timeline-content-item {
      flex: 1 0 100%;
      padding: 5px;
    }
    .timeline-content-item:nth-child(3n + 1) {
      flex: 1 0 33.33%;
    }
	#editor-box {display: flex;}
  </style>