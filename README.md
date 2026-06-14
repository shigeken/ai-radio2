<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>AI Radio</title>

<style>
:root{
  --bg:#0a0a0f;
  --surface:#13131a;
  --border:#2a2a3a;

  --accent:#ff4d6d;
  --accent2:#7b61ff;

  --text:#e8e8f0;
  --muted:#666680;
}

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
}

body{
  background:var(--bg);
  color:var(--text);

  font-family:
    -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    sans-serif;

  min-height:100vh;

  display:flex;
  flex-direction:column;
  align-items:center;

  padding:24px 16px;
}

.header{
  text-align:center;
  margin-bottom:24px;
}

.station-label{
  font-size:12px;
  letter-spacing:4px;
  color:var(--accent);
  margin-bottom:8px;
}

h1{
  font-size:48px;
  font-weight:900;
}

h1 span{
  color:var(--accent);
}

.tagline{
  margin-top:8px;
  color:var(--muted);
  font-size:13px;
}

.card{
  width:100%;
  max-width:520px;

  background:var(--surface);
  border:1px solid var(--border);

  border-radius:20px;

  padding:24px;
  margin-bottom:16px;
}

.section-label{
  font-size:11px;
  letter-spacing:3px;
  color:var(--muted);

  margin-bottom:10px;
}

input[type=password]{
  width:100%;
  padding:14px;

  background:var(--bg);
  color:var(--text);

  border:1px solid var(--border);
  border-radius:12px;

  outline:none;
}

input[type=password]:focus{
  border-color:var(--accent2);
}

.save-row{
  margin-top:10px;
  display:flex;
  gap:8px;
  align-items:center;
}

.save-row label{
  color:var(--muted);
  font-size:12px;
}

.btn-main{
  width:100%;

  border:none;
  border-radius:14px;

  background:var(--accent);
  color:white;

  padding:18px;

  cursor:pointer;
  font-weight:700;
  font-size:18px;
}

.btn-main:hover{
  opacity:.9;
}

.btn-stop{
  width:100%;

  margin-top:12px;

  background:transparent;

  border:1px solid var(--border);

  color:var(--text);

  border-radius:14px;

  padding:14px;

  cursor:pointer;
}

.status{
  margin-top:12px;
  min-height:18px;

  font-size:12px;
  color:var(--muted);
  text-align:center;
}

.status.error{
  color:var(--accent);
}

.now-playing{
  display:none;

  width:100%;
  max-width:520px;

  background:var(--surface);

  border:1px solid var(--border);
  border-radius:20px;

  padding:24px;
}

.now-playing.visible{
  display:block;
}

.mode-badge{
  display:inline-block;

  padding:5px 12px;

  border-radius:999px;

  font-size:12px;
  font-weight:700;

  margin-bottom:12px;
}

.mode-loading{
  background:rgba(123,97,255,.15);
  color:var(--accent2);
}

.mode-talk{
  background:rgba(255,77,109,.15);
  color:var(--accent);
}

.mode-cm{
  background:rgba(255,215,0,.15);
  color:#ffd700;
}

.current-theme{
  margin-bottom:14px;
  font-weight:700;
}

.waveform{
  display:flex;
  gap:3px;
  align-items:center;

  height:32px;

  margin-bottom:14px;
}

.bar{
  width:3px;

  background:var(--accent);

  border-radius:3px;

  animation:wave 1s infinite ease-in-out;
}

.bar:nth-child(1){animation-delay:0s;}
.bar:nth-child(2){animation-delay:.1s;}
.bar:nth-child(3){animation-delay:.2s;}
.bar:nth-child(4){animation-delay:.3s;}
.bar:nth-child(5){animation-delay:.4s;}
.bar:nth-child(6){animation-delay:.3s;}
.bar:nth-child(7){animation-delay:.2s;}
.bar:nth-child(8){animation-delay:.1s;}
.bar:nth-child(9){animation-delay:0s;}

@keyframes wave{
  0%,100%{
    height:5px;
  }

  50%{
    height:26px;
  }
}

.chat-log{
  display:flex;
  flex-direction:column;
  gap:12px;

  max-height:380px;
  overflow-y:auto;
}

.chat-bubble{
  display:flex;
  gap:10px;
}

.chat-bubble.right{
  flex-direction:row-reverse;
}

.avatar{
  width:32px;
  flex-shrink:0;
  text-align:center;
  font-size:24px;
}

.message{
  max-width:80%;
}

.name{
  font-size:11px;
  margin-bottom:4px;
}

.chat-bubble.left .name{
  color:var(--accent);
}

.chat-bubble.right .name{
  color:var(--accent2);
  text-align:right;
}

.text{
  border:1px solid var(--border);

  border-radius:12px;

  background:var(--bg);

  padding:10px 12px;

  line-height:1.7;
}

