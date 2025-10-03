<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<title>互动小游戏</title>
<style>
body {
  font-family: "Microsoft YaHei", sans-serif;
  background: linear-gradient(160deg, #ffe0f0, #fff0f5);
  margin:0; padding:20px;
  display:flex; flex-direction:column; align-items:center;
}
h1 { color:#ff77b6; text-shadow: 1px 1px 3px #f0a3c0; }

/* 小导航栏 */
.navbar { display:flex; gap:10px; margin-bottom:20px; }
.navbar button {
  padding:10px 20px; border:none; border-radius:12px;
  background:#ffb6d9; color:white; font-weight:bold;
  cursor:pointer; transition: transform 0.2s, background 0.3s;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}
.navbar button.active { background:#ff77b6; transform: scale(1.1); }
.navbar button:hover { transform: scale(1.05); }

/* 板块卡片 */
.section {
  display:none; padding:20px;
  background:rgba(255,255,255,0.9);
  border-radius:16px;
  box-shadow:0 8px 12px rgba(0,0,0,0.1);
  width:340px; max-width:90%;
  transition: all 0.3s;
}

/* 互动按钮 */
button.interact {
  padding:8px 16px; margin:5px; border:none;
  border-radius:12px; cursor:pointer; background:#ff9fc9;
  color:white; font-weight:bold; box-shadow:0 3px 5px rgba(0,0,0,0.1);
  transition: transform 0.2s;
}
button.interact:hover { transform: translateY(-3px); }

/* 心情表情飞动画 */
.moodEmoji { font-size:32px; display:inline-block; position:relative; animation: pop 0.5s ease forwards; }

/* 今日签语翻转 */
#fortuneDisplay {
  padding:10px; border-radius:12px; background:#fff0f5;
  text-align:center; font-weight:bold; color:#ff77b6;
  perspective:600px; transform-style: preserve-3d;
  transition: transform 0.5s;
}

/* 对话框 */
#dialogBox {
  min-height:140px; overflow-y:auto; max-height:220px; padding:10px;
  border-radius:16px; background:#fff0f5; border:1px solid #ffb6d9;
  display:flex; flex-direction:column; gap:6px;
}
.bubble {
  padding:6px 10px; border-radius:12px; display:inline-block;
  max-width:70%; word-break: break-word;
  animation: pop 0.3s ease forwards; position:relative;
}
.user { background:#ffe0f0; align-self:flex-end; border-bottom-right-radius:0; }
.oc { background:#ffd6e8; align-self:flex-start; border-bottom-left-radius:0; }
/* 头像小图标 */
.avatar { width:24px; height:24px; border-radius:50%; position:absolute; top:50%; transform: translateY(-50%); }
.user .avatar { left:-30px; }
.oc .avatar { right:-30px; }

/* 飘动画 */
.fly {
  position:absolute; font-size:18px; animation:flyUp 1s ease forwards;
}

@keyframes pop { 0%{transform:scale(0);opacity:0;}50%{transform:scale(1.3);opacity:1;}100%{transform:scale(1);opacity:1;} }
@keyframes flyUp { 0%{opacity:1; transform:translateY(0) scale(1);}100%{opacity:0; transform:translateY(-50px) scale(1.5);} }
</style>
</head>
<body>

<h1>互动小游戏</h1>

<!-- 小导航栏 -->
<div class="navbar">
  <button class="tabBtn active" onclick="showSection('mood', event)">心情打卡</button>
  <button class="tabBtn" onclick="showSection('fortune', event)">今日签语</button>
  <button class="tabBtn" onclick="showSection('dialog', event)">对话框</button>
</div>

<!-- 心情打卡 -->
<div id="mood" class="section" style="display:block; position:relative;">
  <h2>心情打卡</h2>
  <div>
    <button class="interact" onclick="setMood('开心', event)">😊 开心</button>
    <button class="interact" onclick="setMood('平静', event)">😐 平静</button>
    <button class="interact" onclick="setMood('难过', event)">😢 难过</button>
    <button class="interact" onclick="setMood('生气', event)">😡 生气</button>
    <button class="interact" onclick="setMood('疲惫', event)">😫 疲惫</button>
  </div>
  <p id="moodDisplay">今天还没有打卡哦～</p>
</div>

<!-- 今日签语 -->
<div id="fortune" class="section">
  <h2>今日签语</h2>
  <button class="interact" onclick="drawFortune()">抽签</button>
  <p id="fortuneDisplay">点击上方按钮抽取今日签语</p>
</div>

<!-- 对话框 -->
<div id="dialog" class="section">
  <h2>与人机对话</h2>
  <div id="dialogBox"></div>
  <div>
    <button class="interact" onclick="talk('你好！')">你好！</button>
    <button class="interact" onclick="talk('今天过得怎么样？')">今天过得怎么样？</button>
    <button class="interact" onclick="talk('再见')">再见</button>
  </div>
</div>

<script>
// ========== 小导航切换 ==========
function showSection(id,event){
  const sections=document.querySelectorAll('.section');
  const buttons=document.querySelectorAll('.tabBtn');
  sections.forEach(sec=>sec.style.display='none');
  buttons.forEach(btn=>btn.classList.remove('active'));
  document.getElementById(id).style.display='block';
  event.currentTarget.classList.add('active');
}

// ========== 心情打卡 ==========
function setMood(mood,event){
  const display=document.getElementById('moodDisplay');
  display.innerHTML=`你今天的心情是：<span class="moodEmoji">${getEmoji(mood)}</span>`;
  createFlyEffect(event,getEmoji(mood));
}
function getEmoji(mood){
  switch(mood){
    case '开心': return '😊';
    case '平静': return '😐';
    case '难过': return '😢';
    case '生气': return '😡';
    case '疲惫': return '😫';
  }
}
function createFlyEffect(event,symbol){
  const fly=document.createElement('span');
  fly.className='fly'; fly.innerText=symbol;
  document.getElementById('mood').appendChild(fly);
  fly.style.left=event.offsetX+'px';
  fly.style.top=event.offsetY+'px';
  setTimeout(()=>fly.remove(),1000);
}

// ========== 今日签语 ==========
const fortunes=["今天会遇到小惊喜哦~","保持微笑，事情会更顺利~","小心谨慎，避免意外发生","今天适合学习新技能~","给自己一个放松的时间吧"];
function drawFortune(){
  const f=fortunes[Math.floor(Math.random()*fortunes.length)];
  const display=document.getElementById('fortuneDisplay');
  display.style.transform='rotateY(90deg)';
  setTimeout(()=>{ display.innerText=f; display.style.transform='rotateY(0deg)'; },200);
}

// ========== 对话框 ==========
function talk(text){
  const dialog=document.getElementById('dialogBox');
  const userBubble=document.createElement('div');
  userBubble.className='bubble user';
  userBubble.innerHTML=`<span class="avatar" style="background:#ff77b6;"></span>`+text;
  dialog.appendChild(userBubble);

  const ocBubble=document.createElement('div');
  ocBubble.className='bubble oc';
  ocBubble.innerHTML=`<span class="avatar" style="background:#ffd6e8;"></span>`+generateReply(text);
  dialog.appendChild(ocBubble);

  dialog.scrollTop = dialog.scrollHeight;
}
function generateReply(text){
  if(text.includes('你好')) return "你好呀～今天心情如何？";
  if(text.includes('今天')) return "今天很不错呢，你呢？";
  if(text.includes('再见')) return "再见～下次再聊吧！";
  return "嗯嗯~";
}
</script>
</body>
</html>
