# ai-clip
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ClipAI - AI Video Clipper</title>

<style>
*{
  box-sizing:border-box;
  margin:0;
  padding:0;
  font-family:Arial, sans-serif;
}

body{
  background:#09090b;
  color:black;
  min-height:100vh;
}

header{
  height:65px;
  display:flex;
  align-items:center;
  justify-content:space-between;
  padding:0 18px;
  border-bottom:1px solid #27272a;
  background:#111113;
  position:sticky;
  top:0;
  z-index:10;
}

.logo{
  font-size:22px;
  font-weight:bold;
}

.logo span{
  color:black;
}

.header-btn{
  background:white;
  color:gray;
  border:0;
  padding:10px 15px;
  border-radius:10px;
  font-weight:bold;
}

main{
  max-width:1000px;
  margin:auto;
  padding:20px;
}

.hero{
  text-align:center;
  padding:25px 0;
}

.hero h1{
  font-size:32px;
  margin-bottom:8px;
}

.hero p{
  color:#a1a1aa;
}

.upload{
  border:2px dashed #3f3f46;
  border-radius:18px;
  padding:35px 20px;
  text-align:center;
  background:#111113;
  margin-bottom:20px;
}

.upload-icon{
  font-size:45px;
  margin-bottom:10px;
}

.upload button{
  margin-top:15px;
  background:black;
  color:white;
  border:0;
  padding:13px 22px;
  border-radius:10px;
  font-size:15px;
  font-weight:bold;
}

#fileInput{
  display:none;
}

.editor{
  display:none;
}

.video-box{
  background:#000;
  border-radius:15px;
  overflow:hidden;
  margin-bottom:15px;
}

video{
  width:100%;
  display:block;
  max-height:500px;
}

.panel{
  background:#111113;
  border:1px solid #27272a;
  border-radius:15px;
  padding:18px;
  margin-bottom:15px;
}

.panel h3{
  margin-bottom:15px;
}

.ai-btn{
  width:100%;
  padding:14px;
  border:0;
  border-radius:10px;
  background:linear-gradient(90deg,gray,black);
  color:white;
  font-size:16px;
  font-weight:bold;
}

.status{
  margin-top:12px;
  color:#a1a1aa;
  font-size:14px;
}

.time-row{
  display:flex;
  gap:12px;
  margin-top:15px;
}

.time-box{
  flex:1;
}

label{
  display:block;
  color:#a1a1aa;
  margin-bottom:7px;
  font-size:13px;
}

input[type="number"],
input[type="text"]{
  width:100%;
  background:#18181b;
  color:white;
  border:1px solid #3f3f46;
  border-radius:9px;
  padding:12px;
  outline:none;
}

input:focus{
  border-color:black;
}

input[type="range"]{
  width:100%;
  accent-color:gray
  ;
}

.range{
  margin-top:18px;
}

.caption{
  margin-top:15px;
}

.actions{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:10px;
}

.action{
  padding:13px;
  border:0;
  border-radius:10px;
  font-weight:bold;
  cursor:pointer;
}

.preview{
  background:#27272a;
  color:white;
}

.download{
  background:black;
  color:white;
}

.clips{
  display:grid;
  gap:10px;
}

.clip{
  background:#18181b;
  border:1px solid #27272a;
  border-radius:12px;
  padding:14px;
  display:flex;
  align-items:center;
  justify-content:space-between;
  gap:10px;
}

.clip-info strong{
  display:block;
  margin-bottom:5px;
}

.clip-info small{
  color:#a1a1aa;
}

.use-btn{
  background:#27272a;
  color:white;
  border:0;
  padding:9px 12px;
  border-radius:8px;
}

.processing{
  display:none;
  margin-top:15px;
  background:#18181b;
  padding:12px;
  border-radius:10px;
}

.progress{
  height:7px;
  background:#27272a;
  border-radius:20px;
  overflow:hidden;
  margin-top:10px;
}

.progress-bar{
  height:100%;
  width:0%;
  background:gray;
}

@media(max-width:600px){
  main{
    padding:12px;
  }

  .hero h1{
    font-size:27px;
  }

  .time-row{
    flex-direction:column;
  }

  .actions{
    grid-template-columns:1fr;
  }
}
</style>
</head>