.text.speaking{
  background:rgba(255,255,255,.05);
}

.cm-box{
  display:none;

  background:rgba(255,215,0,.08);

  border:1px solid rgba(255,215,0,.25);

  border-radius:12px;

  padding:14px;

  color:#ffe066;
}

.cm-box.visible{
  display:block;
}

.bgm{
  margin-top:12px;

  display:flex;
  align-items:center;
  gap:8px;

  color:var(--muted);
  font-size:12px;
}

.dot{
  width:8px;
  height:8px;

  border-radius:50%;

  background:var(--accent2);
}
</style>
</head>

<body>

<div class="header">
  <div class="station-label">📻 ON AIR</div>
  <h1>AI<span>Radio</span></h1>
  <div class="tagline">
    Claudeが作る終わらないラジオ番組
  </div>
</div>

<div class="card" id="setupCard">

  <div class="section-label">
    API KEY
  </div>

  <input
    type="password"
    id="apiKey"
    placeholder="sk-ant-api..."
  >

  <div class="save-row">
    <input
      type="checkbox"
      id="saveKey"
      checked
    >

    <label for="saveKey">
      この端末に保存
    </label>
  </div>

  <div style="height:16px"></div>

  <button
    id="startBtn"
    class="btn-main"
    onclick="startRadio()"
  >
    ▶ 放送開始
  </button>

  <div
    id="status"
    class="status"
  ></div>

</div>

<div
  id="nowPlaying"
  class="now-playing"
>

  <div class="waveform">

    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>
    <div class="bar"></div>

  </div>

  <div
    id="modeBadge"
    class="mode-badge mode-loading"
  >
    準備中
  </div>

  <div
    id="currentTheme"
    class="current-theme"
  ></div>

  <div
    id="chatLog"
    class="chat-log"
  ></div>

  <div
    id="cmBox"
    class="cm-box"
  ></div>

  <div class="bgm">
    <div class="dot"></div>
    <span>BGM再生中</span>
  </div>

  <button
    class="btn-stop"
    onclick="stopRadio()"
  >
    ■ 放送終了
  </button>

</div>

<script>

// =========================
// 状態管理
// =========================

let isRunning = false;

let synth = window.speechSynthesis;
let voices = [];

let audioCtx = null;

let bgmTimer = null;
let bgmEnabled = false;

const DJS = {

  a:{
    name:"ハル",
    emoji:"😄",
    pitch:1.2,
    rate:1.0,
    desc:"明るく話を広げる"
  },

  b:{
    name:"ソラ",
    emoji:"🤓",
    pitch:0.85,
    rate:0.95,
    desc:"知識豊富なツッコミ役"
  },

  c:{
    name:"ナナ",
    emoji:"😎",
    pitch:1.3,
    rate:1.05,
    desc:"テンション高めのギャル"
  },

  d:{
    name:"ジョージ",
    emoji:"🧓",
    pitch:0.75,
    rate:0.9,
    desc:"昭和おじさん"
  }

};

// =========================
// 音声一覧
// =========================

function loadVoices(){

  voices = synth
    .getVoices()
    .filter(v=>v.lang.startsWith("ja"));

}

loadVoices();

if(speechSynthesis.onvoiceschanged !== undefined){

  speechSynthesis.onvoiceschanged = loadVoices;

}

// =========================
// API KEY
// =========================

window.addEventListener("load",()=>{

  const saved =
    localStorage.getItem("ai_radio_key");

  if(saved){

    document.getElementById("apiKey").value =
      saved;

  }

});

function getApiKey(){

  const key =
    document.getElementById("apiKey")
      .value
      .trim();

  return key;

}

function saveApiKey(){

  const key = getApiKey();

  if(!key) return;

  if(
    document.getElementById("saveKey")
      .checked
  ){

    localStorage.setItem(
      "ai_radio_key",
      key
    );

  }

}

// =========================
// UI
// =========================

function setStatus(text,error=false){

  const el =
    document.getElementById("status");

  el.textContent = text;

  if(error){
    el.className = "status error";
  }else{
    el.className = "status";
  }

}

function setMode(mode,theme="",djKeys=[]){

  const badge =
    document.getElementById("modeBadge");

  const themeEl =
    document.getElementById("currentTheme");

  if(mode==="loading"){

    badge.className =
      "mode-badge mode-loading";

    badge.textContent =
      "⏳ 準備中";

    themeEl.textContent =
      "AIが台本を生成しています";

  }

  else if(mode==="talk"){

    badge.className =
      "mode-badge mode-talk";

    const names =
      djKeys
      .map(k =>
        DJS[k].emoji +
        DJS[k].name
      )
      .join(" × ");

    badge.textContent =
      "🎙 " + names;

    themeEl.textContent =
      "📌 " + theme;

    document
      .getElementById("cmBox")
      .classList
      .remove("visible");

  }

  else if(mode==="cm"){

    badge.className =
      "mode-badge mode-cm";

    badge.textContent =
      "📺 CM";

    themeEl.textContent =
      "スポンサーCM";

    document
      .getElementById("cmBox")
      .classList
      .add("visible");

  }

}

