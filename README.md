<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>StudyMate AI - Jadwal HP</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #eef4ff;
      color: #16325c;
      padding: 16px;
    }

    .app {
      max-width: 640px;
      margin: 0 auto;
      display: grid;
      gap: 16px;
    }

    .card {
      background: rgba(255, 255, 255, 0.94);
      border-radius: 22px;
      padding: 18px;
      box-shadow: 0 14px 34px rgba(54, 93, 157, 0.08);
    }

    .kicker {
      margin: 0 0 8px;
      text-transform: uppercase;
      letter-spacing: 0.14em;
      font-size: 0.74rem;
      font-weight: bold;
      color: #4f8ef7;
    }

    h1, h2, h3, p {
      margin: 0;
    }

    .title {
      font-size: 1.6rem;
      line-height: 1.2;
    }

    .subtitle {
      margin-top: 10px;
      color: #7182a2;
      line-height: 1.6;
    }

    .day-tabs {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 10px;
    }

    .day-button {
      border: 0;
      border-radius: 16px;
      padding: 12px 10px;
      font-weight: bold;
      background: #dfeaff;
      color: #2d68cc;
      cursor: pointer;
    }

    .day-button.active {
      background: linear-gradient(135deg, #4f8ef7 0%, #6ca5ff 100%);
      color: white;
    }

    .status-text {
      color: #2d68cc;
      font-weight: bold;
      line-height: 1.5;
    }

    .schedule-list {
      display: grid;
      gap: 14px;
    }

    .schedule-item {
      border: 1px solid rgba(79, 142, 247, 0.14);
      border-radius: 18px;
      padding: 14px;
      background: #f9fbff;
      display: grid;
      gap: 12px;
    }

    .schedule-item h3 {
      font-size: 1rem;
      color: #2d68cc;
    }

    .field-group {
      display: grid;
      gap: 8px;
    }

    .field-group label {
      font-size: 0.92rem;
      font-weight: bold;
      color: #16325c;
    }

    input {
      width: 100%;
      box-sizing: border-box;
      padding: 12px 14px;
      border-radius: 14px;
      border: 1px solid rgba(91, 126, 183, 0.18);
      background: white;
      font: inherit;
      color: #16325c;
    }

    input:focus {
      outline: 2px solid rgba(79, 142, 247, 0.18);
      border-color: #4f8ef7;
    }

    .actions {
      display: grid;
      gap: 10px;
    }

    .primary-button,
    .secondary-button,
    .ghost-button {
      border: 0;
      border-radius: 999px;
      padding: 13px 16px;
      font: inherit;
      font-weight: bold;
      cursor: pointer;
    }

    .primary-button {
      background: linear-gradient(135deg, #4f8ef7 0%, #6ca5ff 100%);
      color: white;
    }

    .secondary-button {
      background: linear-gradient(135deg, #4caf50 0%, #7ed37f 100%);
      color: white;
    }

    .ghost-button {
      background: transparent;
      color: #2d68cc;
      border: 1px solid rgba(79, 142, 247, 0.2);
    }

    .small-note {
      color: #7182a2;
      line-height: 1.6;
      font-size: 0.94rem;
    }

    @media (min-width: 640px) {
      .actions {
        grid-template-columns: repeat(3, minmax(0, 1fr));
      }

      .day-tabs {
        grid-template-columns: repeat(5, minmax(0, 1fr));
      }
    }
  </style>
</head>
<body>
  <main class="app">
    <section class="card">
      <p class="kicker">Schedule</p>
      <h1 class="title">Jadwal Pelajaran</h1>
      <p class="subtitle">
        
      </p>
    </section>
    <section class="card">
        <p class="kicker">Pelajaran Sekarang</p>
        <h2 id="nowLessonTitle">Memuat...</h2>
        <p id="nowLessonTime" class="subtitle">Sedang mengecek jadwal hari ini.</p>
    </section>

    <section class="card">
      <p class="kicker">Pilih Hari</p>
      <div id="dayTabs" class="day-tabs"></div>
    </section>

    <section class="card">
      <p class="kicker">Hari Aktif</p>
      <h2 id="activeDayTitle">Senin</h2>
      <p id="statusText" class="status-text">Belum ada perubahan.</p>
    </section>

    <section class="card">
      <div id="scheduleList" class="schedule-list"></div>
    </section>

    <section class="card">
      <div class="actions">
        <button id="saveButton" class="primary-button" type="button">Simpan Jadwal</button>
        <button id="exampleButton" class="secondary-button" type="button">Isi Contoh</button>
        <button id="clearButton" class="ghost-button" type="button">Kosongkan Hari Ini</button>
      </div>
      <p class="small-note">
        Semangat Belajar.
      </p>
    </section>
  </main>

  <script>
    const STORAGE_KEY = "studymate-schedule-mobile";
    const DAYS = ["Senin", "Selasa", "Rabu", "Kamis", "Jumat"];
    const DAY_PERIODS = {
  Senin: ["0", "1", "2", "3", "4", "ISTIRAHAT", "5", "6", "7", "8", "9"],
  Selasa: ["0", "1", "2", "3", "4", "ISTIRAHAT 1", "5", "6", "7", "ISTIRAHAT 2", "8", "9", "10"],
  Rabu: ["0", "1", "2", "3", "4", "ISTIRAHAT 1", "5", "6", "7", "ISTIRAHAT 2", "8", "9", "10"],
  Kamis: ["0", "1", "2", "3", "4", "ISTIRAHAT 1", "5", "6", "7", "ISTIRAHAT 2", "8", "9", "10"],
  Jumat: ["0", "1", "2", "3", "4", "ISTIRAHAT 1", "5", "6", "7", "ISTIRAHAT 2", "8", "9", "10"]
};

    const dayTabs = document.getElementById("dayTabs");
    const activeDayTitle = document.getElementById("activeDayTitle");
    const scheduleList = document.getElementById("scheduleList");
    const statusText = document.getElementById("statusText");
    const saveButton = document.getElementById("saveButton");
    const exampleButton = document.getElementById("exampleButton");
    const clearButton = document.getElementById("clearButton");

    let scheduleData = loadScheduleData();
    let activeDay = "Senin";

    buildDayTabs();
    renderDay(activeDay);

    saveButton.addEventListener("click", saveCurrentDay);
    exampleButton.addEventListener("click", fillExampleDay);
    clearButton.addEventListener("click", clearCurrentDay);

    function buildDayTabs() {
      dayTabs.innerHTML = "";

      DAYS.forEach((day) => {
        const button = document.createElement("button");
        button.className = "day-button";
        if (day === activeDay) {
          button.classList.add("active");
        }
        button.textContent = day;
        button.type = "button";
        button.onclick = function () {
          activeDay = day;
          updateActiveTabs();
          renderDay(day);
        };
        dayTabs.appendChild(button);
      });
    }
function getCurrentLesson() {
  const now = new Date();
  const todayIndex = now.getDay();

  const dayMap = {
    1: "Senin",
    2: "Selasa",
    3: "Rabu",
    4: "Kamis",
    5: "Jumat"
  };

  const today = dayMap[todayIndex];
  if (!today) {
    return {
      title: "Hari ini tidak ada pelajaran",
      time: "Sekarang bukan jam sekolah."
    };
  }

  const currentMinutes = now.getHours() * 60 + now.getMinutes();
  const dayData = scheduleData[today] || {};

  for (const period in dayData) {
    const item = dayData[period];
    if (!item || !item.time) continue;

    const range = parseTimeRange(item.time);
    if (!range) continue;

    if (currentMinutes >= range.start && currentMinutes <= range.end) {
      return {
        title: item.subject || "Pelajaran belum diisi",
        time: `${today}, Jam ${period} • ${item.time}`
      };
    }
  }

  return {
    title: "Tidak ada pelajaran yang sedang berjalan",
    time: `${today}, saat ini di luar jam pelajaran.`
  };
}

function parseTimeRange(timeText) {
  const parts = timeText.split("-");
  if (parts.length !== 2) return null;

  const start = parseClock(parts[0].trim());
  const end = parseClock(parts[1].trim());

  if (start === null || end === null) return null;

  return { start, end };
}

function parseClock(text) {
  const parts = text.split(":");
  if (parts.length !== 2) return null;

  const hours = Number(parts[0]);
  const minutes = Number(parts[1]);

  if (Number.isNaN(hours) || Number.isNaN(minutes)) return null;

  return hours * 60 + minutes;
}

function renderCurrentLesson() {
  const lesson = getCurrentLesson();
  document.getElementById("nowLessonTitle").textContent = lesson.title;
  document.getElementById("nowLessonTime").textContent = lesson.time;
}

setInterval(renderCurrentLesson, 60000);
renderCurrentLesson();

    function updateActiveTabs() {
      document.querySelectorAll(".day-button").forEach((button) => {
        button.classList.toggle("active", button.textContent === activeDay);
      });
    }

  function renderDay(day) {
  activeDayTitle.textContent = day;
  scheduleList.innerHTML = "";

  const dayData = scheduleData[day] || {};
  const periods = DAY_PERIODS[day] || [];

  (DAY_PERIODS[day] || []).forEach((period) => {
    const item = document.createElement("article");
    item.className = "schedule-item";

    const title = document.createElement("h3");
    title.textContent = "Jam " + period;

    const subjectGroup = document.createElement("div");
    subjectGroup.className = "field-group";

    const subjectLabel = document.createElement("label");
    subjectLabel.textContent = "Guru / kegiatan";

    const subjectInput = document.createElement("input");
    subjectInput.type = "text";
    subjectInput.placeholder = "Contoh: Matematika - Robi Setiawan";
    subjectInput.dataset.period = period;
    subjectInput.dataset.field = "subject";
    subjectInput.value = dayData[period] ? (dayData[period].subject || "") : "";

    const timeGroup = document.createElement("div");
    timeGroup.className = "field-group";

    const timeLabel = document.createElement("label");
    timeLabel.textContent = "Waktu";

    const timeInput = document.createElement("input");
    timeInput.type = "text";
    timeInput.placeholder = "Contoh: 07:00 - 07:40";
    timeInput.dataset.period = period;
    timeInput.dataset.field = "time";
    timeInput.value = dayData[period] ? (dayData[period].time || "") : "";

    subjectGroup.appendChild(subjectLabel);
    subjectGroup.appendChild(subjectInput);

    timeGroup.appendChild(timeLabel);
    timeGroup.appendChild(timeInput);

    item.appendChild(title);
    item.appendChild(subjectGroup);
    item.appendChild(timeGroup);

    scheduleList.appendChild(item);
  });
}


    function saveCurrentDay() {
      if (!scheduleData[activeDay]) {
        scheduleData[activeDay] = {};
      }

      document.querySelectorAll("#scheduleList input").forEach((input) => {
        const period = input.dataset.period;
        const field = input.dataset.field;

        if (!scheduleData[activeDay][period]) {
          scheduleData[activeDay][period] = {
            subject: "",
            time: ""
          };
        }

        scheduleData[activeDay][period][field] = input.value.trim();
      });

      localStorage.setItem(STORAGE_KEY, JSON.stringify(scheduleData));
      statusText.textContent = "Jadwal " + activeDay + " berhasil disimpan.";
    }

    function clearCurrentDay() {
      const yes = confirm("Kosongkan semua jadwal untuk " + activeDay + "?");
      if (!yes) return;

      scheduleData[activeDay] = {};
      localStorage.setItem(STORAGE_KEY, JSON.stringify(scheduleData));
      renderDay(activeDay);
      statusText.textContent = "Jadwal " + activeDay + " sudah dikosongkan.";
    }

    function fillExampleDay() {
      scheduleData[activeDay] = {
        "0": { subject: activeDay === "Senin" ? "UPACARA" : "DOA PAGI", time: activeDay === "Senin" ? "06:45 - 09:00" : "06:45 - 07:00" },
        "1": { subject: "Matematika - Robi Setiawan", time: "07:00 - 07:40" },
        "2": { subject: "Bahasa Indonesia - Dra. Lusetya", time: "07:40 - 08:20" },
        "3": { subject: "IPA - Della Herlindungan", time: "08:20 - 09:00" },
        "ISTIRAHAT": { subject: "Istirahat", time: "09:40 - 10:00" }
      };

      renderDay(activeDay);
      statusText.textContent = "Contoh jadwal untuk " + activeDay + " berhasil dimuat. Tekan Simpan Jadwal.";
    }

    function loadScheduleData() {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (!raw) return {};

      try {
        return JSON.parse(raw);
      } catch {
        return {};
      }
    }
  </script>
</body>
</html>