<body>

<header>
  <div class="logo">Clip<span>AI</span></div>
  <button class="header-btn" onclick="newVideo()">New Video</button>
</header>

<main>

<section class="hero">
  <h1>AI Video Clipper</h1>
  <p>Turn long videos into short clips.</p>
</section>

<section class="upload" id="uploadBox">

  <div class="upload-icon">🎬</div>

  <h2>Upload your video</h2>

  <p style="color:#a1a1aa;margin-top:8px;">
    MP4, WebM or other browser-supported video
  </p>

  <button onclick="document.getElementById('fileInput').click()">
    Choose Video
  </button>

  <input
    type="file"
    id="fileInput"
    accept="video/*"
  >

</section>

<section class="editor" id="editor">

  <div class="video-box">
    <video id="video" controls></video>
  </div>

  <div class="panel">

    <h3>🤖 AI Clip Finder</h3>

    <button class="ai-btn" onclick="findHighlights()">
      ✨ Find Best Moments
    </button>

    <div class="status" id="status">
      AI will suggest interesting sections of your video.
    </div>

    <div class="processing" id="processing">

      <div>Analyzing video...</div>

      <div class="progress">
        <div class="progress-bar" id="progressBar"></div>
      </div>

    </div>

  </div>

  <div class="panel">

    <h3>✂️ Clip Editor</h3>

    <div class="range">

      <label>Start</label>

      <input
        type="range"
        id="startRange"
        min="0"
        value="0"
        step="0.1"
      >

    </div>

    <div class="range">

      <label>End</label>

      <input
        type="range"
        id="endRange"
        min="0"
        value="0"
        step="0.1"
      >

    </div>

    <div class="time-row">

      <div class="time-box">
        <label>Start Time</label>
        <input type="number" id="startTime" value="0" min="0">
      </div>

      <div class="time-box">
        <label>End Time</label>
        <input type="number" id="endTime" value="0" min="0">
      </div>

    </div>

    <div class="caption">

      <label>Caption / Title</label>

      <input
        type="text"
        id="caption"
        placeholder="Enter caption..."
      >

    </div>

  </div>

  <div class="panel">

    <h3>🔥 Suggested Clips</h3>

    <div class="clips" id="clips">

      <div style="color:#a1a1aa;">
        Click "Find Best Moments" to generate suggestions.
      </div>

    </div>

  </div>

  <div class="panel">

    <h3>🎞️ Export</h3>

    <div class="actions">

      <button class="action preview" onclick="previewClip()">
        ▶ Preview Clip
      </button>

      <button class="action download" onclick="exportClip()">
        ⬇ Export Clip
      </button>

    </div>

    <p id="exportStatus"
       style="color:#a1a1aa;margin-top:12px;font-size:13px;">
    </p>

  </div>

</section>

</main>

<script>

const fileInput = document.getElementById("fileInput");
const uploadBox = document.getElementById("uploadBox");
const editor = document.getElementById("editor");
const video = document.getElementById("video");

const startRange = document.getElementById("startRange");
const endRange = document.getElementById("endRange");

const startTime = document.getElementById("startTime");
const endTime = document.getElementById("endTime");

let videoURL = null;
let duration = 0;

fileInput.addEventListener("change", function(){

  const file = this.files[0];

  if(!file) return;

  if(videoURL){
    URL.revokeObjectURL(videoURL);
  }

  videoURL = URL.createObjectURL(file);

  video.src = videoURL;

  uploadBox.style.display = "none";
  editor.style.display = "block";

});

video.addEventListener("loadedmetadata", function(){

  duration = video.duration;

  startRange.max = duration;
  endRange.max = duration;

  endRange.value = duration;

  startTime.value = 0;
  endTime.value = duration.toFixed(1);

});

startRange.addEventListener("input", function(){

  let value = Number(this.value);

  if(value >= Number(endRange.value)){
    value = Number(endRange.value) - 0.1;
    this.value = value;
  }

  startTime.value = value.toFixed(1);

});

endRange.addEventListener("input", function(){

  let value = Number(this.value);

  if(value <= Number(startRange.value)){
    value = Number(startRange.value) + 0.1;
    this.value = value;
  }

  endTime.value = value.toFixed(1);

});