// =========================
// Claude API
// =========================

async function callClaude(
  prompt,
  maxTokens=1000
){

  const apiKey = getApiKey();

  if(!apiKey){

    throw new Error(
      "APIキーを入力してください"
    );

  }

  const response =
    await fetch(
      "https://api.anthropic.com/v1/messages",
      {
        method:"POST",

        headers:{
          "content-type":"application/json",
          "x-api-key":apiKey,
          "anthropic-version":"2023-06-01",

          "anthropic-dangerous-direct-browser-access":"true"
        },

        body:JSON.stringify({

          model:
            "claude-3-5-sonnet-latest",

          max_tokens:maxTokens,

          messages:[
            {
              role:"user",
              content:prompt
            }
          ]

        })
      }
    );

  const json =
    await response.json();

  if(!response.ok){

    throw new Error(
      json?.error?.message ||
      JSON.stringify(json)
    );

  }

  return json.content[0].text.trim();

}

// =========================
// DJ抽選
// =========================

function pickDJs(){

  const keys =
    Object.keys(DJS);

  const shuffled =
    [...keys]
      .sort(
        ()=>Math.random()-0.5
      );

  return [
    shuffled[0],
    shuffled[1]
  ];

}

// =========================
// テーマ生成
// =========================

async function fetchTheme(){

  return await callClaude(

`面白いラジオ番組のテーマを1つだけ考えてください。

テーマだけを出力してください。
説明不要。
20文字以内。`,

100

  );

}

// =========================
// CM生成
// =========================

async function fetchCM(){

  return await callClaude(

`15秒程度の架空ラジオCMを作ってください。

CM本文のみ出力してください。`,

200

  );

}

// =========================
// 台本生成
// =========================

async function fetchScript(
  theme,
  djKeys
){

  const djA =
    DJS[djKeys[0]];

  const djB =
    DJS[djKeys[1]];

  const raw =
    await callClaude(

`テーマ:
${theme}

DJ A:
${djA.name}
${djA.desc}

DJ B:
${djB.name}
${djB.desc}

ルール:

A:○○
B:○○

形式で25〜35発言。

余計な説明は禁止。`,

2500

);

  const lines = [];

  raw.split("\n")
    .forEach(line=>{

      const t =
        line.trim();

      if(
        t.startsWith("A:")
      ){

        lines.push({

          dj:djKeys[0],

          text:t.replace(
            /^A:\s*/,
            ""
          )

        });

      }

      if(
        t.startsWith("B:")
      ){

        lines.push({

          dj:djKeys[1],

          text:t.replace(
            /^B:\s*/,
            ""
          )

        });

      }

    });

  if(
    lines.length < 5
  ){

    throw new Error(
      "台本解析失敗"
    );

  }

  return {
    theme,
    djKeys,
    lines
  };

}

// =========================
// BGM
// =========================

function startBGM(){

  if(
    !audioCtx ||
    bgmEnabled
  ) return;

  bgmEnabled = true;

  function play(){

    if(
      !bgmEnabled
    ) return;

    const osc =
      audioCtx
      .createOscillator();

    const gain =
      audioCtx
      .createGain();

    osc.connect(gain);

    gain.connect(
      audioCtx.destination
    );

    osc.frequency.value =
      220 +
      Math.random()*300;

    gain.gain.value =
      0.015;

    osc.start();

    osc.stop(
      audioCtx.currentTime
      + 0.3
    );

    bgmTimer =
      setTimeout(
        play,
        400
      );

  }

  play();

}

function stopBGM(){

  bgmEnabled = false;

  if(bgmTimer){

    clearTimeout(
      bgmTimer
    );

    bgmTimer = null;

  }

}

// =========================
// 読み上げ
// =========================

function speak(
  text,
  djKey
){

  return new Promise(resolve=>{

    if(!isRunning){
      resolve();
      return;
    }

    const dj =
      DJS[djKey] ||
      {
        pitch:1,
        rate:1
      };

    const u =
      new SpeechSynthesisUtterance(
        text
      );

    u.lang = "ja-JP";

    u.pitch =
      dj.pitch;

    u.rate =
      dj.rate;

    if(
      voices.length > 0
    ){

      u.voice =
        voices[
          Math.min(
            voices.length - 1,
            Object.keys(DJS)
              .indexOf(djKey)
          )
        ];

    }

    u.onend = resolve;
    u.onerror = resolve;

    synth.speak(u);

  });

}

