<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=1920, height=1080, initial-scale=1" />
  <title>大班互动反馈系统</title>
  <style>
    :root {
      --bg: #e8f4fc;
      --bg-card: #f5fbff;
      --primary: #4a9fd4;
      --primary-dark: #2d7eb5;
      --accent: #7ec8e8;
      --text: #1a3a52;
      --text-muted: #4a6a82;
      --border: #b8d9ee;
      --shadow: 0 4px 20px rgba(45, 126, 181, 0.15);
      --radius: 12px;
      --min-font: 16px;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html, body {
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: system-ui, -apple-system, "PingFang SC", "Hiragino Sans GB",
        "Microsoft YaHei", "Noto Sans CJK SC", "Source Han Sans SC", sans-serif;
      font-size: var(--min-font);
      color: var(--text);
      background: linear-gradient(160deg, #d4ebf7 0%, var(--bg) 40%, #cfe8f5 100%);
    }

    .app {
      width: 100vw;
      height: 100vh;
      max-width: 1920px;
      max-height: 1080px;
      margin: 0 auto;
      display: flex;
      flex-direction: column;
      padding: 20px 28px 24px;
      min-height: 0;
      overflow: hidden;
    }

    header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 16px;
      flex-shrink: 0;
    }

    header h1 {
      font-size: 28px;
      font-weight: 700;
      color: var(--primary-dark);
      letter-spacing: 2px;
    }

    .tabs {
      display: flex;
      gap: 10px;
      background: rgba(255, 255, 255, 0.6);
      padding: 6px;
      border-radius: 14px;
      border: 1px solid var(--border);
    }

    .tab-btn {
      font-size: 18px;
      min-height: 44px;
      padding: 10px 28px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      background: transparent;
      color: var(--text-muted);
      font-family: inherit;
      transition: all 0.2s;
    }

    .tab-btn.active {
      background: var(--primary);
      color: #fff;
      font-weight: 600;
      box-shadow: var(--shadow);
    }

    .tab-btn:hover:not(.active) {
      background: rgba(74, 159, 212, 0.15);
    }

    main {
      flex: 1;
      min-height: 0;
      background: var(--bg-card);
      border-radius: var(--radius);
      border: 1px solid var(--border);
      box-shadow: var(--shadow);
      padding: 24px 28px;
      overflow-x: hidden;
      overflow-y: auto;
      -webkit-overflow-scrolling: touch;
    }

    .panel {
      display: none;
      flex-direction: column;
      overflow: visible;
      min-height: min(100%, auto);
      padding-bottom: 28px;
    }

    .panel.active {
      display: flex;
    }

    .panel > .toolbar,
    .panel > .hint,
    .panel > .panel-title,
    .panel > .score-toolbar {
      flex-shrink: 0;
    }

    .panel-title {
      font-size: 22px;
      font-weight: 600;
      color: var(--primary-dark);
      margin-bottom: 16px;
      flex-shrink: 0;
    }

    .toolbar {
      display: flex;
      flex-wrap: wrap;
      gap: 14px;
      align-items: center;
      margin-bottom: 18px;
      flex-shrink: 0;
    }

    .btn {
      font-size: 17px;
      min-height: 44px;
      padding: 10px 22px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      font-family: inherit;
      transition: transform 0.1s, box-shadow 0.2s;
    }

    .btn:active {
      transform: scale(0.98);
    }

    .btn-primary {
      background: var(--primary);
      color: #fff;
    }

    .btn-primary:hover {
      background: var(--primary-dark);
    }

    .btn-secondary {
      background: #fff;
      color: var(--primary-dark);
      border: 2px solid var(--primary);
    }

    .btn-danger {
      background: #e57373;
      color: #fff;
    }

    .btn-warn {
      background: #ffb74d;
      color: #5d3a00;
    }

    .btn:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }

    label {
      font-size: 17px;
      color: var(--text-muted);
      display: flex;
      align-items: center;
      gap: 8px;
    }

    input[type="number"],
    input[type="text"],
    select {
      font-size: 17px;
      font-family: inherit;
      padding: 8px 12px;
      border: 2px solid var(--border);
      border-radius: 8px;
      width: 80px;
      background: #fff;
      color: var(--text);
    }

    input[type="file"] {
      font-size: 16px;
      font-family: inherit;
    }

    .hint {
      font-size: 16px;
      color: var(--text-muted);
      flex-shrink: 0;
    }

    #panel-group > .hint {
      margin-top: 6px;
      margin-bottom: 36px;
    }

    .status-badge {
      font-size: 16px;
      padding: 6px 14px;
      background: rgba(74, 159, 212, 0.2);
      border-radius: 20px;
      color: var(--primary-dark);
    }

    /* 随机分组：卡片边框随名单人数自适应 */
    .groups-grid {
      flex: 0 0 auto;
      overflow: visible;
      display: flex;
      flex-wrap: wrap;
      gap: 16px;
      align-items: flex-start;
      align-content: flex-start;
      padding-right: 8px;
      padding-bottom: 16px;
    }

    .group-card {
      background: #fff;
      border: 2px solid var(--border);
      border-radius: var(--radius);
      padding: 14px 18px;
      width: fit-content;
      max-width: 100%;
      height: fit-content;
      box-sizing: border-box;
      align-self: flex-start;
    }

    .group-card h3 {
      font-size: 20px;
      color: var(--primary-dark);
      margin-bottom: 8px;
      padding-bottom: 8px;
      border-bottom: 2px solid var(--accent);
      white-space: nowrap;
    }

    .group-card ul {
      list-style: none;
      font-size: 17px;
      line-height: 1.75;
      width: fit-content;
    }

    .group-card li {
      white-space: nowrap;
    }

    .group-card li::before {
      content: "• ";
      color: var(--primary);
    }

    /* 计时器 */
    .timer-wrap {
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 28px;
    }

    .timer-display {
      font-size: 120px;
      font-weight: 700;
      font-variant-numeric: tabular-nums;
      color: var(--primary-dark);
      letter-spacing: 4px;
      line-height: 1;
      text-shadow: 0 2px 8px rgba(45, 126, 181, 0.2);
    }

    .timer-controls {
      display: flex;
      flex-wrap: wrap;
      gap: 14px;
      justify-content: center;
      align-items: center;
    }

    .timer-presets {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      justify-content: center;
    }

    .timer-presets .btn {
      min-width: 72px;
    }

    /* 小组加分 */
    .score-toolbar {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      align-items: center;
      margin-bottom: 14px;
      flex-shrink: 0;
    }

    .score-teams {
      flex: 0 0 auto;
      overflow: visible;
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(340px, 1fr));
      gap: 18px;
      align-content: start;
      padding-right: 6px;
    }

    .score-team {
      background: #fff;
      border: 2px solid var(--border);
      border-radius: var(--radius);
      padding: 14px 16px;
    }

    .score-team-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 12px;
      padding-bottom: 8px;
      border-bottom: 2px solid var(--accent);
    }

    .score-team-header h3 {
      font-size: 20px;
      color: var(--primary-dark);
    }

    .team-total {
      font-size: 22px;
      font-weight: 700;
      color: var(--primary);
    }

    .member-btns {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
    }

    .member-btn {
      font-size: 17px;
      min-height: 48px;
      min-width: 88px;
      padding: 10px 16px;
      border: 2px solid var(--primary);
      border-radius: 10px;
      background: linear-gradient(180deg, #fff 0%, #e8f6fc 100%);
      color: var(--text);
      cursor: pointer;
      font-family: inherit;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 2px;
      transition: all 0.15s;
    }

    .member-btn:hover {
      background: var(--primary);
      color: #fff;
      transform: translateY(-2px);
      box-shadow: var(--shadow);
    }

    .member-btn .pts {
      font-size: 16px;
      color: var(--primary);
      font-weight: 600;
    }

    .member-btn:hover .pts {
      color: #fff;
    }

    .empty-tip {
      flex: 1 1 auto;
      min-height: 200px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
      color: var(--text-muted);
      text-align: center;
      line-height: 1.8;
    }

    .empty-tip.hidden {
      display: none !important;
    }

    /* 弹窗 */
    .overlay {
      display: none;
      position: fixed;
      inset: 0;
      background: rgba(26, 58, 82, 0.45);
      z-index: 1000;
      align-items: center;
      justify-content: center;
      padding: 24px;
    }

    .overlay.show {
      display: flex;
    }

    .modal {
      background: var(--bg-card);
      border-radius: 16px;
      border: 2px solid var(--border);
      box-shadow: 0 12px 48px rgba(0, 0, 0, 0.2);
      max-width: 900px;
      width: 100%;
      max-height: 85vh;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    .modal-header {
      padding: 20px 24px;
      border-bottom: 2px solid var(--border);
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .modal-header h2 {
      font-size: 24px;
      color: var(--primary-dark);
    }

    .modal-close {
      width: 40px;
      height: 40px;
      border: none;
      background: rgba(74, 159, 212, 0.2);
      border-radius: 50%;
      font-size: 22px;
      cursor: pointer;
      color: var(--text);
      font-family: inherit;
    }

    .modal-body {
      padding: 20px 24px;
      overflow-y: auto;
      flex: 1;
    }

    .rank-table {
      width: 100%;
      border-collapse: collapse;
      font-size: 17px;
    }

    .rank-table th,
    .rank-table td {
      padding: 12px 14px;
      text-align: left;
      border-bottom: 1px solid var(--border);
    }

    .rank-table th {
      background: rgba(74, 159, 212, 0.15);
      color: var(--primary-dark);
      font-weight: 600;
    }

    .rank-table tr:nth-child(1) td {
      font-weight: 700;
      color: #c62828;
    }

    .rank-table tr:nth-child(2) td {
      font-weight: 600;
      color: #e65100;
    }

    .rank-table tr:nth-child(3) td {
      font-weight: 600;
      color: #f9a825;
    }

    .mvp-box {
      margin-top: 20px;
      padding: 20px;
      background: linear-gradient(135deg, #fff9c4 0%, #ffe082 100%);
      border-radius: 12px;
      border: 2px solid #ffc107;
      text-align: center;
    }

    .mvp-box h3 {
      font-size: 22px;
      color: #e65100;
      margin-bottom: 12px;
    }

    .mvp-name {
      font-size: 36px;
      font-weight: 800;
      color: #bf360c;
    }

    .mvp-score {
      font-size: 20px;
      margin-top: 8px;
      color: var(--text-muted);
    }

    .toast {
      position: fixed;
      top: 24px;
      left: 50%;
      transform: translateX(-50%) translateY(-80px);
      background: var(--primary-dark);
      color: #fff;
      padding: 14px 28px;
      border-radius: 10px;
      font-size: 17px;
      z-index: 2000;
      opacity: 0;
      transition: all 0.3s;
      pointer-events: none;
    }

    .toast.show {
      transform: translateX(-50%) translateY(0);
      opacity: 1;
    }

    /* 课堂答题 */
    .quiz-main {
      flex: 0 0 auto;
      display: flex;
      flex-direction: column;
      overflow: visible;
      min-height: 0;
    }

    .quiz-timer-bar {
      flex-shrink: 0;
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      gap: 6px 10px;
      padding: 6px 12px;
      background: rgba(255, 255, 255, 0.85);
      border: 2px solid var(--border);
      border-radius: 10px;
      margin-bottom: 8px;
    }

    .quiz-timer-display {
      font-size: 28px;
      font-weight: 700;
      font-variant-numeric: tabular-nums;
      color: var(--primary-dark);
      min-width: 88px;
      line-height: 1;
    }

    .quiz-timer-controls {
      display: flex;
      flex-wrap: wrap;
      gap: 6px 8px;
      align-items: center;
      flex: 1;
    }

    .quiz-timer-controls label {
      font-size: 16px;
      gap: 6px;
    }

    .quiz-timer-controls #timerMode {
      border: 4px solid var(--primary);
      border-radius: 8px;
      padding: 4px 10px;
      font-weight: 600;
      min-width: 108px;
    }

    .quiz-timer-controls input[type="number"] {
      border-width: 1px;
      padding: 4px 8px;
      width: 56px;
      min-height: 32px;
    }

    .quiz-timer-controls .btn {
      min-height: 32px;
      padding: 4px 14px;
      font-size: 16px;
    }

    .quiz-timer-controls .btn-primary {
      border: none;
    }

    .quiz-timer-controls .btn-secondary {
      border-width: 1px;
    }

    .quiz-timer-preset {
      min-width: 64px;
      padding: 4px 10px;
    }

    .quiz-question-area {
      flex: 0 0 auto;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      text-align: center;
      padding: 16px 24px;
      gap: 16px;
      overflow: visible;
      width: 100%;
    }

    .quiz-progress {
      font-size: 18px;
      color: var(--text-muted);
    }

    .quiz-type-tag {
      font-size: 17px;
      padding: 6px 18px;
      background: rgba(74, 159, 212, 0.25);
      border-radius: 20px;
      color: var(--primary-dark);
      font-weight: 600;
    }

    .quiz-stem {
      font-size: 26px;
      line-height: 1.55;
      font-weight: 600;
      color: var(--text);
      max-width: 96%;
      width: 100%;
      text-align: left;
      overflow-y: auto;
      max-height: calc(52vh * 3);
      min-height: calc(52vh * 0.8);
      flex: 0 0 auto;
    }

    .quiz-options-inline {
      margin-top: 18px;
      padding-top: 14px;
      border-top: 2px dashed var(--border);
    }

    .quiz-options-inline .opt-line {
      font-size: 24px;
      line-height: 1.75;
      margin: 8px 0;
      font-weight: 500;
    }

    .quiz-sub-label {
      font-size: 24px;
      color: var(--primary-dark);
      font-weight: 700;
      margin: 18px 0 10px;
    }

    .quiz-sub-part {
      border-left: 5px solid var(--primary);
      padding-left: 18px;
      margin-top: 14px;
    }

    .quiz-sub-part.quiz-sub-active {
      border-left-width: 6px;
      border-left-color: var(--primary-dark);
      background: rgba(74, 159, 212, 0.08);
      border-radius: 8px;
      padding: 12px 14px 12px 18px;
    }

    .quiz-sub-part.quiz-sub-dim {
      opacity: 0.55;
    }

    .quiz-passage {
      margin-bottom: 16px;
      padding-bottom: 12px;
      border-bottom: 2px dashed var(--border);
    }

    .quiz-passage img,
    .quiz-pick-sub img {
      max-width: 100%;
      max-height: 360px;
    }

    .quiz-pick-sub {
      width: 100%;
      background: #fff;
      border: 2px solid var(--border);
      border-radius: 12px;
      padding: 16px 20px;
      margin-bottom: 14px;
      text-align: left;
      max-height: calc(52vh * 2);
      overflow-y: auto;
      flex-shrink: 0;
    }

    .quiz-pick-sub .quiz-sub-label {
      margin-top: 0;
    }

    .quiz-stem p {
      margin: 10px 0;
      font-weight: 500;
    }

    .quiz-stem img,
    .quiz-media img {
      max-width: 100%;
      max-height: 380px;
      width: auto;
      height: auto;
      margin: 14px auto;
      display: block;
      border-radius: 8px;
      border: 1px solid var(--border);
      box-shadow: var(--shadow);
    }

    .quiz-section-label {
      font-size: 17px;
      color: var(--text-muted);
      margin-bottom: 8px;
    }

    .quiz-media {
      max-width: 92%;
      text-align: center;
    }

    .analysis-report {
      font-size: 17px;
      line-height: 1.75;
    }

    .analysis-report h3 {
      font-size: 20px;
      color: var(--primary-dark);
      margin: 20px 0 10px;
      padding-bottom: 6px;
      border-bottom: 2px solid var(--accent);
    }

    .analysis-report h4 {
      font-size: 18px;
      margin: 14px 0 8px;
      color: var(--text);
    }

    .api-key-modal .hint {
      margin-bottom: 14px;
    }

    .api-key-modal input[type="password"] {
      width: 100%;
      margin-bottom: 14px;
      font-size: 17px;
      padding: 10px 12px;
      border: 2px solid var(--border);
      border-radius: 8px;
    }

    .api-key-status {
      font-size: 16px;
      color: var(--primary-dark);
      padding: 6px 12px;
      background: rgba(74, 159, 212, 0.15);
      border-radius: 8px;
    }

    .quiz-options-preview {
      font-size: 22px;
      line-height: 1.8;
      text-align: left;
      max-width: 800px;
      color: var(--text-muted);
    }

    .quiz-options-preview .opt-line {
      margin: 6px 0;
    }

    .quiz-pick-area {
      flex: 0 0 auto;
      display: flex;
      flex-direction: column;
      overflow: visible;
      width: 100%;
    }

    .quiz-pick-toolbar {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      align-items: center;
      margin-bottom: 12px;
      flex-shrink: 0;
    }

    .quiz-student-grid {
      flex: 0 0 auto;
      overflow: visible;
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      align-content: flex-start;
      padding: 4px 0;
    }

    .quiz-stu-btn {
      font-size: 18px;
      min-height: 52px;
      padding: 12px 20px;
      border: 2px solid var(--primary);
      border-radius: 10px;
      background: #fff;
      cursor: pointer;
      font-family: inherit;
      color: var(--text);
    }

    .quiz-stu-btn:hover {
      background: var(--primary);
      color: #fff;
    }

    .quiz-stu-btn .team-label {
      display: block;
      font-size: 16px;
      opacity: 0.75;
      margin-top: 2px;
    }

    .hidden {
      display: none !important;
    }

    .answer-brief,
    .answer-brief * {
      max-height: none;
      pointer-events: none !important;
    }

    .answer-brief {
      max-height: 240px;
      overflow-y: auto;
    }

    .answer-brief img {
      max-height: 160px;
    }

    #answerChoiceArea {
      position: relative;
      z-index: 2;
      pointer-events: auto;
    }

    .answer-opt-btn {
      display: block;
      width: 100%;
      text-align: left;
      font-size: 18px;
      min-height: 48px;
      padding: 12px 16px;
      margin-bottom: 10px;
      border: 2px solid var(--border);
      border-radius: 10px;
      background: #fff;
      cursor: pointer;
      font-family: inherit;
      pointer-events: auto;
      touch-action: manipulation;
      position: relative;
      z-index: 1;
    }

    .answer-opt-btn.selected {
      background: var(--primary);
      color: #fff;
      border-color: var(--primary-dark);
    }

    .answer-text-wrap textarea {
      width: 100%;
      font-size: 18px;
      font-family: inherit;
      padding: 12px;
      border: 2px solid var(--border);
      border-radius: 10px;
      min-height: 100px;
      resize: vertical;
      margin-bottom: 12px;
    }

    .voice-row {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      align-items: center;
      margin-bottom: 16px;
    }

    #voiceStatus {
      font-size: 16px;
      color: var(--text-muted);
    }

    .profile-card {
      background: #fff;
      border: 2px solid var(--border);
      border-radius: 12px;
      padding: 16px;
      margin-bottom: 14px;
    }

    .profile-card h4 {
      font-size: 20px;
      color: var(--primary-dark);
      margin-bottom: 8px;
    }

    .profile-card .profile-body {
      font-size: 17px;
      line-height: 1.7;
      white-space: pre-wrap;
    }

    .api-key-input {
      width: 220px !important;
    }

    .loading-text {
      font-size: 17px;
      color: var(--text-muted);
      padding: 20px;
      text-align: center;
    }

    @media (max-width: 1200px) {
      .timer-display {
        font-size: 80px;
      }
      .quiz-stem {
        font-size: 26px;
      }
    }
  </style>
</head>
<body>
  <div class="app">
    <header>
      <h1>大班互动反馈系统</h1>
      <nav class="tabs" role="tablist">
        <button class="tab-btn active" data-tab="group" role="tab">随机分组</button>
        <button class="tab-btn" data-tab="score" role="tab">小组加分</button>
        <button class="tab-btn" data-tab="quiz" role="tab">课堂答题</button>
      </nav>
    </header>

    <main>
      <!-- 随机分组 -->
      <section id="panel-group" class="panel active" role="tabpanel">
        <h2 class="panel-title">随机分组</h2>
        <div class="toolbar">
          <label>
            <input type="file" id="fileInput" accept=".xlsx,.xls,.csv,.txt" />
            上传名单（Excel / CSV）
          </label>
          <span class="status-badge" id="studentCount">未导入 · 0 人</span>
          <label>小组数量 <input type="number" id="groupCount" min="2" max="20" value="4" /></label>
          <label>每组最多人数 <input type="number" id="maxPerGroup" min="2" max="15" value="6" /></label>
          <button class="btn btn-primary" id="btnRandomGroup">随机分组</button>
        </div>
        <p class="hint">请将学生姓名放在 Excel 第一列（首行可为表头「姓名」等，会自动跳过）。支持 .xlsx / .xls / .csv，完全离线可用。</p>
        <div class="groups-grid" id="groupsGrid"></div>
        <div class="empty-tip" id="groupEmpty">请先上传班级名单，再设置参数并点击「随机分组」</div>
      </section>

      <!-- 小组加分 -->
      <section id="panel-score" class="panel" role="tabpanel">
        <h2 class="panel-title">小组加分</h2>
        <div class="score-toolbar">
          <button class="btn btn-danger" id="btnUndo" disabled>撤回上一次加分</button>
          <button class="btn btn-warn" id="btnSettle">结算</button>
          <span class="hint">点击学生姓名 +1 分（小组与个人同时累计）</span>
        </div>
        <div class="score-teams" id="scoreTeams"></div>
        <div class="empty-tip" id="scoreEmpty">请先在「随机分组」中完成分组，再到此加分</div>
      </section>

      <!-- 课堂答题 -->
      <section id="panel-quiz" class="panel" role="tabpanel">
        <h2 class="panel-title">课堂答题</h2>
        <div class="toolbar">
          <label>
            <input type="file" id="quizFileInput" accept=".docx,.doc,.txt" />
            导入 Word 题库（.docx）
          </label>
          <span class="status-badge" id="quizCountBadge">未导入 · 0 题</span>
          <span class="api-key-status" id="apiKeyStatus">DeepSeek：未配置</span>
          <button class="btn btn-secondary" id="btnConfigApiKey">配置 API（仅本会话）</button>
          <button class="btn btn-primary" id="btnStartQuiz" disabled>开始答题</button>
          <button class="btn btn-danger" id="btnEndQuiz" disabled>提前结束</button>
        </div>
        <p class="hint">Word 按大标题分块，如「一、单项选择题」，其下用「1.题目」编号；选项 A. B. C. D. 可写在同一行；答案行用【答案】。文档中的图片、图表会一并展示。分完组后方可开始。</p>
        <div class="quiz-main">
          <div class="quiz-timer-bar hidden" id="quizTimerBar">
            <div class="quiz-timer-display" id="timerDisplay">00:00</div>
            <div class="quiz-timer-controls">
              <label>
                模式
                <select id="timerMode">
                  <option value="countdown">倒计时</option>
                  <option value="stopwatch">正计时</option>
                </select>
              </label>
              <label>分 <input type="number" id="timerMin" min="0" max="99" value="5" /></label>
              <label>秒 <input type="number" id="timerSec" min="0" max="59" value="0" /></label>
              <button type="button" class="btn btn-primary" id="btnTimerStart">开始</button>
              <button type="button" class="btn btn-secondary" id="btnTimerPause">暂停</button>
              <button type="button" class="btn btn-secondary" id="btnTimerReset">重置</button>
              <button type="button" class="btn btn-secondary quiz-timer-preset" data-preset="60">1 分钟</button>
              <button type="button" class="btn btn-secondary quiz-timer-preset" data-preset="180">3 分钟</button>
              <button type="button" class="btn btn-secondary quiz-timer-preset" data-preset="300">5 分钟</button>
              <button type="button" class="btn btn-secondary quiz-timer-preset" data-preset="600">10 分钟</button>
            </div>
          </div>
          <div class="quiz-question-area hidden" id="quizQuestionArea">
            <div class="quiz-progress" id="quizProgress"></div>
            <div class="quiz-section-label hidden" id="quizSectionLabel"></div>
            <div class="quiz-type-tag" id="quizTypeTag"></div>
            <div class="quiz-stem" id="quizStem"></div>
            <div class="quiz-media hidden" id="quizExtraMedia"></div>
            <button class="btn btn-primary" id="btnReadyPick">展示完毕，选人答题</button>
          </div>
          <div class="quiz-pick-area hidden" id="quizPickArea">
            <div class="quiz-pick-sub hidden" id="pickSubQuestion"></div>
            <div class="quiz-pick-toolbar">
              <span class="hint" id="pickHint">点击学生姓名进入答题，或使用随机抽人</span>
              <button class="btn btn-secondary" id="btnRandomStudent">随机抽人</button>
              <button class="btn btn-primary hidden" id="btnNextQuestion">下一题</button>
            </div>
            <div class="quiz-student-grid" id="quizStudentGrid"></div>
          </div>
          <div class="empty-tip" id="quizEmpty">请先完成「随机分组」，再导入 Word 题库</div>
        </div>
      </section>
    </main>
  </div>

  <div class="overlay" id="overlayAnswer">
    <div class="modal" style="max-width: 720px;">
      <div class="modal-header">
        <h2 id="answerModalTitle">答题</h2>
        <button class="modal-close" data-close="overlayAnswer">×</button>
      </div>
      <div class="modal-body">
        <div class="hint answer-brief" id="answerQuestionBrief" style="margin-bottom:14px"></div>
        <div id="answerChoiceArea"></div>
        <div id="answerTextArea" class="answer-text-wrap hidden">
          <textarea id="answerText" placeholder="学生回答内容，可使用语音输入"></textarea>
          <div class="voice-row">
            <button type="button" class="btn btn-secondary" id="btnVoiceInput">语音输入</button>
            <span id="voiceStatus"></span>
          </div>
        </div>
        <button class="btn btn-primary" id="btnSubmitAnswer">确认提交（+1 分）</button>
      </div>
    </div>
  </div>

  <div class="overlay" id="overlayQuizResult">
    <div class="modal" style="max-width: 1000px;">
      <div class="modal-header">
        <h2>答题结束 · 排行榜</h2>
        <button class="modal-close" data-close="overlayQuizResult">×</button>
      </div>
      <div class="modal-body" id="quizResultBody"></div>
      <div style="padding:0 24px 20px;display:flex;gap:12px;flex-wrap:wrap">
        <button class="btn btn-primary" id="btnGenProfile">生成教学分析报告（DeepSeek）</button>
        <button class="btn btn-secondary" id="btnExportAnswers">导出答题记录</button>
      </div>
    </div>
  </div>

  <div class="overlay" id="overlayProfile">
    <div class="modal" style="max-width: 960px;">
      <div class="modal-header">
        <h2>教学分析报告</h2>
        <button class="modal-close" data-close="overlayProfile">×</button>
      </div>
      <div class="modal-body analysis-report" id="profileBody"></div>
    </div>
  </div>

  <div class="overlay" id="overlayApiKey">
    <div class="modal api-key-modal" style="max-width: 520px;">
      <div class="modal-header">
        <h2>配置 DeepSeek API</h2>
        <button class="modal-close" data-close="overlayApiKey">×</button>
      </div>
      <div class="modal-body">
        <p class="hint">API Key 仅保存在<strong>当前标签页内存</strong>中，关闭或刷新页面后自动清除，<strong>不会</strong>写入 localStorage 或导出文件。请勿在公共电脑上保存密钥。</p>
        <input type="password" id="apiKeyInput" autocomplete="off" placeholder="sk-..." />
        <div style="display:flex;gap:10px;flex-wrap:wrap">
          <button class="btn btn-primary" id="btnConfirmApiKey">确认（仅本会话）</button>
          <button class="btn btn-secondary" id="btnClearApiKey">清除密钥</button>
        </div>
      </div>
    </div>
  </div>

  <div class="overlay" id="overlaySettle">
    <div class="modal" style="max-width: 1000px;">
      <div class="modal-header">
        <h2>课堂结算</h2>
        <button class="modal-close" data-close="overlaySettle">×</button>
      </div>
      <div class="modal-body" id="settleBody"></div>
    </div>
  </div>

  <div class="toast" id="toast"></div>

  <script>
(function () {
  "use strict";

  // ========== 状态 ==========
  let students = [];
  let groups = [];
  let teamScores = {};
  let memberScores = {};
  let undoStack = [];
  let questions = [];
  let quizIndex = 0;
  let quizSubIndex = 0;
  let quizActive = false;
  let answerRecords = [];
  let currentAnswerStudent = null;
  let selectedOptions = [];
  let speechRec = null;

  const STORAGE_KEY = "classroom_display_v3";
  const QUESTIONS_FULL_KEY = "classroom_questions_full";
  const ANSWERS_KEY = "classroom_answers_v1";
  const API_KEY_SESSION = "ds_api_key_session";
  let apiKeyMemory = null;

  const TYPE_LABELS = {
    single: "单选题",
    multiple: "多选题",
    fill: "填空题",
    short: "简答题"
  };

  const TYPE_MAP = {
    单选题: "single", 单选: "single",
    多选题: "multiple", 多选: "multiple",
    填空题: "fill", 填空: "fill",
    简答题: "short", 简答: "short"
  };

  function saveState() {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify({
        students, groups, teamScores, memberScores,
        questions: questionsForStorage(), quizIndex, quizActive, quizSubIndex
      }));
      try {
        sessionStorage.setItem(QUESTIONS_FULL_KEY, JSON.stringify(questions));
      } catch (_) {}
      localStorage.setItem(ANSWERS_KEY, JSON.stringify(answerRecords));
    } catch (_) {}
  }

  function loadState() {
    try {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (raw) {
        const d = JSON.parse(raw);
        if (Array.isArray(d.students)) students = d.students;
        if (Array.isArray(d.groups)) groups = d.groups;
        if (d.teamScores) teamScores = d.teamScores;
        if (d.memberScores) memberScores = d.memberScores;
        if (Array.isArray(d.questions)) questions = d.questions;
        if (typeof d.quizIndex === "number") quizIndex = d.quizIndex;
        if (typeof d.quizSubIndex === "number") quizSubIndex = d.quizSubIndex;
        if (typeof d.quizActive === "boolean") quizActive = d.quizActive;
      }
      try {
        const fullQ = sessionStorage.getItem(QUESTIONS_FULL_KEY);
        if (fullQ) {
          const parsed = JSON.parse(fullQ);
          if (Array.isArray(parsed) && parsed.length) questions = parsed;
        }
      } catch (_) {}
      const ans = localStorage.getItem(ANSWERS_KEY);
      if (ans) answerRecords = JSON.parse(ans) || [];
      localStorage.removeItem("deepseek_api_key");
      updateApiKeyStatus();
      syncScoresStructure();
      updateStudentBadge();
      renderGroups();
      renderScorePanel();
      updateQuizBadge();
      updateQuizUI();
    } catch (_) {}
  }

  function saveAnswers() {
    try {
      localStorage.setItem(ANSWERS_KEY, JSON.stringify(answerRecords));
    } catch (_) {}
  }

  function syncScoresStructure() {
    groups.forEach((g) => {
      const tid = String(g.id);
      if (teamScores[tid] == null) teamScores[tid] = 0;
      if (!memberScores[tid]) memberScores[tid] = {};
      g.members.forEach((name) => {
        if (memberScores[tid][name] == null) memberScores[tid][name] = 0;
      });
    });
  }

  function showToast(msg) {
    const el = document.getElementById("toast");
    el.textContent = msg;
    el.classList.add("show");
    clearTimeout(showToast._t);
    showToast._t = setTimeout(() => el.classList.remove("show"), 2600);
  }

  // ========== 轻量 XLSX 解析（离线，无外部库） ==========
  function readUint16(dv, o) { return dv.getUint16(o, true); }
  function readUint32(dv, o) { return dv.getUint32(o, true); }

  async function inflateRaw(deflated) {
    if (typeof DecompressionStream !== "undefined") {
      const ds = new DecompressionStream("deflate-raw");
      const blob = new Blob([deflated]);
      const stream = blob.stream().pipeThrough(ds);
      const ab = await new Response(stream).arrayBuffer();
      return new Uint8Array(ab);
    }
    throw new Error("当前浏览器不支持解压 xlsx，请改用 CSV 或换用 Chrome/Edge 打开");
  }

  async function unzipAsync(bytes) {
    const dv = new DataView(bytes.buffer, bytes.byteOffset, bytes.byteLength);
    const files = {};
    let pos = 0;
    while (pos < bytes.length - 30) {
      if (bytes[pos] !== 0x50 || bytes[pos + 1] !== 0x4b || bytes[pos + 2] !== 0x03) {
        pos++;
        continue;
      }
      const compMethod = readUint16(dv, pos + 8);
      const compSize = readUint32(dv, pos + 18);
      const uncompSize = readUint32(dv, pos + 22);
      const nameLen = readUint16(dv, pos + 26);
      const extraLen = readUint16(dv, pos + 28);
      const name = new TextDecoder().decode(bytes.subarray(pos + 30, pos + 30 + nameLen));
      const dataStart = pos + 30 + nameLen + extraLen;
      let content = bytes.subarray(dataStart, dataStart + compSize);
      if (compMethod === 8) {
        try {
          content = await inflateRaw(content);
        } catch (_) {
          if (uncompSize > 0 && uncompSize <= bytes.length) {
            content = bytes.subarray(dataStart, dataStart + uncompSize);
          } else {
            throw _;
          }
        }
      } else if (compMethod === 0 && uncompSize > compSize) {
        content = bytes.subarray(dataStart, dataStart + uncompSize);
      }
      files[name] = content;
      pos = dataStart + compSize;
    }
    return files;
  }

  function parseXML(str) {
    return new DOMParser().parseFromString(str, "text/xml");
  }

  function getTextNodes(xml, tag) {
    const els = xml.getElementsByTagName(tag);
    const arr = [];
    for (let i = 0; i < els.length; i++) arr.push(els[i]);
    return arr;
  }

  function parseSharedStrings(xmlStr) {
    const doc = parseXML(xmlStr);
    const strings = [];
    const siList = getTextNodes(doc, "si");
    siList.forEach((si) => {
      const ts = si.getElementsByTagName("t");
      let s = "";
      for (let i = 0; i < ts.length; i++) s += ts[i].textContent || "";
      strings.push(s.trim());
    });
    return strings;
  }

  function colRow(ref) {
    const m = /^([A-Z]+)(\d+)$/.exec(ref);
    if (!m) return { col: 0, row: 0 };
    let col = 0;
    for (let i = 0; i < m[1].length; i++) col = col * 26 + (m[1].charCodeAt(i) - 64);
    return { col: col - 1, row: parseInt(m[2], 10) };
  }

  function parseSheet(xmlStr, shared) {
    const doc = parseXML(xmlStr);
    const rows = {};
    const cells = getTextNodes(doc, "c");
    cells.forEach((c) => {
      const ref = c.getAttribute("r") || "";
      const { col, row } = colRow(ref);
      const t = c.getAttribute("t");
      let val = "";
      const v = c.getElementsByTagName("v")[0];
      const is = c.getElementsByTagName("is")[0];
      if (t === "s" && v) {
        const idx = parseInt(v.textContent, 10);
        val = shared[idx] != null ? shared[idx] : "";
      } else if (is) {
        const ts = is.getElementsByTagName("t");
        for (let i = 0; i < ts.length; i++) val += ts[i].textContent || "";
      } else if (v) {
        val = v.textContent || "";
      }
      if (!rows[row]) rows[row] = {};
      rows[row][col] = String(val).trim();
    });
    return rows;
  }

  function rowsToNames(rows) {
    const headerWords = ["姓名", "名字", "学生", "成员", "name", "序号", "学号", "编号", "no", "id"];
    const names = [];
    const rowKeys = Object.keys(rows).map(Number).sort((a, b) => a - b);
    rowKeys.forEach((rk, idx) => {
      const row = rows[rk];
      const cols = Object.keys(row).map(Number).sort((a, b) => a - b);
      for (const c of cols) {
        let v = row[c];
        if (!v) continue;
        const lower = v.toLowerCase();
        if (idx === 0 && headerWords.some((h) => lower.includes(h))) continue;
        if (/^\d+$/.test(v) && cols[0] === c) continue;
        names.push(v);
        break;
      }
    });
    return [...new Set(names)].filter(Boolean);
  }

  async function parseXlsx(buffer) {
    const bytes = new Uint8Array(buffer);
    const files = await unzipAsync(bytes);
    let shared = [];
    const sharedPath = Object.keys(files).find((k) => k.includes("sharedStrings"));
    if (sharedPath) {
      shared = parseSharedStrings(new TextDecoder().decode(files[sharedPath]));
    }
    const sheetPath = Object.keys(files).find((k) => /sheet\d+\.xml$/i.test(k) && k.includes("worksheets"));
    if (!sheetPath) throw new Error("未找到工作表");
    const rows = parseSheet(new TextDecoder().decode(files[sheetPath]), shared);
    return rowsToNames(rows);
  }

  function parseCSV(text) {
    const lines = text.split(/\r?\n/).filter((l) => l.trim());
    const names = [];
    const headerWords = ["姓名", "名字", "学生", "name"];
    lines.forEach((line, idx) => {
      const cell = line.split(/[,;\t]/)[0].replace(/^"|"$/g, "").trim();
      if (!cell) return;
      if (idx === 0 && headerWords.some((h) => cell.toLowerCase().includes(h))) return;
      if (/^\d+$/.test(cell)) return;
      names.push(cell);
    });
    return [...new Set(names)];
  }

  async function parseExcelFile(file) {
    const ext = (file.name.split(".").pop() || "").toLowerCase();
    const buf = await file.arrayBuffer();
    if (ext === "csv" || ext === "txt") {
      const text = new TextDecoder("utf-8").decode(buf);
      return parseCSV(text);
    }
    if (ext === "xlsx" || ext === "xls") {
      try {
        return await parseXlsx(buf);
      } catch (e) {
        const text = new TextDecoder("utf-8").decode(buf);
        if (text.includes(",") || text.includes("\t")) return parseCSV(text);
        throw e;
      }
    }
    throw new Error("不支持的文件格式");
  }

  // ========== 随机分组 ==========
  function shuffle(arr) {
    const a = [...arr];
    for (let i = a.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [a[i], a[j]] = [a[j], a[i]];
    }
    return a;
  }

  function randomGroup(stuList, groupCount, maxPerGroup) {
    const total = stuList.length;
    const minNeeded = Math.ceil(total / maxPerGroup);
    if (groupCount < minNeeded) {
      return { error: `小组数量至少为 ${minNeeded}（按每组最多 ${maxPerGroup} 人计算）` };
    }
    const capacity = groupCount * maxPerGroup;
    if (total > capacity) {
      return { error: `学生共 ${total} 人，超出 ${groupCount} 组 × 每组 ${maxPerGroup} 人 的上限` };
    }
    const shuffled = shuffle(stuList);
    const result = Array.from({ length: groupCount }, (_, i) => ({
      id: i + 1,
      name: `第${i + 1}组`,
      members: []
    }));
    shuffled.forEach((name) => {
      const available = result.filter((g) => g.members.length < maxPerGroup);
      const pick = available[Math.floor(Math.random() * available.length)];
      pick.members.push(name);
    });
    return { groups: result };
  }

  function updateStudentBadge() {
    document.getElementById("studentCount").textContent =
      students.length ? `已导入 · ${students.length} 人` : "未导入 · 0 人";
  }

  function renderGroups() {
    const grid = document.getElementById("groupsGrid");
    const empty = document.getElementById("groupEmpty");
    grid.innerHTML = "";
    if (!groups.length) {
      empty.style.display = "flex";
      return;
    }
    empty.style.display = "none";
    groups.forEach((g) => {
      const card = document.createElement("div");
      card.className = "group-card";
      card.innerHTML =
        `<h3>${g.name}（${g.members.length} 人）</h3><ul>` +
        g.members.map((m) => `<li>${escapeHtml(m)}</li>`).join("") +
        "</ul>";
      grid.appendChild(card);
    });
  }

  function escapeHtml(s) {
    const d = document.createElement("div");
    d.textContent = s;
    return d.innerHTML;
  }

  // ========== 加分 ==========
  function renderScorePanel() {
    const container = document.getElementById("scoreTeams");
    const empty = document.getElementById("scoreEmpty");
    container.innerHTML = "";
    if (!groups.length) {
      empty.style.display = "flex";
      document.getElementById("btnUndo").disabled = true;
      return;
    }
    empty.style.display = "none";
    groups.forEach((g) => {
      const tid = String(g.id);
      const teamTotal = teamScores[tid] || 0;
      const block = document.createElement("div");
      block.className = "score-team";
      block.innerHTML = `
        <div class="score-team-header">
          <h3>${escapeHtml(g.name)}</h3>
          <span class="team-total" data-team-total="${tid}">${teamTotal} 分</span>
        </div>
        <div class="member-btns"></div>`;
      const btnWrap = block.querySelector(".member-btns");
      g.members.forEach((name) => {
        const pts = (memberScores[tid] && memberScores[tid][name]) || 0;
        const btn = document.createElement("button");
        btn.type = "button";
        btn.className = "member-btn";
        btn.innerHTML = `<span>${escapeHtml(name)}</span><span class="pts">${pts} 分</span>`;
        btn.addEventListener("click", () => addPoint(tid, name));
        btnWrap.appendChild(btn);
      });
      container.appendChild(block);
    });
    document.getElementById("btnUndo").disabled = undoStack.length === 0;
  }

  function addPoint(tid, name) {
    if (!memberScores[tid]) memberScores[tid] = {};
    if (memberScores[tid][name] == null) memberScores[tid][name] = 0;
    memberScores[tid][name]++;
    teamScores[tid] = (teamScores[tid] || 0) + 1;
    undoStack.push({ tid, name });
    document.getElementById("btnUndo").disabled = false;
    updateScoreUI(tid, name);
    saveState();
  }

  function updateScoreUI(tid, name) {
    renderScorePanel();
  }

  function undoLast() {
    if (!undoStack.length) return;
    const { tid, name } = undoStack.pop();
    if (memberScores[tid] && memberScores[tid][name] > 0) memberScores[tid][name]--;
    if (teamScores[tid] > 0) teamScores[tid]--;
    document.getElementById("btnUndo").disabled = undoStack.length === 0;
    renderScorePanel();
    saveState();
    showToast("已撤回上一次加分");
  }

  // ========== API Key（仅会话内存，不落盘明文） ==========
  function getApiKey() {
    return apiKeyMemory || null;
  }

  function setApiKey(key) {
    apiKeyMemory = key;
    try {
      sessionStorage.setItem(API_KEY_SESSION, key);
    } catch (_) {}
    updateApiKeyStatus();
  }

  function clearApiKey() {
    apiKeyMemory = null;
    try {
      sessionStorage.removeItem(API_KEY_SESSION);
    } catch (_) {}
    localStorage.removeItem("deepseek_api_key");
    const inp = document.getElementById("apiKeyInput");
    if (inp) inp.value = "";
    updateApiKeyStatus();
    showToast("API Key 已清除");
  }

  function initApiKey() {
    try {
      const k = sessionStorage.getItem(API_KEY_SESSION);
      if (k) apiKeyMemory = k;
    } catch (_) {}
    localStorage.removeItem("deepseek_api_key");
    updateApiKeyStatus();
  }

  function updateApiKeyStatus() {
    const el = document.getElementById("apiKeyStatus");
    if (!el) return;
    el.textContent = getApiKey()
      ? "DeepSeek：已配置（仅本会话）"
      : "DeepSeek：未配置";
  }

  function ensureApiKey() {
    if (getApiKey()) return true;
    document.getElementById("overlayApiKey").classList.add("show");
    showToast("请先配置 DeepSeek API Key");
    return false;
  }

  function questionsForStorage() {
    return questions.map((q) => ({
      id: q.id,
      type: q.type,
      section: q.section,
      stem: q.stem,
      options: q.options,
      answer: q.answer,
      fullContent: q.fullContent,
      imageCount: (q.images && q.images.length) || 0,
      subQuestionCount: (q.subQuestions && q.subQuestions.length) || 0
    }));
  }

  // ========== Word 题库解析（大标题 + 图片） ==========
  function bytesToDataUrl(bytes, filename) {
    const ext = (filename.split(".").pop() || "png").toLowerCase();
    const mime =
      { png: "image/png", jpg: "image/jpeg", jpeg: "image/jpeg", gif: "image/gif",
        bmp: "image/bmp", webp: "image/webp", emf: "image/emf", wmf: "image/wmf" }[ext] ||
      "image/png";
    let bin = "";
    const chunk = 8192;
    for (let i = 0; i < bytes.length; i += chunk) {
      const sub = bytes.subarray(i, i + chunk);
      bin += String.fromCharCode.apply(null, sub);
    }
    return "data:" + mime + ";base64," + btoa(bin);
  }

  function getRelEmbedId(node) {
    if (!node || !node.getAttribute) return null;
    return (
      node.getAttribute("r:embed") ||
      node.getAttributeNS(
        "http://schemas.openxmlformats.org/officeDocument/2006/relationships",
        "embed"
      )
    );
  }

  function buildDocxImageMap(files) {
    const map = {};
    const relsPath = Object.keys(files).find((k) => /word\/_rels\/document\.xml\.rels$/i.test(k));
    if (!relsPath) return map;
    const relDoc = parseXML(new TextDecoder("utf-8").decode(files[relsPath]));
    const rels = relDoc.getElementsByTagName("Relationship");
    for (let i = 0; i < rels.length; i++) {
      const rel = rels[i];
      const id = rel.getAttribute("Id");
      const target = rel.getAttribute("Target") || "";
      if (!/media\//i.test(target)) continue;
      const mediaPath = "word/" + target.replace(/^\.\//, "");
      if (files[mediaPath]) map[id] = bytesToDataUrl(files[mediaPath], mediaPath);
    }
    return map;
  }

  function extractImagesFromNode(node, imageMap, out) {
    if (!node || node.nodeType !== 1) return;
    const ln = (node.localName || node.tagName || "").toLowerCase();
    if (ln === "blip") {
      const rid = getRelEmbedId(node);
      if (rid && imageMap[rid]) out.push({ src: imageMap[rid] });
    }
    for (let c = node.firstChild; c; c = c.nextSibling) extractImagesFromNode(c, imageMap, out);
  }

  function extractParagraph(p, imageMap) {
    const images = [];
    extractImagesFromNode(p, imageMap, images);
    const ts = p.getElementsByTagName("w:t");
    let text = "";
    for (let i = 0; i < ts.length; i++) text += ts[i].textContent || "";
    text = text.replace(/\s+/g, " ").trim();
    return { text, images };
  }

  async function parseDocxFull(buffer) {
    const files = await unzipAsync(new Uint8Array(buffer));
    const docPath = Object.keys(files).find((k) => /word\/document\.xml$/i.test(k));
    if (!docPath) throw new Error("无效的 Word 文件");
    const imageMap = buildDocxImageMap(files);
    const doc = parseXML(new TextDecoder("utf-8").decode(files[docPath]));
    const ps = doc.getElementsByTagName("w:p");
    const paragraphs = [];
    for (let i = 0; i < ps.length; i++) {
      const para = extractParagraph(ps[i], imageMap);
      if (para.text || para.images.length) paragraphs.push(para);
    }
    return { paragraphs, imageMap };
  }

  function isSectionHeader(text) {
    if (
      /^[（(]?[一二三四五六七八九十百千0-9]+[）)]?[、．.\s]/.test(text) &&
      /(单项选择|多项选择|单选|多选|填空|简答|判断|材料|论述|非选择|综合)/.test(text)
    ) {
      return true;
    }
    return /^(第[一二三四五六七八九十百千\d]+[部分节题]|[Part][^\s]*)/i.test(text) &&
      /(选择|填空|简答|材料|论述|非选择)/.test(text);
  }

  function detectSectionType(text) {
    if (/多项|多选/.test(text)) return "multiple";
    if (/单项|单选|判断/.test(text)) return "single";
    if (/填空/.test(text)) return "fill";
    if (/简答|论述|材料|非选择|综合/.test(text)) return "short";
    return "short";
  }

  function isSubPartMarker(text) {
    return !!parseSubPartLabel(text);
  }

  function getQuestionNumber(text) {
    const m = (text || "").trim().match(/^(\d+)[\.．、\)）\s]/);
    return m ? parseInt(m[1], 10) : null;
  }

  function isQuestionStart(text, sectionType) {
    const t = text.trim();
    if (/^\d+[\.．、\)）]/.test(t)) return true;
    if (/^\d+[、\s]\S/.test(t)) return true;
    return false;
  }

  function sectionAutoStartsQuestion(sectionType, sectionTitle) {
    return (
      sectionType === "short" ||
      sectionType === "fill" ||
      /简答|材料|论述|非选择|综合/.test(sectionTitle || "")
    );
  }

  function isMaterialQuestionStart(text) {
    return /^(阅读材料|阅读下列|结合.*所学|根据材料|完成.*要求|材料[一二三四五六七八九十\d]+)/.test(
      text.trim()
    );
  }

  function currentHasAnswer(current) {
    return (
      current &&
      current.blocks.some(
        (b) => isAnswerLine(b.text) || /^【\s*答案\s*】\s*$/i.test(b.text)
      )
    );
  }

  function shouldStartNewQuestionAfterAnswer(text) {
    const t = text.trim();
    if (/^\d+[\.．、\)）]/.test(t)) return true;
    return isMaterialQuestionStart(t);
  }

  function isAnswerLine(text) {
    return /^【\s*答案\s*】/i.test(text) || /^答案\s*[：:]/i.test(text);
  }

  function parseAnswerText(text) {
    return text
      .replace(/^【\s*答案\s*】\s*/i, "")
      .replace(/^答案\s*[：:]\s*/i, "")
      .trim();
  }

  function parseOptionsFromText(text) {
    const opts = [];
    if (!text) return opts;
    const parts = text.split(/\s*(?=[A-G][\.．、:：)\s])/i).filter((p) => p.trim());
    parts.forEach((part) => {
      const m = part.trim().match(/^([A-G])[\.．、:：)\s]\s*(.+)$/i);
      if (m) opts.push({ key: m[1].toUpperCase(), text: m[2].trim() });
    });
    return opts;
  }

  function extractInlineOptions(line) {
    const m = line.match(/\s([A-G][\.．、:：)\s]\s*.+)/i);
    if (!m) return { stemLine: line, options: [] };
    const idx = line.indexOf(m[1]);
    const stemLine = line.slice(0, idx).trim();
    const options = parseOptionsFromText(line.slice(idx));
    return { stemLine, options };
  }

  function getOptionsForQuestion(q) {
    if (q.options && q.options.length >= 2) return q.options;
    const lines = (q.fullContent || q.stem || "").split("\n");
    for (let i = 0; i < lines.length; i++) {
      const o = parseOptionsFromText(lines[i]);
      if (o.length >= 2) return o;
    }
    const all = parseOptionsFromText(q.fullContent || q.stem || "");
    if (all.length >= 2) return all;
    if (q.type === "single" || q.type === "multiple") {
      return ["A", "B", "C", "D"].map((k) => ({ key: k, text: "选项 " + k }));
    }
    return [];
  }

  function lineLooksLikeOptions(text) {
    const opts = parseOptionsFromText(text);
    return opts.length >= 2;
  }

  function isSingleOptionLine(text) {
    return /^[A-G][\.．、:：)\s]\s*\S/i.test((text || "").trim());
  }

  function isOptionContent(text) {
    return lineLooksLikeOptions(text) || isSingleOptionLine(text);
  }

  function dedupeOptions(opts) {
    const seen = {};
    const out = [];
    opts.forEach((o) => {
      if (!o || !o.key || seen[o.key]) return;
      seen[o.key] = true;
      out.push(o);
    });
    return out;
  }

  function getMcOptions(q) {
    if (q.options && q.options.length >= 2) return dedupeOptions(q.options);
    return dedupeOptions(getOptionsForQuestion(q));
  }

  function blocksToHtml(blocks) {
    let html = "";
    blocks.forEach((b) => {
      if (b.text) html += "<p>" + escapeHtml(b.text) + "</p>";
      (b.images || []).forEach((img) => {
        html += '<img src="' + img.src + '" alt="题目配图" loading="lazy" />';
      });
    });
    return html;
  }

  function optionsToHtml(options) {
    if (!options || !options.length) return "";
    let html = '<div class="quiz-options-inline">';
    options.forEach((o) => {
      html +=
        '<p class="opt-line"><strong>' +
        escapeHtml(o.key) +
        ".</strong> " +
        escapeHtml(o.text) +
        "</p>";
    });
    html += "</div>";
    return html;
  }

  function parseSubPartLabel(text) {
    const t = (text || "").trim();
    let m = t.match(/^[（(]\s*(\d+)\s*[）)]\s*(.+)$/);
    if (m) return { num: m[1], label: "（" + m[1] + "）", rest: m[2].trim() };
    m = t.match(/^[（(]\s*(\d+)\s*[）)]\s*$/);
    if (m) return { num: m[1], label: "（" + m[1] + "）", rest: "" };
    m = t.match(/^\(\s*(\d+)\s*\)\s*(.+)$/);
    if (m) return { num: m[1], label: "（" + m[1] + "）", rest: m[2].trim() };
    m = t.match(/^\(\s*(\d+)\s*\)\s*$/);
    if (m) return { num: m[1], label: "（" + m[1] + "）", rest: "" };
    return null;
  }

  function stripOptionsFromHtml(html) {
    return (html || "").replace(
      /<div[^>]*class="quiz-options-inline"[^>]*>[\s\S]*?<\/div>/gi,
      ""
    );
  }

  function getAllDisplayedHtml(q, subIdx) {
    let html = buildQuestionDisplayHtml(q, subIdx);
    if (q.subQuestions && q.subQuestions.length) {
      html += (q.passageHtml || "") + q.subQuestions.map((s) => s.stemHtml || "").join("");
    }
    return html;
  }

  function renderQuizExtraImages(q, subIdx) {
    const mediaEl = document.getElementById("quizExtraMedia");
    if (!mediaEl) return;
    const html = getAllDisplayedHtml(q, subIdx != null ? subIdx : quizSubIndex);
    const extra = (q.images || []).filter((img) => html.indexOf(img.src) < 0);
    if (extra.length) {
      mediaEl.innerHTML = extra
        .map((img) => '<img src="' + img.src + '" alt="题目配图" loading="lazy" />')
        .join("");
      mediaEl.classList.remove("hidden");
    } else {
      mediaEl.innerHTML = "";
      mediaEl.classList.add("hidden");
    }
  }

  function extractSubQuestions(blocks) {
    const passage = [];
    const subs = [];
    let current = null;

    blocks.forEach((b) => {
      if (isAnswerLine(b.text)) return;
      const sp = parseSubPartLabel(b.text);
      if (sp) {
        if (current) subs.push(current);
        current = {
          label: sp.label,
          num: sp.num,
          blocks: [{ text: b.text, images: (b.images || []).slice() }]
        };
      } else if (current) {
        current.blocks.push({ text: b.text, images: (b.images || []).slice() });
      } else {
        passage.push({ text: b.text, images: (b.images || []).slice() });
      }
    });
    if (current) subs.push(current);

    const mapped = subs.map((sub) => {
      const stem = sub.blocks
        .map((x) => x.text)
        .join(" ")
        .replace(/^[（(]\d+[）)]\s*/, "")
        .replace(/^\(\d+\)\s*/, "");
      return {
        label: sub.label,
        num: parseInt(sub.num, 10) || 0,
        stem,
        stemHtml: blocksToHtml(sub.blocks),
        blocks: sub.blocks
      };
    });
    mapped.sort((a, b) => a.num - b.num);
    return { passage, subQuestions: mapped };
  }

  function questionHasSubParts(q) {
    return !!(q.subQuestions && q.subQuestions.length);
  }

  function buildQuestionDisplayHtml(q, subIdx) {
    const idx = subIdx != null ? subIdx : 0;
    if (questionHasSubParts(q)) {
      let html = "";
      const passage = q.passageHtml || q.stemHtml || "";
      if (passage) {
        html += '<div class="quiz-passage">' + passage + "</div>";
      }
      const sub = q.subQuestions[idx];
      if (sub) {
        html +=
          '<div class="quiz-sub-part quiz-sub-active"><p class="quiz-sub-label">小问 ' +
          escapeHtml(sub.label) +
          "</p>" +
          (sub.stemHtml || "<p>" + escapeHtml(sub.stem) + "</p>") +
          "</div>";
      }
      return html;
    }
    let html = q.stemHtml || "<p>" + escapeHtml(q.stem) + "</p>";
    if (q.type === "single" || q.type === "multiple") {
      const opts = getMcOptions(q);
      if (opts.length >= 2) html += optionsToHtml(opts);
    }
    return html;
  }

  function buildAnswerBriefHtml(q, subIdx) {
    const idx = subIdx != null ? subIdx : 0;
    if (q.subQuestions && q.subQuestions.length) {
      const sub = q.subQuestions[idx];
      if (!sub) return "";
      return (
        '<div class="quiz-sub-part"><p class="quiz-sub-label">' +
        escapeHtml(sub.label) +
        "</p>" +
        (sub.stemHtml || "<p>" + escapeHtml(sub.stem) + "</p>") +
        "</div>"
      );
    }
    if (q.type === "single" || q.type === "multiple") {
      let html = q.stemHtml || "<p>" + escapeHtml(q.stem) + "</p>";
      return stripOptionsFromHtml(html);
    }
    return q.stemHtml || "<p>" + escapeHtml(q.stem) + "</p>";
  }

  function buildPickSubHtml(q, subIdx) {
    const sub = q.subQuestions[subIdx];
    let html =
      '<p class="quiz-sub-label">请为 ' + escapeHtml(sub.label) + " 选择作答学生</p>";
    if (q.passageHtml) {
      html += '<div class="quiz-passage">' + q.passageHtml + "</div>";
    }
    html += sub.stemHtml || "<p>" + escapeHtml(sub.stem) + "</p>";
    const extra = (q.images || []).filter((img) => html.indexOf(img.src) < 0);
    if (extra.length) {
      html += extra
        .map((img) => '<img src="' + img.src + '" alt="题目配图" loading="lazy" />')
        .join("");
    }
    return html;
  }

  function finalizeShortQuestion(raw, sectionType, sectionTitle, result, allImages) {
    let answer = "";
    raw.blocks.forEach((b) => {
      if (isAnswerLine(b.text)) answer = parseAnswerText(b.text);
    });
    const { passage, subQuestions } = extractSubQuestions(raw.blocks);

    if (subQuestions.length >= 1) {
      const passageText = passage.map((b) => b.text).filter(Boolean).join("\n");
      let passageHtml = blocksToHtml(passage);
      if (!passageHtml && passage.some((b) => b.images && b.images.length)) {
        passageHtml = blocksToHtml(passage);
      }
      if (!passageHtml) {
        const beforeFirstSub = [];
        raw.blocks.forEach((b) => {
          if (isAnswerLine(b.text)) return;
          if (parseSubPartLabel(b.text)) return;
          beforeFirstSub.push(b);
        });
        passageHtml = blocksToHtml(beforeFirstSub);
      }
      result.push({
        id: result.length + 1,
        type: "short",
        section: sectionTitle,
        stem: passageText.slice(0, 300) || subQuestions[0].stem,
        stemHtml: passageHtml || blocksToHtml(raw.blocks.filter((b) => !isAnswerLine(b.text))),
        passageHtml: passageHtml,
        subQuestions,
        images: allImages,
        options: [],
        answer,
        fullContent:
          passageText + "\n" + subQuestions.map((s) => s.stem).join("\n")
      });
      return;
    }

    const contentBlocks = raw.blocks.filter((b) => !isAnswerLine(b.text));
    const fullText = contentBlocks.map((b) => b.text).join("\n");
    const stemHtml = blocksToHtml(contentBlocks);
    result.push({
      id: result.length + 1,
      type: sectionType,
      section: sectionTitle,
      stem: fullText.slice(0, 300),
      stemHtml,
      passageHtml: "",
      subQuestions: [],
      images: allImages,
      options: [],
      answer,
      fullContent: fullText
    });
  }

  function getCurrentSubQuestion(q) {
    if (!q.subQuestions || !q.subQuestions.length) return null;
    return q.subQuestions[quizSubIndex] || null;
  }

  function finalizeQuestion(raw, sectionType, sectionTitle, result) {
    if (!raw || !raw.blocks.length) return;

    const allImages = [];
    raw.blocks.forEach((b) => {
      (b.images || []).forEach((img) => allImages.push(img));
    });

    const extractedSubs = extractSubQuestions(raw.blocks);
    if (extractedSubs.subQuestions.length >= 1) {
      finalizeShortQuestion(raw, sectionType, sectionTitle, result, allImages);
      return;
    }
    if (sectionType === "short" || sectionType === "fill") {
      finalizeShortQuestion(raw, sectionType, sectionTitle, result, allImages);
      return;
    }

    const textLines = [];
    const stemBlocks = [];
    let options = [];
    let answer = "";
    raw.blocks.forEach((b, blockIdx) => {
      if (isAnswerLine(b.text)) {
        answer = parseAnswerText(b.text);
        if (!answer) {
          const idx = raw.blocks.indexOf(b);
          for (let i = idx + 1; i < raw.blocks.length; i++) {
            const t = raw.blocks[i].text.trim();
            if (!t || isQuestionStart(t, sectionType)) break;
            if (!lineLooksLikeOptions(t)) {
              answer = t;
              break;
            }
          }
        }
        return;
      }
      let lineText = b.text;
      let lineOpts = [];
      if (blockIdx === 0 && (sectionType === "single" || sectionType === "multiple")) {
        const extracted = extractInlineOptions(b.text);
        if (extracted.options.length >= 2) {
          lineText = extracted.stemLine;
          lineOpts = extracted.options;
        }
      }
      if (lineOpts.length >= 2) {
        options = dedupeOptions(lineOpts);
        if (lineText) {
          textLines.push(lineText);
          stemBlocks.push({ text: lineText, images: b.images || [] });
        }
        return;
      }
      if (
        (sectionType === "single" || sectionType === "multiple") &&
        isOptionContent(b.text)
      ) {
        options = dedupeOptions(options.concat(parseOptionsFromText(b.text)));
        return;
      }
      if (lineText) textLines.push(lineText);
      stemBlocks.push({ text: lineText || b.text, images: b.images || [] });
    });
    let stem = textLines[0] || raw.blocks[0].text || "";
    stem = stem.replace(/^\d+[\.．、\)）]\s*/, "");
    options = dedupeOptions(options);
    const fullContent = textLines.join("\n");
    const stemOnlyBlocks = stemBlocks.filter((b) => !isOptionContent(b.text || ""));
    const stemOnlyHtml = blocksToHtml(stemOnlyBlocks);
    result.push({
      id: result.length + 1,
      type: sectionType,
      section: sectionTitle,
      stem,
      stemHtml: stemOnlyHtml,
      images: allImages,
      options,
      answer,
      fullContent: fullContent || stem
    });
  }

  function parseQuestionsFromParagraphs(paragraphs) {
    const result = [];
    let sectionType = "single";
    let sectionTitle = "";
    let current = null;
    let pendingAnswerLine = false;
    let autoStartSection = false;

    const flush = () => {
      pendingAnswerLine = false;
      if (current) {
        finalizeQuestion(current, sectionType, sectionTitle, result);
        current = null;
      }
    };

    const startQuestion = (text, images) => {
      current = { blocks: [{ text: text || "", images: (images || []).slice() }] };
      autoStartSection = false;
    };

    paragraphs.forEach((para) => {
      const { text, images } = para;
      if (!text && !images.length) return;

      if (pendingAnswerLine && current && text) {
        current.blocks.push({ text: "【答案】" + text, images: images.slice() });
        pendingAnswerLine = false;
        return;
      }

      if (text && /^【\s*答案\s*】\s*$/i.test(text)) {
        if (current) pendingAnswerLine = true;
        return;
      }

      if (text && isSectionHeader(text)) {
        flush();
        sectionTitle = text;
        sectionType = detectSectionType(text);
        autoStartSection = sectionAutoStartsQuestion(sectionType, sectionTitle);
        return;
      }

      if (text && isQuestionStart(text, sectionType)) {
        flush();
        startQuestion(text, images);
        return;
      }

      if (!current) {
        if (images.length && result.length) {
          const last = result[result.length - 1];
          last.images = last.images || [];
          images.forEach((image) => {
            last.images.push(image);
            const imgTag =
              '<img src="' + image.src + '" alt="题目配图" loading="lazy" />';
            if (last.subQuestions && last.subQuestions.length) {
              last.passageHtml = (last.passageHtml || "") + imgTag;
            } else {
              last.stemHtml = (last.stemHtml || "") + imgTag;
            }
          });
        }
        if (text && autoStartSection) {
          startQuestion(text, images);
          return;
        }
        if (!text && images.length && autoStartSection) {
          startQuestion("", images);
          return;
        }
        return;
      }

      if (text && current) {
        const newNum = getQuestionNumber(text);
        const curNum = getQuestionNumber(current.blocks[0] && current.blocks[0].text);
        if (newNum && curNum && newNum > curNum) {
          flush();
          startQuestion(text, images);
          return;
        }
      }

      if (text && currentHasAnswer(current) && shouldStartNewQuestionAfterAnswer(text)) {
        flush();
        if (isMaterialQuestionStart(text)) sectionType = "short";
        startQuestion(text, images);
        return;
      }

      if (!text && images.length && current) {
        current.blocks.push({ text: "", images: images.slice() });
        return;
      }

      if (text && isSubPartMarker(text)) {
        if (!current && (autoStartSection || sectionTitle)) {
          startQuestion(text, images);
          return;
        }
        current.blocks.push({ text: text, images: images.slice() });
        return;
      }

      current.blocks.push({ text: text || "", images: images.slice() });
    });
    flush();
    return result;
  }

  function parseQuestionsFromText(text) {
    const lines = text.replace(/\r\n/g, "\n").split("\n");
    const paragraphs = lines.map((t) => ({ text: t.trim(), images: [] })).filter((p) => p.text);
    return parseQuestionsFromParagraphs(paragraphs);
  }

  async function parseQuizFile(file) {
    const ext = (file.name.split(".").pop() || "").toLowerCase();
    const buf = await file.arrayBuffer();
    let qs = [];
    if (ext === "docx") {
      const { paragraphs } = await parseDocxFull(buf);
      qs = parseQuestionsFromParagraphs(paragraphs);
      try {
        sessionStorage.setItem(QUESTIONS_FULL_KEY, JSON.stringify(qs));
      } catch (_) {}
    } else if (ext === "txt") {
      qs = parseQuestionsFromText(new TextDecoder("utf-8").decode(buf));
    } else {
      throw new Error("请上传 .docx 或 .txt 格式题库");
    }
    if (!qs.length) {
      throw new Error("未识别到题目，请检查 Word 是否含「一、单项选择题」等大标题及「1.」题号");
    }
    return qs;
  }

  function renderQuestionContent(q, stemElId, mediaElId, briefElId, subIdx) {
    const stemEl = document.getElementById(stemElId);
    const mediaEl = mediaElId ? document.getElementById(mediaElId) : null;
    if (stemEl) {
      stemEl.innerHTML = buildQuestionDisplayHtml(q, subIdx != null ? subIdx : quizSubIndex);
    }
    if (mediaEl) {
      const extra = (q.images || []).filter((img) => {
        const html = getAllDisplayedHtml(q, subIdx != null ? subIdx : quizSubIndex);
        return html.indexOf(img.src) < 0;
      });
      if (extra.length) {
        mediaEl.innerHTML = extra
          .map((img) => '<img src="' + img.src + '" alt="题目配图" />')
          .join("");
        mediaEl.classList.remove("hidden");
      } else {
        mediaEl.innerHTML = "";
        mediaEl.classList.add("hidden");
      }
    }
    if (briefElId) {
      const brief = document.getElementById(briefElId);
      if (brief) {
        const preview = (q.fullContent || q.stem).slice(0, 120);
        brief.textContent = "【" + (TYPE_LABELS[q.type] || "") + "】" + preview + (preview.length >= 120 ? "…" : "");
      }
    }
  }

  function findStudentTeam(name) {
    for (const g of groups) {
      if (g.members.includes(name)) return { tid: String(g.id), teamName: g.name };
    }
    return { tid: "", teamName: "" };
  }

  function getAllStudentsFlat() {
    const list = [];
    groups.forEach((g) => {
      g.members.forEach((name) => {
        list.push({ name, tid: String(g.id), teamName: g.name });
      });
    });
    return list;
  }

  function updateQuizBadge() {
    document.getElementById("quizCountBadge").textContent =
      questions.length ? `已导入 · ${questions.length} 题` : "未导入 · 0 题";
    const canStart = groups.length && questions.length;
    document.getElementById("btnStartQuiz").disabled = !canStart || quizActive;
    document.getElementById("btnEndQuiz").disabled = !quizActive;
  }

  function updateQuizUI() {
    const empty = document.getElementById("quizEmpty");
    const qArea = document.getElementById("quizQuestionArea");
    const pArea = document.getElementById("quizPickArea");
    if (!groups.length || !questions.length) {
      empty.classList.remove("hidden");
      qArea.classList.add("hidden");
      pArea.classList.add("hidden");
      return;
    }
    empty.classList.add("hidden");
    if (!quizActive) {
      qArea.classList.add("hidden");
      pArea.classList.add("hidden");
      return;
    }
    showCurrentQuestion();
  }

  function startQuiz() {
    if (!groups.length || !questions.length) {
      showToast("请先分组并导入题库");
      return;
    }
    if (questions.some((q) => q.imageCount && !(q.images && q.images.length))) {
      showToast("提示：若刚刷新页面，请重新导入 Word 以显示配图");
    }
    quizActive = true;
    quizIndex = 0;
    quizSubIndex = 0;
    answerRecords = [];
    saveAnswers();
    updateQuizBadge();
    document.getElementById("quizQuestionArea").classList.remove("hidden");
    document.getElementById("quizPickArea").classList.add("hidden");
    document.getElementById("btnNextQuestion").classList.add("hidden");
    showCurrentQuestion();
    saveState();
    showToast("答题开始");
  }

  function showCurrentQuestion() {
    if (!quizActive || quizIndex >= questions.length) return;
    const q = questions[quizIndex];
    let progressText = `第 ${quizIndex + 1} / ${questions.length} 题`;
    if (q.subQuestions && q.subQuestions.length) {
      const sub = q.subQuestions[quizSubIndex];
      progressText += ` · 小问 ${quizSubIndex + 1}/${q.subQuestions.length}`;
      if (sub) progressText += " " + sub.label;
    }
    document.getElementById("quizProgress").textContent = progressText;
    const secEl = document.getElementById("quizSectionLabel");
    if (q.section) {
      secEl.textContent = q.section;
      secEl.classList.remove("hidden");
    } else {
      secEl.classList.add("hidden");
    }
    document.getElementById("quizTypeTag").textContent = questionHasSubParts(q)
      ? "简答题 · 分小问作答"
      : TYPE_LABELS[q.type] || q.type;
    document.getElementById("quizStem").innerHTML = buildQuestionDisplayHtml(q, quizSubIndex);
    renderQuizExtraImages(q, quizSubIndex);
    document.getElementById("quizQuestionArea").classList.remove("hidden");
    document.getElementById("quizPickArea").classList.add("hidden");
    setQuizTimerVisible(true);
  }

  function setQuizTimerVisible(show) {
    const bar = document.getElementById("quizTimerBar");
    if (bar) bar.classList.toggle("hidden", !show);
  }

  function showPickStudents() {
    const q = questions[quizIndex];
    setQuizTimerVisible(true);
    document.getElementById("quizQuestionArea").classList.add("hidden");
    document.getElementById("quizPickArea").classList.remove("hidden");
    document.getElementById("btnNextQuestion").classList.add("hidden");
    const pickSub = document.getElementById("pickSubQuestion");
    const pickHint = document.getElementById("pickHint");
    if (q.subQuestions && q.subQuestions.length) {
      const sub = q.subQuestions[quizSubIndex];
      pickSub.classList.remove("hidden");
      pickSub.innerHTML = buildPickSubHtml(q, quizSubIndex);
      pickHint.textContent =
        "第 " +
        (quizSubIndex + 1) +
        "/" +
        q.subQuestions.length +
        " 小问 " +
        sub.label +
        "：点击学生作答";
    } else {
      pickSub.classList.add("hidden");
      pickSub.innerHTML = "";
      pickHint.textContent = "点击学生姓名进入答题，或使用随机抽人";
    }
    const grid = document.getElementById("quizStudentGrid");
    grid.innerHTML = "";
    getAllStudentsFlat().forEach((s) => {
      const btn = document.createElement("button");
      btn.type = "button";
      btn.className = "quiz-stu-btn";
      btn.innerHTML = `${escapeHtml(s.name)}<span class="team-label">${escapeHtml(s.teamName)}</span>`;
      btn.addEventListener("click", () => openAnswerModal(s));
      grid.appendChild(btn);
    });
  }

  function pickRandomStudent() {
    const list = getAllStudentsFlat();
    if (!list.length) return;
    openAnswerModal(list[Math.floor(Math.random() * list.length)]);
  }

  function openAnswerModal(student) {
    if (!quizActive || quizIndex >= questions.length) return;
    currentAnswerStudent = student;
    selectedOptions = [];
    const q = questions[quizIndex];
    const sub = getCurrentSubQuestion(q);
    let title = `答题 · ${student.name}（${student.teamName}）`;
    if (sub) title += ` · 小问 ${sub.label}`;
    document.getElementById("answerModalTitle").textContent = title;
    const briefEl = document.getElementById("answerQuestionBrief");
    if (briefEl) {
      const tag = questionHasSubParts(q)
        ? "【简答题】"
        : "【" + (TYPE_LABELS[q.type] || "") + "】";
      let html = buildAnswerBriefHtml(q, quizSubIndex);
      if (html.length > 1500) html = html.slice(0, 1500) + "…";
      briefEl.innerHTML =
        '<span class="hint">' +
        escapeHtml(tag) +
        (sub ? " · 小问 " + escapeHtml(sub.label) : "") +
        '</span><div class="answer-brief" style="max-height:280px;overflow:auto;margin-top:8px">' +
        html +
        "</div>";
    }
    const choiceArea = document.getElementById("answerChoiceArea");
    const textArea = document.getElementById("answerTextArea");
    choiceArea.innerHTML = "";
    document.getElementById("answerText").value = "";
    document.getElementById("voiceStatus").textContent = "";

    if (
      !questionHasSubParts(q) &&
      (q.type === "single" || q.type === "multiple")
    ) {
      choiceArea.classList.remove("hidden");
      textArea.classList.add("hidden");
      const opts = getOptionsForQuestion(q);
      if (!opts.length) {
        choiceArea.innerHTML =
          '<p class="hint">未解析到选项，请检查 Word 中 A. B. C. D. 格式</p>';
      } else {
      opts.forEach((o) => {
        const btn = document.createElement("button");
        btn.type = "button";
        btn.className = "answer-opt-btn";
        btn.textContent = o.text ? o.key + ". " + o.text : o.key;
        btn.dataset.key = o.key;
        btn.addEventListener("click", (e) => {
          e.preventDefault();
          e.stopPropagation();
          if (q.type === "single") {
            choiceArea.querySelectorAll(".answer-opt-btn").forEach((b) => b.classList.remove("selected"));
            btn.classList.add("selected");
            selectedOptions = [o.key];
          } else {
            btn.classList.toggle("selected");
            selectedOptions = [...choiceArea.querySelectorAll(".answer-opt-btn.selected")].map(
              (b) => b.dataset.key
            );
          }
        });
        choiceArea.appendChild(btn);
      });
      }
    } else {
      choiceArea.classList.add("hidden");
      textArea.classList.remove("hidden");
    }
    document.getElementById("overlayAnswer").classList.add("show");
  }

  function initSpeech() {
    const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
    if (!SR) return null;
    const rec = new SR();
    rec.lang = "zh-CN";
    rec.continuous = true;
    rec.interimResults = true;
    return rec;
  }

  function startVoiceInput() {
    if (!speechRec) speechRec = initSpeech();
    if (!speechRec) {
      showToast("当前浏览器不支持语音输入，请手动输入");
      return;
    }
    const ta = document.getElementById("answerText");
    const status = document.getElementById("voiceStatus");
    status.textContent = "正在聆听…";
    speechRec.onresult = (e) => {
      let t = "";
      for (let i = e.resultIndex; i < e.results.length; i++) {
        t += e.results[i][0].transcript;
      }
      if (e.results[e.results.length - 1].isFinal) {
        ta.value = (ta.value ? ta.value + " " : "") + t;
      }
    };
    speechRec.onerror = () => {
      status.textContent = "语音识别中断，可重试或手动修改";
    };
    speechRec.onend = () => {
      status.textContent = "语音识别结束，可手动修改后提交";
    };
    try {
      speechRec.start();
    } catch (_) {
      status.textContent = "请允许麦克风权限后重试";
    }
  }

  function stopVoice() {
    if (speechRec) try { speechRec.stop(); } catch (_) {}
  }

  function submitAnswer() {
    if (!currentAnswerStudent || quizIndex >= questions.length) return;
    const q = questions[quizIndex];
    let answer = "";
    if (
      !questionHasSubParts(q) &&
      (q.type === "single" || q.type === "multiple")
    ) {
      if (!selectedOptions.length) {
        showToast("请点击选择选项");
        return;
      }
      answer = selectedOptions.sort().join("");
    } else {
      answer = document.getElementById("answerText").value.trim();
      if (!answer) {
        showToast("请输入或语音录入回答");
        return;
      }
    }
    stopVoice();
    const { name, tid, teamName } = currentAnswerStudent;
    const sub = getCurrentSubQuestion(q);
    const record = {
      questionIndex: quizIndex,
      questionId: q.id,
      questionType: q.type,
      questionSection: q.section || "",
      questionStem: sub ? sub.stem : q.stem,
      questionFullContent: q.fullContent || q.stem,
      questionOptions: q.options || [],
      subQuestionLabel: sub ? sub.label : "",
      subQuestionStem: sub ? sub.stem : "",
      standardAnswer: q.answer || "",
      studentName: name,
      teamId: tid,
      teamName,
      answer,
      timestamp: new Date().toISOString()
    };
    answerRecords.push(record);
    saveAnswers();
    addPoint(tid, name);
    document.getElementById("overlayAnswer").classList.remove("show");
    currentAnswerStudent = null;
    showToast(`${name} 已记录回答并 +1 分`);
    saveState();

    if (q.subQuestions && q.subQuestions.length && quizSubIndex < q.subQuestions.length - 1) {
      quizSubIndex++;
      showPickStudents();
      return;
    }
    document.getElementById("btnNextQuestion").classList.remove("hidden");
    document.getElementById("quizPickArea").classList.remove("hidden");
  }

  function nextQuestion() {
    quizIndex++;
    quizSubIndex = 0;
    document.getElementById("btnNextQuestion").classList.add("hidden");
    if (quizIndex >= questions.length) {
      finishQuiz();
      return;
    }
    showCurrentQuestion();
    saveState();
  }

  function finishQuiz() {
    quizActive = false;
    setQuizTimerVisible(false);
    stopTimer();
    updateQuizBadge();
    saveState();
    renderQuizResultModal();
  }

  function renderQuizResultModal() {
    const teams = getTeamRankList();
    const individuals = getIndividualRankList();
    const mvp = individuals[0];
    let html = "<h3 style='font-size:20px;margin-bottom:12px;color:var(--primary-dark)'>小组排行榜</h3>";
    html += '<table class="rank-table"><thead><tr><th>名次</th><th>小组</th><th>得分</th></tr></thead><tbody>';
    teams.forEach((t, i) => {
      html += `<tr><td>${i + 1}</td><td>${escapeHtml(t.name)}</td><td>${t.score}</td></tr>`;
    });
    html += "</tbody></table>";
    html += "<h3 style='font-size:20px;margin:24px 0 12px;color:var(--primary-dark)'>个人排行榜</h3>";
    html += '<table class="rank-table"><thead><tr><th>名次</th><th>姓名</th><th>小组</th><th>得分</th></tr></thead><tbody>';
    individuals.forEach((p, i) => {
      html += `<tr><td>${i + 1}</td><td>${escapeHtml(p.name)}</td><td>${escapeHtml(p.team)}</td><td>${p.score}</td></tr>`;
    });
    html += "</tbody></table>";
    if (mvp) {
      html += `<div class="mvp-box"><h3>🏆 MVP</h3><div class="mvp-name">${escapeHtml(mvp.name)}</div>
        <div class="mvp-score">${escapeHtml(mvp.team)} · ${mvp.score} 分</div></div>`;
    }
    html += `<p class="hint" style="margin-top:16px">共记录 ${answerRecords.length} 条答题数据（已存本地）</p>`;
    document.getElementById("quizResultBody").innerHTML = html;
    document.getElementById("overlayQuizResult").classList.add("show");
    updateQuizUI();
  }

  function buildFullAnalysisPayload() {
    const byStudent = {};
    answerRecords.forEach((r) => {
      if (!byStudent[r.studentName]) byStudent[r.studentName] = [];
      byStudent[r.studentName].push({
        题号: r.questionId,
        大题分类: r.questionSection || "",
        题型: TYPE_LABELS[r.questionType] || r.questionType,
        题干摘要: r.questionStem,
        题目全文: r.questionFullContent || r.questionStem,
        选项: r.questionOptions || [],
        参考答案: r.standardAnswer || "未提供",
        学生回答: r.answer,
        小组: r.teamName
      });
    });
    return {
      题库: questions.map((q) => ({
        题号: q.id,
        大题分类: q.section || "",
        题型: TYPE_LABELS[q.type] || q.type,
        题干: q.stem,
        题目全文: q.fullContent || q.stem,
        选项: q.options || [],
        参考答案: q.answer || "未提供",
        含配图: !!(q.images && q.images.length)
      })),
      学生答题记录: byStudent,
      参与学生数: Object.keys(byStudent).length,
      答题总次数: answerRecords.length
    };
  }

  function markdownToHtml(md) {
    let html = escapeHtml(md);
    html = html.replace(/^### (.+)$/gm, "<h4>$1</h4>");
    html = html.replace(/^## (.+)$/gm, "<h3>$1</h3>");
    html = html.replace(/^# (.+)$/gm, "<h3>$1</h3>");
    html = html.replace(/\*\*(.+?)\*\*/g, "<strong>$1</strong>");
    html = html.replace(/\n\n/g, "</p><p>");
    html = html.replace(/\n/g, "<br>");
    return "<p>" + html + "</p>";
  }

  async function callDeepSeekProfiles() {
    if (!ensureApiKey()) return;
    const apiKey = getApiKey();
    if (!answerRecords.length) {
      showToast("暂无答题记录可分析");
      return;
    }
    if (!questions.length) {
      showToast("缺少题库数据，请重新导入 Word");
      return;
    }
    const body = document.getElementById("profileBody");
    body.innerHTML = '<p class="loading-text">正在将题库与答题数据发送至 DeepSeek 分析，请稍候…</p>';
    document.getElementById("overlayProfile").classList.add("show");

    const dataJson = JSON.stringify(buildFullAnalysisPayload(), null, 2);
    const prompt = `你是一位资深教研员与课堂评价专家。以下 JSON 包含「完整题库」与「学生答题记录」，请基于全部题目与学生实际作答进行综合分析。

请严格按以下三个部分输出（使用 Markdown 标题 ##）：

## 一、学生参与积极性评价分析
（按学生逐一简评，含未答题学生的参与情况推断；结合答题次数、主动性、表达长度等）

## 二、学生知识掌握程度评价分析
（结合题目考查点、参考答案与学生答案的匹配度；区分选择题与开放性题目；指出典型正确与偏差）

## 三、知识点分析与教学策略建议
（归纳本节课涉及的核心知识点、薄弱点、易错点；提出 3–5 条可操作的课堂教学改进建议）

要求：内容具体、可落地，使用中文，面向教师阅读。不要输出 JSON。

数据：
${dataJson}`;

    try {
      const res = await fetch("https://api.deepseek.com/chat/completions", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: "Bearer " + apiKey
        },
        body: JSON.stringify({
          model: "deepseek-chat",
          messages: [
            {
              role: "system",
              content:
                "你是课堂教学数据分析专家，擅长从题库与课堂互动答题记录中生成结构化教学分析报告。"
            },
            { role: "user", content: prompt }
          ],
          temperature: 0.6,
          max_tokens: 4096
        })
      });
      if (!res.ok) {
        const errText = await res.text();
        throw new Error(res.status + " " + errText.slice(0, 200));
      }
      const data = await res.json();
      const content =
        data.choices && data.choices[0] && data.choices[0].message
          ? data.choices[0].message.content
          : "";
      renderAnalysisReport(content);
      try {
        sessionStorage.setItem("classroom_report_v1", content);
      } catch (_) {}
    } catch (err) {
      body.innerHTML =
        '<p class="hint" style="color:#c62828">分析失败：' +
        escapeHtml(String(err.message)) +
        '</p><p class="hint" style="margin-top:12px">请检查 API Key、网络与 DeepSeek 余额。若浏览器拦截跨域，请用本地 HTTP 服务打开本页。可「导出答题记录」后手动分析。密钥仅存于当前标签页，不会写入本地文件。</p>';
    }
  }

  function renderAnalysisReport(content) {
    const body = document.getElementById("profileBody");
    if (!content || !content.trim()) {
      body.innerHTML = '<p class="hint">未生成报告内容</p>';
      return;
    }
    body.innerHTML = markdownToHtml(content);
  }

  function exportAnswers() {
    const payload = {
      导出时间: new Date().toISOString(),
      题库摘要: questionsForStorage(),
      答题记录: answerRecords
    };
    const blob = new Blob([JSON.stringify(payload, null, 2)], {
      type: "application/json"
    });
    const a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = `答题记录_${new Date().toISOString().slice(0, 10)}.json`;
    a.click();
    URL.revokeObjectURL(a.href);
    showToast("答题记录已导出");
  }

  // ========== 排名与结算 ==========
  function getTeamRankList() {
    return groups
      .map((g) => ({
        id: g.id,
        name: g.name,
        score: teamScores[String(g.id)] || 0,
        members: g.members
      }))
      .sort((a, b) => b.score - a.score || a.id - b.id);
  }

  function getIndividualRankList() {
    const list = [];
    groups.forEach((g) => {
      const tid = String(g.id);
      g.members.forEach((name) => {
        list.push({
          name,
          team: g.name,
          score: (memberScores[tid] && memberScores[tid][name]) || 0
        });
      });
    });
    return list.sort((a, b) => b.score - a.score || a.name.localeCompare(b.name, "zh"));
  }

  function renderSettleModal() {
    const teams = getTeamRankList();
    const individuals = getIndividualRankList();
    const topTeam = teams[0];
    const mvp = individuals[0];

    let html = "<h3 style='font-size:20px;margin-bottom:12px;color:var(--primary-dark)'>小组得分排名</h3>";
    html += '<table class="rank-table"><thead><tr><th>名次</th><th>小组</th><th>得分</th></tr></thead><tbody>';
    teams.forEach((t, i) => {
      html += `<tr><td>${i + 1}</td><td>${escapeHtml(t.name)}</td><td>${t.score}</td></tr>`;
    });
    html += "</tbody></table>";

    html += "<h3 style='font-size:20px;margin:24px 0 12px;color:var(--primary-dark)'>个人得分排名</h3>";
    html += '<table class="rank-table"><thead><tr><th>名次</th><th>姓名</th><th>所属小组</th><th>得分</th></tr></thead><tbody>';
    individuals.forEach((p, i) => {
      html += `<tr><td>${i + 1}</td><td>${escapeHtml(p.name)}</td><td>${escapeHtml(p.team)}</td><td>${p.score}</td></tr>`;
    });
    html += "</tbody></table>";

    if (mvp && mvp.score > 0) {
      html += `<div class="mvp-box">
        <h3>🏆 本课 MVP</h3>
        <div class="mvp-name">${escapeHtml(mvp.name)}</div>
        <div class="mvp-score">${escapeHtml(mvp.team)} · 个人 ${mvp.score} 分</div>
      </div>`;
    } else if (topTeam) {
      html += `<div class="mvp-box">
        <h3>🏆 优胜小组</h3>
        <div class="mvp-name">${escapeHtml(topTeam.name)}</div>
        <div class="mvp-score">小组 ${topTeam.score} 分</div>
      </div>`;
    } else {
      html += '<p class="hint" style="margin-top:16px">暂无得分记录</p>';
    }

    document.getElementById("settleBody").innerHTML = html;
    document.getElementById("overlaySettle").classList.add("show");
  }

  // ========== 计时器 ==========
  let timerId = null;
  let timerRemain = 0;
  let timerRunning = false;
  let timerMode = "countdown";

  function formatTime(sec) {
    const m = Math.floor(sec / 60);
    const s = sec % 60;
    return `${String(m).padStart(2, "0")}:${String(s).padStart(2, "0")}`;
  }

  function updateTimerDisplay() {
    document.getElementById("timerDisplay").textContent = formatTime(Math.max(0, timerRemain));
  }

  function getInputSeconds() {
    const min = parseInt(document.getElementById("timerMin").value, 10) || 0;
    const sec = parseInt(document.getElementById("timerSec").value, 10) || 0;
    return min * 60 + sec;
  }

  function stopTimer() {
    if (timerId) clearInterval(timerId);
    timerId = null;
    timerRunning = false;
  }

  function startTimer() {
    stopTimer();
    timerMode = document.getElementById("timerMode").value;
    if (timerMode === "countdown") {
      if (timerRemain <= 0) timerRemain = getInputSeconds();
    } else {
      if (timerRemain <= 0) timerRemain = 0;
    }
    timerRunning = true;
    timerId = setInterval(() => {
      if (timerMode === "countdown") {
        timerRemain--;
        if (timerRemain <= 0) {
          timerRemain = 0;
          updateTimerDisplay();
          stopTimer();
          showToast("时间到！");
          try {
            const ctx = new (window.AudioContext || window.webkitAudioContext)();
            const o = ctx.createOscillator();
            const g = ctx.createGain();
            o.connect(g);
            g.connect(ctx.destination);
            o.frequency.value = 880;
            g.gain.value = 0.15;
            o.start();
            setTimeout(() => o.stop(), 400);
          } catch (_) {}
          return;
        }
      } else {
        timerRemain++;
      }
      updateTimerDisplay();
    }, 1000);
    updateTimerDisplay();
  }

  function resetTimer() {
    stopTimer();
    timerMode = document.getElementById("timerMode").value;
    timerRemain = timerMode === "countdown" ? getInputSeconds() : 0;
    updateTimerDisplay();
  }

  // ========== 事件绑定 ==========
  document.querySelectorAll(".tab-btn").forEach((btn) => {
    btn.addEventListener("click", () => {
      document.querySelectorAll(".tab-btn").forEach((b) => b.classList.remove("active"));
      document.querySelectorAll(".panel").forEach((p) => p.classList.remove("active"));
      btn.classList.add("active");
      document.getElementById("panel-" + btn.dataset.tab).classList.add("active");
    });
  });

  document.getElementById("fileInput").addEventListener("change", async (e) => {
    const file = e.target.files[0];
    if (!file) return;
    try {
      const names = await parseExcelFile(file);
      if (!names.length) {
        showToast("未读取到有效姓名，请检查 Excel 第一列");
        return;
      }
      students = names;
      groups = [];
      teamScores = {};
      memberScores = {};
      undoStack = [];
      updateStudentBadge();
      renderGroups();
      renderScorePanel();
      saveState();
      showToast(`成功导入 ${students.length} 名学生`);
    } catch (err) {
      showToast("导入失败：" + (err.message || "请检查文件格式"));
    }
    e.target.value = "";
  });

  document.getElementById("btnRandomGroup").addEventListener("click", () => {
    if (!students.length) {
      showToast("请先上传班级名单");
      return;
    }
    const groupCount = parseInt(document.getElementById("groupCount").value, 10) || 4;
    const maxPerGroup = parseInt(document.getElementById("maxPerGroup").value, 10) || 6;
    const res = randomGroup(students, groupCount, maxPerGroup);
    if (res.error) {
      showToast(res.error);
      return;
    }
    groups = res.groups;
    teamScores = {};
    memberScores = {};
    undoStack = [];
    syncScoresStructure();
    renderGroups();
    renderScorePanel();
    saveState();
    showToast("随机分组完成，可切换到「课堂答题」或「小组加分」");
    updateQuizBadge();
    updateQuizUI();
  });

  document.getElementById("btnUndo").addEventListener("click", undoLast);
  document.getElementById("btnSettle").addEventListener("click", renderSettleModal);

  document.getElementById("quizFileInput").addEventListener("change", async (e) => {
    const file = e.target.files[0];
    if (!file) return;
    try {
      questions = await parseQuizFile(file);
      updateQuizBadge();
      updateQuizUI();
      saveState();
      const subCnt = questions.reduce(
        (n, q) => n + (q.subQuestions && q.subQuestions.length ? q.subQuestions.length : 0),
        0
      );
      const imgCnt = questions.reduce(
        (n, q) => n + ((q.images && q.images.length) || 0),
        0
      );
      let msg = `成功导入 ${questions.length} 道题目`;
      if (subCnt) msg += `，简答小问共 ${subCnt} 个`;
      if (imgCnt) msg += `，配图 ${imgCnt} 张`;
      showToast(msg);
    } catch (err) {
      showToast("题库导入失败：" + (err.message || ""));
    }
    e.target.value = "";
  });

  document.getElementById("btnConfigApiKey").addEventListener("click", () => {
    const inp = document.getElementById("apiKeyInput");
    inp.value = getApiKey() || "";
    document.getElementById("overlayApiKey").classList.add("show");
  });

  document.getElementById("btnConfirmApiKey").addEventListener("click", () => {
    const key = document.getElementById("apiKeyInput").value.trim();
    if (!key) {
      showToast("请输入 API Key");
      return;
    }
    setApiKey(key);
    document.getElementById("apiKeyInput").value = "";
    document.getElementById("overlayApiKey").classList.remove("show");
    showToast("API Key 已载入（仅当前页面会话）");
  });

  document.getElementById("btnClearApiKey").addEventListener("click", clearApiKey);

  document.getElementById("btnStartQuiz").addEventListener("click", startQuiz);
  document.getElementById("btnEndQuiz").addEventListener("click", finishQuiz);
  document.getElementById("btnReadyPick").addEventListener("click", () => {
    quizSubIndex = 0;
    showPickStudents();
  });
  document.getElementById("btnRandomStudent").addEventListener("click", pickRandomStudent);
  document.getElementById("btnNextQuestion").addEventListener("click", nextQuestion);
  document.getElementById("btnSubmitAnswer").addEventListener("click", submitAnswer);
  document.getElementById("btnVoiceInput").addEventListener("click", startVoiceInput);
  document.getElementById("btnGenProfile").addEventListener("click", () => {
    if (ensureApiKey()) callDeepSeekProfiles();
  });
  document.getElementById("btnExportAnswers").addEventListener("click", exportAnswers);
  document.querySelectorAll(".modal-close").forEach((btn) => {
    btn.addEventListener("click", () => {
      if (btn.dataset.close === "overlayAnswer") stopVoice();
      document.getElementById(btn.dataset.close).classList.remove("show");
    });
  });

  document.querySelectorAll(".overlay").forEach((ov) => {
    ov.addEventListener("click", (e) => {
      if (e.target === ov) {
        if (ov.id === "overlayAnswer") stopVoice();
        ov.classList.remove("show");
      }
    });
  });

  document.getElementById("btnTimerStart").addEventListener("click", startTimer);
  document.getElementById("btnTimerPause").addEventListener("click", stopTimer);
  document.getElementById("btnTimerReset").addEventListener("click", resetTimer);

  document.getElementById("timerMode").addEventListener("change", resetTimer);

  document.querySelectorAll(".quiz-timer-preset").forEach((btn) => {
    btn.addEventListener("click", () => {
      const sec = parseInt(btn.dataset.preset, 10);
      document.getElementById("timerMin").value = Math.floor(sec / 60);
      document.getElementById("timerSec").value = sec % 60;
      document.getElementById("timerMode").value = "countdown";
      timerRemain = sec;
      stopTimer();
      updateTimerDisplay();
    });
  });

  initApiKey();
  loadState();
  updateQuizBadge();
  resetTimer();
})();
  </script>
</body>
</html>
