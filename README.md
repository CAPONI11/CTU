
<html lang="ar">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CAPONI</title>

  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

  <style>
    *{margin:0;padding:0;box-sizing:border-box;font-family:"Segoe UI",Arial,sans-serif;}
    body{height:100vh;overflow:hidden;background:#000;color:#fff;}

    /* ===== Video Background ===== */
    #bgVideo{
      position:fixed;top:50%;left:50%;
      min-width:100%;min-height:100%;
      transform:translate(-50%,-50%);
      object-fit:cover;z-index:-3;
    }
    .bg-dark{position:fixed;inset:0;background:rgba(0,0,0,0.45);z-index:-2;}

    /* ===== Entry Blur Screen ===== */
    #overlay{
      position:fixed;inset:0;
      background:rgba(255,255,255,0.90);
      backdrop-filter:blur(12px);
      -webkit-backdrop-filter:blur(12px);
      display:flex;align-items:center;justify-content:center;
      cursor:pointer;z-index:10;
      transition:opacity 1.2s ease;
      user-select:none;
    }
    #overlay span{font-size:36px;font-weight:900;color:#000;}

    /* ===== Main ===== */
    .main{
      display:none;
      height:100vh;
      justify-content:center;
      align-items:center;
      padding:16px;
    }

    .wrap{
      width:420px;max-width:95vw;
      text-align:center;
      display:flex;flex-direction:column;align-items:center;
      gap:10px;
    }

    /* ===== Fade + Blur reveal ===== */
    .reveal{
      opacity:0;
      transform:translateY(12px);
      filter:blur(10px);
      transition:opacity 650ms ease, transform 650ms ease, filter 650ms ease;
      will-change: opacity, transform, filter;
    }
    .reveal.show{
      opacity:1;
      transform:translateY(0);
      filter:blur(0);
    }

    /* ===== Avatar + Discord Decoration overlay ===== */
    .pfp{
      width:110px;height:110px;
      position:relative;
      display:grid;
      place-items:center;
    }
    .pfpBase{
      width:120px;height:120px;border-radius:50%;
      border:2px solid rgba(255,255,255,0.85);
      object-fit:cover;
      background:rgba(0,0,0,0.2);
      z-index:1;
    }
    .pfpDecor{
      position:absolute;
      left:50%;top:50%;
      width:140%;
      height:140%;
      transform:translate(-50%,-50%);
      pointer-events:none;
      z-index:2;
      display:none;
      filter:drop-shadow(0 0 10px rgba(0,0,0,0.35));
    }

    h1{font-size:38px;font-weight:900;letter-spacing:0.2px;}
    .quote{font-size:15px;opacity:0.95;margin-top:-2px;}
    .location{
      margin-top:4px;font-size:14px;opacity:0.8;
      display:flex;align-items:center;justify-content:center;gap:8px;
    }

    /* ===== Discord Card ===== */
    .discord{
      width:100%;
      margin-top:10px;
      background:rgba(255,255,255,0.09);
      border:1px solid rgba(255,255,255,0.10);
      backdrop-filter:blur(22px);
      -webkit-backdrop-filter:blur(22px);
      border-radius:18px;
      padding:14px;
      display:flex;align-items:center;gap:12px;
      transition:box-shadow .25s ease, border-color .25s ease;
    }
    .discord.glow-online{box-shadow:0 0 22px rgba(59,165,93,0.65);border-color:rgba(59,165,93,0.35);}
    .discord.glow-idle{box-shadow:0 0 22px rgba(250,166,26,0.65);border-color:rgba(250,166,26,0.35);}
    .discord.glow-dnd{box-shadow:0 0 22px rgba(237,66,69,0.65);border-color:rgba(237,66,69,0.35);}
    .discord.glow-off{box-shadow:0 0 18px rgba(116,127,141,0.55);border-color:rgba(116,127,141,0.25);}

    /* mini avatar + mini decoration */
    .miniPfp{position:relative;width:54px;height:54px;flex:0 0 auto;}
    #discordAvatar{
      width:54px;height:54px;border-radius:50%;
      object-fit:cover;
      border:2px solid rgba(255,255,255,0.7);
      background:rgba(0,0,0,0.2);
      position:absolute;inset:0;z-index:1;
    }
    #discordDecorMini{
      position:absolute;left:50%;top:50%;
      width:140%;
      height:140%;
      transform:translate(-50%,-50%);
      pointer-events:none;
      z-index:2;
      display:none;
      filter:drop-shadow(0 0 8px rgba(0,0,0,0.30));
    }

    #spotifyCover{
      width:54px;height:54px;border-radius:12px;
      object-fit:cover;
      border:2px solid rgba(255,255,255,0.45);
      box-shadow:0 0 18px rgba(255,255,255,0.18);
      display:none;
      flex:0 0 auto;
    }

    .discord-info{flex:1;min-width:0;text-align:left;}
    #discordName{font-size:15px;font-weight:800;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
    #discordStatus{margin-top:2px;font-size:13px;opacity:0.9;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
    #discordActivity{margin-top:4px;font-size:12px;opacity:0.8;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}

    /* ===== Discord-like status icons ===== */
    .statusIcon{
      display:inline-block;
      width:14px;height:14px;
      margin-right:8px;
      vertical-align:middle;
      position:relative;
    }
    .status-online{border-radius:50%;background:#3BA55D;box-shadow:0 0 10px rgba(59,165,93,0.35);}
    .status-idle{border-radius:50%;background:#FAA61A;box-shadow:0 0 10px rgba(250,166,26,0.35);}
    .status-idle::after{
      content:"";
      position:absolute;
      width:10px;height:10px;
      top:2px;left:5px;
      border-radius:50%;
      background:rgba(255,255,255,0.10);
    }
    .status-dnd{border-radius:50%;background:#ED4245;box-shadow:0 0 10px rgba(237,66,69,0.35);}
    .status-dnd::after{
      content:"";
      position:absolute;
      width:10px;height:3px;
      top:50%;left:50%;
      transform:translate(-50%,-50%);
      border-radius:2px;
      background:#fff;
    }
    .status-off{border-radius:50%;background:#747F8D;box-shadow:0 0 10px rgba(116,127,141,0.25);}

    /* ===== Socials ===== */
    .socials{margin-top:14px;display:flex;justify-content:center;gap:16px;}
    .socials a{
      width:54px;height:54px;border-radius:16px;
      display:flex;align-items:center;justify-content:center;
      color:#fff;text-decoration:none;
      background:rgba(0,0,0,0.30);
      border:1px solid rgba(255,255,255,0.10);
      backdrop-filter:blur(14px);
      -webkit-backdrop-filter:blur(14px);
      transition:transform .25s ease, opacity .25s ease;
      font-size:26px;
    }
    .socials a:hover{transform:scale(1.10);opacity:0.9;}

    /* ===== Views ===== */
    .views{
      margin-top:8px;
      font-size:14px;
      opacity:0.9;
      display:flex;align-items:center;gap:8px;
      justify-content:flex-start;
      width:100%;
    }

    /* ===== Mobile performance ===== */
    @media(max-width:480px){
      #overlay span{font-size:26px;}
      h1{font-size:30px;}
      .pfp{width:118px;height:118px;}
      .pfpBase{width:110px;height:110px;}
      .discord{
        backdrop-filter:blur(14px);
        -webkit-backdrop-filter:blur(14px);
        box-shadow:none !important;
      }
      .bg-dark{background:rgba(0,0,0,0.52);}
    }

    @media (prefers-reduced-motion: reduce){
      .reveal{transition:none;}
    }
  </style>
</head>

<body>
  <video autoplay muted loop playsinline id="bgVideo">
    <source src="11.mp4" type="video/mp4">
  </video>
  <div class="bg-dark"></div>

  <div id="overlay"><span>اضغط هنا</span></div>

  <audio id="music" loop>
    <source src="55.mp3" type="audio/mpeg">
  </audio>

  <div class="main" id="main">
    <div class="wrap">

      <!-- Avatar + Decoration -->
      <div class="pfp reveal">
        <img id="baseAvatar" class="pfpBase" src="https://cdn.discordapp.com/embed/avatars/0.png" alt="avatar">
        <img id="decorAvatar" class="pfpDecor" alt="decoration">
      </div>

      <h1 class="reveal" id="titleName">CAPONI</h1>
      <div class="quote reveal">Whoever revives a dead heart, he is for it</div>
      <div class="location reveal"><i class="fa-solid fa-location-dot"></i> Morocco MARRAKECH</div>

      <div class="discord glow-off reveal" id="discordBox">
        <div class="miniPfp">
          <img id="discordAvatar" src="https://cdn.discordapp.com/embed/avatars/0.png" alt="discord avatar">
          <img id="discordDecorMini" alt="discord decoration mini">
        </div>

        <img id="spotifyCover" alt="spotify cover">

        <div class="discord-info">
          <div id="discordName">CAPONI</div>
          <div id="discordStatus">
            <span class="statusIcon status-off" id="statusIcon" aria-hidden="true"></span>
            <span id="statusText">Loading...</span>
          </div>
          <div id="discordActivity"></div>
        </div>
      </div>

      <div class="socials reveal">
        <a href="https://discord.com/users/1159527398098882703" target="_blank" title="Discord"><i class="fab fa-discord"></i></a>
        <a href="https://www.instagram.com/caponi.11" target="_blank" title="Instagram"><i class="fab fa-instagram"></i></a>
        <a href="https://open.spotify.com" target="_blank" title="Spotify"><i class="fab fa-spotify"></i></a>
        <a href="https://www.tiktok.com/@caponi.666" target="_blank" title="TikTok"><i class="fab fa-tiktok"></i></a>
      </div>

      <div class="views reveal">
        <i class="fa-regular fa-eye"></i>
        <span id="viewsText">...</span>
      </div>

    </div>
  </div>

<script>
  // ===== اسم ثابت =====
  const CUSTOM_NAME = "𝙲𝙰𝙿𝙾𝙽𝙸";

  // ===== Entry + Music (✅ مايبقاش يعاود يريستارت) =====
  const overlay = document.getElementById("overlay");
  const main = document.getElementById("main");
  const music = document.getElementById("music");

  let started = false; // يمنع تكرار التشغيل

  overlay.addEventListener("click", async () => {
    if(started) return;
    started = true;

    overlay.style.opacity = "0";

    setTimeout(async () => {
      overlay.style.display = "none";
      main.style.display = "flex";

      const items = document.querySelectorAll(".reveal");
      items.forEach((el, i) => setTimeout(() => el.classList.add("show"), 110 * i));

      // ✅ تشغيل الموسيقى بأمان (إلا رفض المتصفح، ماشي مشكل)
      try{
        await music.play();
      }catch(e){
        console.warn("Music blocked:", e);
      }
    }, 1200);
  });

  // ===== Views =====
  const viewsEl = document.getElementById("viewsText");
  const NAMESPACE = "caponi-page-views";
  const KEY = "main";

  async function updateViews(){
    try{
      const res = await fetch(`https://api.countapi.xyz/hit/${encodeURIComponent(NAMESPACE)}/${encodeURIComponent(KEY)}`);
      const data = await res.json();
      viewsEl.textContent = data.value;
    }catch(e){
      viewsEl.textContent = "—";
      console.warn("Views error:", e);
    }
  }
  updateViews();

  // ===== Discord Live (Lanyard) + Decoration =====
  const userId = "1159527398098882703";

  const baseAvatar = document.getElementById("baseAvatar");
  const decorAvatar = document.getElementById("decorAvatar");

  const titleName = document.getElementById("titleName");
  const discordBox = document.getElementById("discordBox");

  const discordAvatar = document.getElementById("discordAvatar");
  const discordDecorMini = document.getElementById("discordDecorMini");

  const discordName = document.getElementById("discordName");
  const statusIcon = document.getElementById("statusIcon");
  const statusText = document.getElementById("statusText");
  const activityDiv = document.getElementById("discordActivity");
  const spotifyCover = document.getElementById("spotifyCover");

  let lastAvatarUrl = "";
  let lastDecorAsset = "";
  let lastSpotifyArt = "";
  let lastActivityText = "";
  let lastStatus = "";

  titleName.textContent = CUSTOM_NAME;
  discordName.textContent = CUSTOM_NAME;

  function statusLabel(st){
    if(st === "online") return "Online";
    if(st === "idle") return "Idle";
    if(st === "dnd") return "Do Not Disturb";
    return "Offline";
  }
  function glowClass(st){
    if(st === "online") return "glow-online";
    if(st === "idle") return "glow-idle";
    if(st === "dnd") return "glow-dnd";
    return "glow-off";
  }
  function setStatusIcon(st){
    statusIcon.className = "statusIcon";
    if(st === "online") statusIcon.classList.add("status-online");
    else if(st === "idle") statusIcon.classList.add("status-idle");
    else if(st === "dnd") statusIcon.classList.add("status-dnd");
    else statusIcon.classList.add("status-off");
  }
  function getAvatarUrl(user){
    if(!user.avatar) return "https://cdn.discordapp.com/embed/avatars/0.png";
    return `https://cdn.discordapp.com/avatars/${user.id}/${user.avatar}.png?size=256`;
  }

  // ✅ Decoration URL بدون passthrough (باش مايديرش loop)
  function getDecorUrl(asset, ext){
    const e = ext || "png";
    return `https://cdn.discordapp.com/avatar-decoration-presets/${asset}.${e}?size=256`;
  }

  function pickGameActivity(activities){
    return (activities || []).find(a => a.type === 0 && a.name);
  }

  const isMobile = window.matchMedia("(max-width: 480px)").matches;
  const refreshMs = isMobile ? 4000 : 3000;

  // ✅ decoration: png -> webp -> توقف
  function setDecoration(asset){
    decorAvatar.onerror = null;
    discordDecorMini.onerror = null;

    const pngUrl = getDecorUrl(asset, "png");
    const webpUrl = getDecorUrl(asset, "webp");
    let triedWebp = false;

    decorAvatar.src = pngUrl;
    discordDecorMini.src = pngUrl;
    decorAvatar.style.display = "block";
    discordDecorMini.style.display = "block";

    const onErr = () => {
      if(triedWebp){
        decorAvatar.style.display = "none";
        discordDecorMini.style.display = "none";
        return;
      }
      triedWebp = true;
      decorAvatar.src = webpUrl;
      discordDecorMini.src = webpUrl;
    };

    decorAvatar.onerror = onErr;
    discordDecorMini.onerror = onErr;
  }

  async function updateDiscord(){
    try{
      const res = await fetch(`https://api.lanyard.rest/v1/users/${userId}`);
      const json = await res.json();
      if(!json.success) return;

      const d = json.data;
      const u = d.discord_user;

      const avatarUrl = getAvatarUrl(u);
      if(avatarUrl !== lastAvatarUrl){
        lastAvatarUrl = avatarUrl;
        baseAvatar.src = avatarUrl;
        discordAvatar.src = avatarUrl;
      }

      const st = d.discord_status || "offline";
      if(st !== lastStatus){
        lastStatus = st;
        setStatusIcon(st);
        statusText.textContent = `Discord: ${statusLabel(st)}`;

        discordBox.classList.remove("glow-online","glow-idle","glow-dnd","glow-off");
        discordBox.classList.add(glowClass(st));
      }

      let activityText = "";
      let spotifyArt = "";

      if(d.spotify){
        activityText = `🎧 ${d.spotify.song} — ${d.spotify.artist}`;
        spotifyArt = d.spotify.album_art_url || "";
      } else {
        const game = pickGameActivity(d.activities);
        activityText = game ? `🎮 ${game.name}` : "";
      }

      if(activityText !== lastActivityText){
        lastActivityText = activityText;
        activityDiv.textContent = activityText;
      }

      if(spotifyArt){
        if(spotifyArt !== lastSpotifyArt){
          lastSpotifyArt = spotifyArt;
          spotifyCover.src = spotifyArt;
        }
        spotifyCover.style.display = "block";
      } else {
        lastSpotifyArt = "";
        spotifyCover.style.display = "none";
      }

      const dec = u.avatar_decoration_data;
      const asset = dec && dec.asset ? dec.asset : "";

      if(asset && asset !== lastDecorAsset){
        lastDecorAsset = asset;
        setDecoration(asset);
      }

      if(!asset && lastDecorAsset){
        lastDecorAsset = "";
        decorAvatar.style.display = "none";
        discordDecorMini.style.display = "none";
      }

    }catch(e){
      console.warn("Lanyard error:", e);
      setStatusIcon("offline");
      statusText.textContent = "Discord: Offline";
      activityDiv.textContent = "";
      spotifyCover.style.display = "none";
      decorAvatar.style.display = "none";
      discordDecorMini.style.display = "none";
    }
  }

  updateDiscord();
  setInterval(updateDiscord, refreshMs);

  // ✅ باش أي error ما يطيّحش الصفحة فبعض البيئات
  window.addEventListener("error", (e) => console.warn("JS error:", e.message));
  window.addEventListener("unhandledrejection", (e) => console.warn("Promise error:", e.reason));
</script>

</body>
</html>