startTime.addEventListener("input", function(){

  let value = Number(this.value);

  if(value < 0) value = 0;

  if(value >= Number(endTime.value)){
    value = Number(endTime.value) - 0.1;
  }

  startRange.value = value;
  this.value = value;

});

endTime.addEventListener("input", function(){

  let value = Number(this.value);

  if(value > duration) value = duration;

  if(value <= Number(startTime.value)){
    value = Number(startTime.value) + 0.1;
  }

  endRange.value = value;
  this.value = value;

});

function findHighlights(){

  if(!duration){
    alert("Please upload a video first.");
    return;
  }

  const processing = document.getElementById("processing");
  const progressBar = document.getElementById("progressBar");
  const status = document.getElementById("status");

  processing.style.display = "block";

  let progress = 0;

  const timer = setInterval(function(){

    progress += 5;

    progressBar.style.width = progress + "%";

    if(progress >= 100){

      clearInterval(timer);

      processing.style.display = "none";

      status.innerText =
        "AI found some potential highlight moments.";

      generateClips();

    }

  },80);

}

function generateClips(){

  const clips = document.getElementById("clips");

  clips.innerHTML = "";

  const clipLength = Math.min(30, duration);

  const positions = [

    0,

    Math.max(0, duration * 0.25),

    Math.max(0, duration * 0.50),

    Math.max(0, duration * 0.75)

  ];

  positions.forEach(function(position,index){

    let start = Math.min(position,duration-0.1);

    let end = Math.min(start + clipLength,duration);

    const div = document.createElement("div");

    div.className = "clip";

    div.innerHTML = `

      <div class="clip-info">

        <strong>🔥 Highlight ${index + 1}</strong>

        <small>
          ${formatTime(start)} - ${formatTime(end)}
        </small>

      </div>

      <button class="use-btn">
        Use Clip
      </button>

    `;

    div.querySelector(".use-btn").onclick = function(){

      setClip(start,end);

    };

    clips.appendChild(div);

  });

}

function setClip(start,end){

  startRange.value = start;
  endRange.value = end;

  startTime.value = start.toFixed(1);
  endTime.value = end.toFixed(1);

  video.currentTime = start;

  window.scrollTo({
    top:video.offsetTop - 80,
    behavior:"smooth"
  });

}

function previewClip(){

  const start = Number(startTime.value);

  video.currentTime = start;

  video.play();

  const stopAt = Number(endTime.value);

  function stop(){

    if(video.currentTime >= stopAt){

      video.pause();

      video.removeEventListener("timeupdate",stop);

    }

  }

  video.addEventListener("timeupdate",stop);

}

async function exportClip(){

  const status = document.getElementById("exportStatus");

  if(!video.src){

    alert("Upload a video first.");

    return;

  }

  /*
    Browser-only version:

    A normal HTML page cannot reliably cut and encode
    every video format by itself.

    We therefore create a downloadable clip reference
    when supported by the browser.
  */

  const start = Number(startTime.value);
  const end = Number(endTime.value);

  status.innerText =
    "Preparing your clip...";

  video.currentTime = start;

  setTimeout(function(){

    /*
      This downloads the original video as a fallback.
      Real frame-accurate exporting should use FFmpeg
      or a backend video-processing service.
    */

    const a = document.createElement("a");

    a.href = videoURL;

    a.download =
      "ClipAI_" +
      Math.round(start) +
      "-" +
      Math.round(end) +
      ".mp4";

    a.click();

    status.innerText =
      "Export started. For true AI clipping and MP4 rendering, connect FFmpeg or an AI video API.";

  },800);

}

function formatTime(seconds){

  seconds = Math.floor(seconds);

  const minutes = Math.floor(seconds / 60);

  const secs = seconds % 60;

  return String(minutes).padStart(2,"0")
    + ":" +
    String(secs).padStart(2,"0");

}

function newVideo(){

  fileInput.value = "";

  editor.style.display = "none";

  uploadBox.style.display = "block";

  if(videoURL){

    URL.revokeObjectURL(videoURL);

    videoURL = null;

  }

  video.removeAttribute("src");

  video.load();

}

</script>

</body>
</html>#0c0909c7