function wait(ms){

  return new Promise(
    r=>setTimeout(r,ms)
  );

}

// =========================
// UI描画
// =========================

function renderChat(
  lines
){

  const log =
    document.getElementById(
      "chatLog"
    );

  log.innerHTML = "";

  if(
    !lines ||
    lines.length===0
  ) return;

  const leftDj =
    lines[0].dj;

  lines.forEach(
    (line,index)=>{

      const dj =
        DJS[line.dj];

      const side =
        line.dj===leftDj
        ? "left"
        : "right";

      const div =
        document.createElement(
          "div"
        );

      div.className =
        "chat-bubble " +
        side;

      div.innerHTML =

`
<div class="avatar">
${dj.emoji}
</div>

<div class="message">

  <div class="name">
    ${dj.name}
  </div>

  <div
    class="text"
    id="line-${index}"
  >
    ${line.text}
  </div>

</div>
`;

      log.appendChild(div);

    });

}

function highlightLine(
  index
){

  document
    .querySelectorAll(
      ".text"
    )
    .forEach(
      el =>
        el.classList.remove(
          "speaking"
        )
    );

  const target =
    document.getElementById(
      `line-${index}`
    );

  if(target){

    target.classList.add(
      "speaking"
    );

    target.scrollIntoView({
      block:"nearest",
      behavior:"smooth"
    });

  }

}

// =========================
// メインループ
// =========================

async function radioLoop(){

  setMode(
    "loading"
  );

  setStatus(
    "最初の番組を生成中..."
  );

  let current;

  try{

    const firstTheme =
      await fetchTheme();

    const firstDjs =
      pickDJs();

    current =
      await fetchScript(
        firstTheme,
        firstDjs
      );

  }

  catch(err){

    setStatus(
      err.message,
      true
    );

    stopRadio();

    return;

  }

  while(
    isRunning
  ){

    setMode(
      "talk",
      current.theme,
      current.djKeys
    );

    renderChat(
      current.lines
    );

    startBGM();

    const nextProgramPromise =
      (async()=>{

        const theme =
          await fetchTheme();

        const djs =
          pickDJs();

        return await fetchScript(
          theme,
          djs
        );

      })();

    const cmPromise =
      fetchCM();

    // -----------------
    // 本編
    // -----------------

    for(
      let i=0;
      i<current.lines.length;
      i++
    ){

      if(
        !isRunning
      ) return;

      highlightLine(i);

      await speak(
        current.lines[i].text,
        current.lines[i].dj
      );

      await wait(
        150
      );

    }

    // -----------------
    // CM
    // -----------------

    stopBGM();

    setMode(
      "cm"
    );

    let cmText =
      "";

    try{

      cmText =
        await cmPromise;

    }

    catch{

      cmText =
        "この番組はAI Radioの提供でお送りしています。";

    }

    document
      .getElementById(
        "cmBox"
      )
      .textContent =
        cmText;

    await speak(
      "ここでCMです",
      current.djKeys[0]
    );

    await wait(
      200
    );

    await speak(
      cmText,
      null
    );

    await wait(
      500
    );

    await speak(
      "番組に戻ります",
      current.djKeys[1]
    );

    // -----------------
    // 次番組
    // -----------------

    setMode(
      "loading"
    );

    setStatus(
      "次の番組を準備中..."
    );

    try{

      current =
        await nextProgramPromise;

    }

    catch(err){

      console.error(
        err
      );

      await wait(
        2000
      );

      continue;

    }

  }

}

// =========================
// 開始
// =========================

async function startRadio(){

  if(
    isRunning
  ) return;

  try{

    saveApiKey();

    if(
      !getApiKey()
    ){

      setStatus(
        "APIキーを入力してください",
        true
      );

      return;

    }

    audioCtx =
      new (
        window.AudioContext ||
        window.webkitAudioContext
      )();

    if(
      audioCtx.state ===
      "suspended"
    ){

      await audioCtx.resume();

    }

    isRunning = true;

    document
      .getElementById(
        "setupCard"
      )
      .style.display =
      "none";

    document
      .getElementById(
        "nowPlaying"
      )
      .classList.add(
        "visible"
      );

    setStatus("");

    radioLoop();

  }

  catch(err){

    console.error(
      err
    );

    setStatus(
      err.message,
      true
    );

  }

}

// =========================
// 停止
// =========================

function stopRadio(){

  isRunning = false;

  stopBGM();

  try{

    synth.cancel();

  }catch{}

  document
    .getElementById(
      "setupCard"
    )
    .style.display =
    "block";

  document
    .getElementById(
      "nowPlaying"
    )
    .classList.remove(
      "visible"
    );

  setStatus(
    "停止しました"
  );

}

</script>

</body>
</html>
