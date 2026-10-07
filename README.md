<!DOCTYPE html>
<html lang="ko" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ChemKinetics Lab - 화학 반응속도론 미적분 시각화 & 개념 탐구</title>
  
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            navy: {
              900: '#0b132b',
              800: '#0f172a',
              700: '#1c2541'
            },
            slatepanel: '#1e293b',
            amberneon: '#fbbf24',
            amberglow: 'rgba(251, 191, 36, 0.25)'
          }
        }
      }
    }
  </script>

  <!-- Chart.js CDN -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <!-- MathJax CDN for LaTeX rendering -->
  <script>
    window.MathJax = {
      tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']],
        displayMath: [['$$', '$$'], ['\\[', '\\]']]
      },
      svg: { fontCache: 'global' },
      startup: {
        typeset: true
      }
    };
  </script>
  <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

  <style>
    body {
      background-color: #0b132b;
      color: #f8fafc;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    }
    
    .bg-panel {
      background-color: #1e293b;
    }
    
    .bg-panel-hover:hover {
      background-color: #334155;
    }

    .border-glow {
      box-shadow: 0 0 15px rgba(251, 191, 36, 0.2);
    }

    .border-glow-active {
      box-shadow: 0 0 20px rgba(251, 191, 36, 0.4);
      border-color: #fbbf24 !important;
    }

    /* Custom scrollbar styling */
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: #0b132b;
    }
    ::-webkit-scrollbar-thumb {
      background: #334155;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #475569;
    }
  </style>
</head>
<body class="min-h-screen flex flex-col antialiased selection:bg-amber-400 selection:text-slate-900">

  <!-- [상단 공통 Header] -->
  <header class="bg-navy-800 border-b border-slate-700 sticky top-0 z-50 shadow-md">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
      <!-- 로고 및 홈 이동 -->
      <div class="flex items-center space-x-3 cursor-pointer group" onclick="navigateTo('home')">
        <span class="text-2xl sm:text-3xl transform group-hover:scale-110 transition">🧪</span>
        <span class="text-xl sm:text-2xl font-black tracking-wider text-amberneon">
          ChemKinetics <span class="text-slate-100 font-light">Lab</span>
        </span>
      </div>

      <!-- 네비게이션 버튼 목록 (PC) -->
      <nav class="hidden lg:flex space-x-1">
        <button onclick="navigateTo('concepts')" id="nav-concepts" class="px-3 py-2 rounded-lg text-xs font-semibold transition text-slate-300 hover:text-white hover:bg-slate-700">
          💡 핵심 개념 백과
        </button>
        <button onclick="navigateTo('screen2')" id="nav-screen2" class="px-3 py-2 rounded-lg text-xs font-semibold transition text-slate-300 hover:text-white hover:bg-slate-700">
          📈 차수/미분 시각화
        </button>
        <button onclick="navigateTo('screen3')" id="nav-screen3" class="px-3 py-2 rounded-lg text-xs font-semibold transition text-slate-300 hover:text-white hover:bg-slate-700">
          🧮 수치대입 & 반감기
        </button>
        <button onclick="navigateTo('screen4')" id="nav-screen4" class="px-3 py-2 rounded-lg text-xs font-semibold transition text-slate-300 hover:text-white hover:bg-slate-700">
          ⚡ QSSA & 분자 반응
        </button>
        <button onclick="navigateTo('screen5')" id="nav-screen5" class="px-3 py-2 rounded-lg text-xs font-semibold transition text-slate-300 hover:text-white hover:bg-slate-700">
          📝 상세 수식유도 노트
        </button>
      </nav>

      <!-- Mobile/Tablet navigation menu dropdown button -->
      <div class="lg:hidden">
        <select onchange="navigateTo(this.value); this.value='';" class="bg-slate-800 text-amberneon border border-slate-600 rounded-lg p-2 text-xs font-bold focus:outline-none">
          <option value="" disabled selected>📜 메뉴 선택...</option>
          <option value="home">🏠 홈 화면</option>
          <option value="concepts">💡 핵심 개념 백과</option>
          <option value="screen2">📈 1. 차수/미분 시각화</option>
          <option value="screen3">🧮 2. 수치대입 & 반감기</option>
          <option value="screen4">⚡ 3. QSSA & 분자 반응</option>
          <option value="screen5">📝 4. 상세 수식유도 노트</option>
        </select>
      </div>
    </div>
  </header>

  <!-- 메인 컨텐츠 영역 -->
  <main class="flex-grow max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8">

    <!-- [공통 상단 홈으로 돌아가기 버튼] (화면 2~5에서 표시) -->
    <div id="back-to-home-container" class="hidden mb-6">
      <button onclick="navigateTo('home')" class="w-full sm:w-auto flex items-center justify-center space-x-2 bg-slate-800 hover:bg-slate-700 border border-amberneon text-amberneon font-bold py-2.5 px-5 rounded-xl border-glow transition transform hover:-translate-y-0.5 text-sm">
        <span class="text-lg">⬅️</span>
        <span>처음 화면으로 돌아가기</span>
      </button>
    </div>

    <!-- ==================== [화면 1: 홈 랜딩] ==================== -->
    <section id="screen-home" class="space-y-8">
      <div class="text-center space-y-4 py-8">
        <div class="inline-block px-4 py-1.5 bg-amber-400/10 border border-amber-400/30 rounded-full text-amberneon text-xs font-bold uppercase tracking-widest mb-2">
          Calculus & Chemical Kinetics Visualizer
        </div>
        <h1 class="text-3xl sm:text-5xl font-extrabold text-white tracking-tight">
          미적분으로 푸는 <span class="text-amberneon">화학 반응 속도론</span>
        </h1>
        <p class="text-slate-400 text-sm sm:text-base max-w-2xl mx-auto leading-relaxed">
          반응 차수별 농도 변화, 순간 반응속도 접선 기울기, 수치 대입 반감기 계산, 분자 충돌 시각화 및 정류상태 근사(QSSA) 유도 과정을 통합 탐구합니다.
        </p>
      </div>

      <!-- 5개 탐구 카드 Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        
        <!-- 개념 백과 카드 -->
        <div onclick="navigateTo('concepts')" class="bg-panel bg-panel-hover p-6 rounded-xl border border-amberneon/40 hover:border-amberneon cursor-pointer transition duration-300 transform hover:-translate-y-1 shadow-xl flex flex-col justify-between group">
          <div>
            <div class="flex items-center space-x-3 mb-4">
              <span class="text-3xl p-3 bg-slate-900/80 rounded-xl group-hover:scale-110 transition">💡</span>
              <h2 class="text-xl font-bold text-white group-hover:text-amberneon transition">핵심 개념 사전</h2>
            </div>
            <p class="text-slate-400 text-sm leading-relaxed mb-4">
              순간 반응속도, 반응 차수, 속도 상수($k$), 반감기, 정류상태 근사의 물리적·수학적 의미를 카드로 체계적으로 정리합니다.
            </p>
          </div>
          <div class="text-amberneon font-semibold text-sm flex items-center space-x-2 pt-2 border-t border-slate-700/50">
            <span>개념 학습하기</span>
            <span class="transform group-hover:translate-x-1 transition">→</span>
          </div>
        </div>

        <!-- 카드 1 -->
        <div onclick="navigateTo('screen2')" class="bg-panel bg-panel-hover p-6 rounded-xl border border-slate-700 hover:border-amberneon cursor-pointer transition duration-300 transform hover:-translate-y-1 shadow-xl flex flex-col justify-between group">
          <div>
            <div class="flex items-center space-x-3 mb-4">
              <span class="text-3xl p-3 bg-slate-900/80 rounded-xl group-hover:scale-110 transition">📈</span>
              <h2 class="text-xl font-bold text-white group-hover:text-amberneon transition">반응 차수 & 접선 기울기</h2>
            </div>
            <p class="text-slate-400 text-sm leading-relaxed mb-4">
              0차, 1차, 2차 반응의 $[A]-t$ 농도 그래프를 확인하고, 호버 위치에서 순간 반응속도 접선 기울기($-\frac{d[A]}{dt}$)를 그려봅니다.
            </p>
          </div>
          <div class="text-amberneon font-semibold text-sm flex items-center space-x-2 pt-2 border-t border-slate-700/50">
            <span>미분 시각화 시작</span>
            <span class="transform group-hover:translate-x-1 transition">→</span>
          </div>
        </div>

        <!-- 카드 2 -->
        <div onclick="navigateTo('screen3')" class="bg-panel bg-panel-hover p-6 rounded-xl border border-slate-700 hover:border-amberneon cursor-pointer transition duration-300 transform hover:-translate-y-1 shadow-xl flex flex-col justify-between group">
          <div>
            <div class="flex items-center space-x-3 mb-4">
              <span class="text-3xl p-3 bg-slate-900/80 rounded-xl group-hover:scale-110 transition">🧮</span>
              <h2 class="text-xl font-bold text-white group-hover:text-amberneon transition">수치대입 & 직접 입력</h2>
            </div>
            <p class="text-slate-400 text-sm leading-relaxed mb-4">
              슬라이더 및 **키보드 직접 입력**으로 초기농도($[A]_0$), 속도상수($k$), 관찰시간($t$)을 원하는 값으로 정확히 계산합니다.
            </p>
          </div>
          <div class="text-amberneon font-semibold text-sm flex items-center space-x-2 pt-2 border-t border-slate-700/50">
            <span>수치 계산기 열기</span>
            <span class="transform group-hover:translate-x-1 transition">→</span>
          </div>
        </div>

        <!-- 카드 3 -->
        <div onclick="navigateTo('screen4')" class="bg-panel bg-panel-hover p-6 rounded-xl border border-slate-700 hover:border-amberneon cursor-pointer transition duration-300 transform hover:-translate-y-1 shadow-xl flex flex-col justify-between group">
          <div>
            <div class="flex items-center space-x-3 mb-4">
              <span class="text-3xl p-3 bg-slate-900/80 rounded-xl group-hover:scale-110 transition">⚡</span>
              <h2 class="text-xl font-bold text-white group-hover:text-amberneon transition">QSSA & 분자 충돌 시각화</h2>
            </div>
            <p class="text-slate-400 text-sm leading-relaxed mb-4">
              Euler 수치해석 그래프와 함께 실시간 **2D 분자 충돌 캔버스 시각화**로 $A \rightarrow I \rightarrow P$ 입자의 변화 현상을 직접 관찰합니다.
            </p>
          </div>
          <div class="text-amberneon font-semibold text-sm flex items-center space-x-2 pt-2 border-t border-slate-700/50">
            <span>분자 시각화 보기</span>
            <span class="transform group-hover:translate-x-1 transition">→</span>
          </div>
        </div>

        <!-- 카드 4 -->
        <div onclick="navigateTo('screen5')" class="bg-panel bg-panel-hover p-6 rounded-xl border border-slate-700 hover:border-amberneon cursor-pointer transition duration-300 transform hover:-translate-y-1 shadow-xl flex flex-col justify-between group md:col-span-2 lg:col-span-2">
          <div>
            <div class="flex items-center space-x-3 mb-4">
              <span class="text-3xl p-3 bg-slate-900/80 rounded-xl group-hover:scale-110 transition">📝</span>
              <h2 class="text-xl font-bold text-white group-hover:text-amberneon transition">상세 미적분 수식 유도 노트</h2>
            </div>
            <p class="text-slate-400 text-sm leading-relaxed mb-4">
              미분 정의부터 변수분리, 정적분 영역 설정, 로그 공식, 직선화 그래프 개형, 적분인자(Integrating Factor)를 이용한 엄밀한 QSSA 완전해 유도까지 처음부터 완벽하게 다룹니다.
            </p>
          </div>
          <div class="text-amberneon font-semibold text-sm flex items-center space-x-2 pt-2 border-t border-slate-700/50">
            <span>수식 유도 전체보기</span>
            <span class="transform group-hover:translate-x-1 transition">→</span>
          </div>
        </div>

      </div>
    </section>

    <!-- ==================== [화면: 핵심 개념 사전] ==================== -->
    <section id="screen-concepts" class="hidden space-y-6">
      <div class="bg-panel p-6 rounded-xl border border-slate-700 shadow-xl space-y-6">
        <div>
          <h2 class="text-2xl font-bold text-amberneon mb-2">💡 화학 반응속도론 & 미적분 핵심 개념 백과</h2>
          <p class="text-slate-400 text-sm">
            시뮬레이션을 이해하기 위한 필수 기초 용어와 미적분학적 원리를 체계적으로 설명합니다.
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          
          <!-- 개념 카드 1 -->
          <div class="bg-navy-900 p-5 rounded-xl border border-slate-700 space-y-3">
            <div class="flex items-center space-x-3">
              <span class="text-2xl p-2 bg-amber-500/20 rounded-lg text-amberneon">1</span>
              <h3 class="text-lg font-bold text-white">순간 반응 속도와 미분 ($-\frac{d[A]}{dt}$)</h3>
            </div>
            <p class="text-slate-300 text-sm leading-relaxed">
              화학 반응 속도는 단위 시간당 반응물 농도의 감소량 또는 생성물 농도의 증가량입니다.
              반응물 $[A]$의 농도는 시간에 따라 줄어들므로 변화율 $\frac{\Delta [A]}{\Delta t}$는 음수 값을 갖습니다.
              시간 간격을 극한 $\lim_{\Delta t \to 0}$으로 보내면 **시간 $t$에서의 순간 반응속도 $v = -\frac{d[A]}{dt}$** 가 유도되며, 이는 $[A]-t$ 농도 그래프 상에서의 **접선의 기울기**와 정확히 일치합니다.
            </p>
          </div>

          <!-- 개념 카드 2 -->
          <div class="bg-navy-900 p-5 rounded-xl border border-slate-700 space-y-3">
            <div class="flex items-center space-x-3">
              <span class="text-2xl p-2 bg-amber-500/20 rounded-lg text-amberneon">2</span>
              <h3 class="text-lg font-bold text-white">반응 차수 (Reaction Order) 와 속도 상수 ($k$)</h3>
            </div>
            <p class="text-slate-300 text-sm leading-relaxed">
              속도 법칙 $v = k [A]^n$ 에서 **지수 $n$** 을 반응 차수라고 부릅니다.
            </p>
            <ul class="text-xs text-slate-300 space-y-1.5 list-disc list-inside bg-slate-800/60 p-3 rounded-lg font-mono">
              <li><strong class="text-amberneon">0차 ($n=0$):</strong> 속도가 농도에 무관하게 일정함 ($v = k$)</li>
              <li><strong class="text-amberneon">1차 ($n=1$):</strong> 속도가 농도에 비례함 ($v = k[A]$)</li>
              <li><strong class="text-amberneon">2차 ($n=2$):</strong> 속도가 농도의 제곱에 비례함 ($v = k[A]^2$)</li>
            </ul>
            <p class="text-xs text-slate-400">속도 상수 $k$는 온도와 촉매에 따라 결정되는 고유한 상수입니다.</p>
          </div>

          <!-- 개념 카드 3 -->
          <div class="bg-navy-900 p-5 rounded-xl border border-slate-700 space-y-3">
            <div class="flex items-center space-x-3">
              <span class="text-2xl p-2 bg-amber-500/20 rounded-lg text-amberneon">3</span>
              <h3 class="text-lg font-bold text-white">적분 속도식 (Integrated Rate Law)</h3>
            </div>
            <p class="text-slate-300 text-sm leading-relaxed">
              미분 속도식은 순간 속도를 보여주지만, 특정 시간 $t$후에 남아있는 농도를 직접 알기 어렵습니다.
              따라서 **변수분리법(Separation of Variables)** 및 정적분을 적용하여 **시간 $t$와 농도 $[A]_t$ 간의 직접적인 관계식**인 적분 속도식을 도출합니다.
            </p>
            <p class="text-xs text-sky-300 bg-slate-800/60 p-2.5 rounded-lg">
              이를 변형하면 각각 차수별로 직선 그래프 ($[A]$ vs $t$, $\ln[A]$ vs $t$, $1/[A]$ vs $t$)를 얻을 수 있습니다.
            </p>
          </div>

          <!-- 개념 카드 4 -->
          <div class="bg-navy-900 p-5 rounded-xl border border-slate-700 space-y-3">
            <div class="flex items-center space-x-3">
              <span class="text-2xl p-2 bg-amber-500/20 rounded-lg text-amberneon">4</span>
              <h3 class="text-lg font-bold text-white">반감기 ($t_{1/2}$) 의 물리적 의미</h3>
            </div>
            <p class="text-slate-300 text-sm leading-relaxed">
              반응물의 초기 농도가 절반($[A]_t = \frac{1}{2}[A]_0$)으로 줄어드는 데 걸리는 시간입니다.
            </p>
            <ul class="text-xs text-slate-300 space-y-1.5 list-disc list-inside bg-slate-800/60 p-3 rounded-lg font-mono">
              <li><strong class="text-amberneon">0차:</strong> $t_{1/2} = \frac{[A]_0}{2k}$ (초기농도에 비례)</li>
              <li><strong class="text-amberneon">1차:</strong> $t_{1/2} = \frac{\ln 2}{k} \approx \frac{0.693}{k}$ (초기농도와 무관, 일정함)</li>
              <li><strong class="text-amberneon">2차:</strong> $t_{1/2} = \frac{1}{k[A]_0}$ (초기농도에 반비례)</li>
            </ul>
          </div>

          <!-- 개념 카드 5 -->
          <div class="bg-navy-900 p-5 rounded-xl border border-slate-700 space-y-3 md:col-span-2">
            <div class="flex items-center space-x-3">
              <span class="text-2xl p-2 bg-amber-500/20 rounded-lg text-amberneon">5</span>
              <h3 class="text-lg font-bold text-white">다단계 메커니즘과 정류상태 근사 (QSSA)</h3>
            </div>
            <p class="text-slate-300 text-sm leading-relaxed">
              대부분의 복잡한 화학 반응은 한 번에 일어나지 않고 연속적인 다단계 반응($A \xrightarrow{k_1} I \xrightarrow{k_2} P$)을 거칩니다.
              여기서 intermediate 생성물 $[I]$는 매우 불안정하여 생성되는 즉시 소멸합니다.
            </p>
            <p class="text-slate-300 text-sm leading-relaxed">
              소멸 속도가 생성 속도보다 훨씬 빠른 조건($k_2 \gg k_1$)에서는 반응 진행 중 중간체의 농도 변화율이 거의 0에 수렴한다는 가정 **$\frac{d[I]}{dt} \approx 0$** 을 세울 수 있으며, 이를 **정류상태 근사(Quasi-Steady-State Approximation, QSSA)** 라고 부릅니다.
            </p>
          </div>

        </div>
      </div>
    </section>

    <!-- ==================== [화면 2: 차수/미분] ==================== -->
    <section id="screen-screen2" class="hidden space-y-6">
      <div class="bg-panel p-6 rounded-xl border border-slate-700 shadow-xl space-y-6">
        <div>
          <h2 class="text-2xl font-bold text-amberneon mb-2">반응 차수별 [A]-t 곡선 및 순간 반응속도(접선 기울기)</h2>
          <p class="text-slate-400 text-sm">
            원하는 반응 차수를 선택하세요. 그래프 위로 마우스를 호버하면 선택 시점 $t$에서의 **순간 반응속도(접선 기울기 $-\frac{d[A]}{dt}$)** 가 그래프 상에 직접 그려집니다.
          </p>
        </div>

        <!-- 반응 차수 선택 버튼 그룹 -->
        <div class="flex flex-wrap gap-3">
          <button onclick="setOrderScreen2(0)" id="btn-order2-0" class="px-5 py-2.5 rounded-xl font-bold border transition duration-200 bg-amberneon text-slate-900 border-amberneon border-glow-active">0차 반응</button>
          <button onclick="setOrderScreen2(1)" id="btn-order2-1" class="px-5 py-2.5 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700">1차 반응</button>
          <button onclick="setOrderScreen2(2)" id="btn-order2-2" class="px-5 py-2.5 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700">2차 반응</button>
        </div>

        <!-- 호버 실시간 수치 데이터 패널 -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 bg-navy-900 p-4 rounded-xl border border-slate-700">
          <div class="p-2">
            <span class="text-xs text-slate-400 uppercase font-bold tracking-wider block mb-1">현재 시간 ($t$)</span>
            <span id="screen2-hover-t" class="text-xl sm:text-2xl font-black text-white">그래프에 마우스를 올리세요</span>
          </div>
          <div class="p-2 border-t sm:border-t-0 sm:border-l border-slate-800">
            <span class="text-xs text-slate-400 uppercase font-bold tracking-wider block mb-1">현재 농도 ($[A]$)</span>
            <span id="screen2-hover-conc" class="text-xl sm:text-2xl font-black text-slate-200">-</span>
          </div>
          <div class="p-2 border-t sm:border-t-0 sm:border-l border-slate-800">
            <span class="text-xs text-slate-400 uppercase font-bold tracking-wider block mb-1">순간 속도 ($-\frac{d[A]}{dt}$)</span>
            <span id="screen2-hover-rate" class="text-xl sm:text-2xl font-black text-amberneon">-</span>
          </div>
        </div>

        <!-- Chart.js 캔버스 래퍼 -->
        <div class="relative h-96 w-full bg-navy-900 p-4 rounded-xl border border-slate-800 shadow-inner">
          <canvas id="chartScreen2"></canvas>
        </div>
      </div>
    </section>

    <!-- ==================== [화면 3: 수치대입] ==================== -->
    <section id="screen-screen3" class="hidden space-y-6">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <!-- Input 제어 패널 -->
        <div class="bg-panel p-6 rounded-xl border border-slate-700 shadow-xl space-y-5">
          <h2 class="text-xl font-bold text-amberneon border-b border-slate-700 pb-3 flex items-center space-x-2">
            <span>⚙️</span>
            <span>반응 변수 수치 대입 패널</span>
          </h2>

          <div>
            <label class="block text-xs font-bold uppercase text-slate-300 mb-2">반응 차수 선택</label>
            <select id="screen3-order" onchange="calculateScreen3('slider')" class="w-full bg-navy-900 border border-slate-600 rounded-lg p-3 text-white font-semibold focus:outline-none focus:border-amberneon">
              <option value="0">0차 반응 ([A]_t = [A]_0 - kt)</option>
              <option value="1" selected>1차 반응 ([A]_t = [A]_0 · e^-kt)</option>
              <option value="2">2차 반응 (1/[A]_t = 1/[A]_0 + kt)</option>
            </select>
          </div>

          <!-- 초기 농도 [A]0 입력 및 슬라이더 동기화 -->
          <div class="space-y-1.5">
            <div class="flex justify-between items-center text-xs font-bold uppercase text-slate-300">
              <label for="screen3-a0-input">초기 농도 ($[A]_0$, M):</label>
              <input type="number" id="screen3-a0-input" min="0.01" max="10.0" step="0.01" value="1.00" oninput="calculateScreen3('text')" class="w-24 bg-navy-900 border border-slate-600 focus:border-amberneon rounded text-amberneon font-mono text-right p-1 font-bold">
            </div>
            <input type="range" id="screen3-a0" min="0.1" max="5.0" step="0.05" value="1.0" oninput="calculateScreen3('slider')" class="w-full accent-amber-400 cursor-pointer">
          </div>

          <!-- 속도 상수 k 입력 및 슬라이더 동기화 -->
          <div class="space-y-1.5">
            <div class="flex justify-between items-center text-xs font-bold uppercase text-slate-300">
              <label for="screen3-k-input">속도 상수 ($k$):</label>
              <input type="number" id="screen3-k-input" min="0.001" max="5.0" step="0.01" value="0.10" oninput="calculateScreen3('text')" class="w-24 bg-navy-900 border border-slate-600 focus:border-amberneon rounded text-amberneon font-mono text-right p-1 font-bold">
            </div>
            <input type="range" id="screen3-k" min="0.01" max="1.0" step="0.01" value="0.1" oninput="calculateScreen3('slider')" class="w-full accent-amber-400 cursor-pointer">
          </div>

          <!-- 관찰 시간 t 입력 및 슬라이더 동기화 -->
          <div class="space-y-1.5">
            <div class="flex justify-between items-center text-xs font-bold uppercase text-slate-300">
              <label for="screen3-t-input">관찰 시간 ($t$, 초):</label>
              <input type="number" id="screen3-t-input" min="0" max="100" step="0.1" value="5.00" oninput="calculateScreen3('text')" class="w-24 bg-navy-900 border border-slate-600 focus:border-amberneon rounded text-amberneon font-mono text-right p-1 font-bold">
            </div>
            <input type="range" id="screen3-t" min="0" max="30" step="0.2" value="5.0" oninput="calculateScreen3('slider')" class="w-full accent-amber-400 cursor-pointer">
          </div>
          
          <p class="text-xs text-slate-400 pt-2 border-t border-slate-700">
            💡 <strong>Tip:</strong> 슬라이더를 조절하거나 박스 안의 숫자를 키보드로 직접 수정할 수 있습니다.
          </p>
        </div>

        <!-- 결과 카드 및 그래프 -->
        <div class="lg:col-span-2 space-y-6">
          <!-- 수치 결과 출력 카드 -->
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
            <!-- 반감기 카드 -->
            <div class="bg-panel p-5 rounded-xl border border-slate-700 border-l-4 border-l-amberneon shadow-lg">
              <span class="text-xs font-bold text-slate-400 uppercase tracking-wider block mb-1">계산된 반감기 ($t_{1/2}$)</span>
              <span id="screen3-res-thalf" class="text-3xl font-black text-amberneon">-</span>
              <span class="text-xs text-slate-400 block mt-2 font-mono" id="screen3-thalf-formula">공식: ln(2) / k</span>
            </div>

            <!-- 특정 시간 농도 카드 -->
            <div class="bg-panel p-5 rounded-xl border border-slate-700 border-l-4 border-l-sky-500 shadow-lg">
              <span class="text-xs font-bold text-slate-400 uppercase tracking-wider block mb-1">시간 $t$ 일 때 남은 농도 ($[A]_t$)</span>
              <span id="screen3-res-at" class="text-3xl font-black text-sky-400">-</span>
              <span class="text-xs text-slate-400 block mt-2">초기 농도의 <span id="screen3-res-percent" class="text-white font-bold">-</span>%</span>
            </div>
          </div>

          <!-- 변화 곡선 차트 -->
          <div class="bg-panel p-6 rounded-xl border border-slate-700 shadow-xl space-y-4">
            <h3 class="text-lg font-bold text-white">시간에 따른 반응물 농도 감쇄 곡선</h3>
            <div class="relative h-72 w-full bg-navy-900 p-4 rounded-xl border border-slate-800 shadow-inner">
              <canvas id="chartScreen3"></canvas>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ==================== [화면 4: QSSA & 분자 시각화] ==================== -->
    <section id="screen-screen4" class="hidden space-y-6">
      <div class="bg-panel p-6 rounded-xl border border-slate-700 shadow-xl space-y-6">
        <div>
          <h2 class="text-2xl font-bold text-amberneon mb-2">QSSA (정류상태 근사) 반응 & 2D 분자 동역학 시각화</h2>
          <p class="text-slate-400 text-sm leading-relaxed">
            다단계 연속 반응 $A \xrightarrow{k_1} I \xrightarrow{k_2} P$ 의 반응 농도 곡선과 캔버스 안에서 충돌 변환하는 분자 입자들의 실제 움직임을 관찰합니다.
            $k_2 \gg k_1$ 조건이 성립할 때 노란색 중간체 $[I]$ 입자가 축적되지 않고 곧바로 생성물 $[P]$로 전환되는 정류상태를 직접 확인하세요.
          </p>
        </div>

        <!-- 슬라이더 조절바 -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6 bg-navy-900 p-5 rounded-xl border border-slate-700">
          <div>
            <div class="flex justify-between items-center mb-2">
              <label class="text-xs font-bold uppercase text-slate-300">속도 상수 $k_1$ ($A \rightarrow I$):</label>
              <span id="val-k1" class="text-amberneon font-black font-mono text-base">0.10</span>
            </div>
            <input type="range" id="slider-k1" min="0.01" max="0.5" step="0.01" value="0.10" oninput="updateQSSA()" class="w-full accent-amber-400 cursor-pointer">
          </div>

          <div>
            <div class="flex justify-between items-center mb-2">
              <label class="text-xs font-bold uppercase text-slate-300">속도 상수 $k_2$ ($I \rightarrow P$):</label>
              <span id="val-k2" class="text-amberneon font-black font-mono text-base">1.50</span>
            </div>
            <input type="range" id="slider-k2" min="0.01" max="3.0" step="0.05" value="1.50" oninput="updateQSSA()" class="w-full accent-amber-400 cursor-pointer">
          </div>
        </div>

        <!-- QSSA 성립 여부 진단 상태 박스 -->
        <div id="qssa-status-box" class="p-4 rounded-xl border transition-all duration-300 bg-slate-800 border-slate-600">
          <div class="flex items-center space-x-3">
            <span id="qssa-status-icon" class="text-3xl">⚠️</span>
            <div>
              <h4 id="qssa-status-title" class="font-bold text-white text-base">QSSA 상태 진단 중...</h4>
              <p id="qssa-status-desc" class="text-xs text-slate-300 mt-0.5">k1 및 k2의 비율을 조절해 보세요.</p>
            </div>
          </div>
        </div>

        <!-- 분자 충돌 시각화 + Euler 차트 Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
          
          <!-- 2D Particle Visualizer Canvas Card -->
          <div class="bg-navy-900 p-4 rounded-xl border border-slate-800 space-y-3">
            <div class="flex justify-between items-center">
              <div class="flex items-center space-x-2">
                <span class="text-lg">⚛️</span>
                <h3 class="font-bold text-white text-sm">실시간 2D 분자 충돌 시각화</h3>
              </div>
              <button onclick="resetParticleSim()" class="px-3 py-1 bg-slate-800 hover:bg-slate-700 text-amberneon text-xs font-bold rounded-lg border border-amberneon/40 transition">
                🔄 시뮬레이션 리셋
              </button>
            </div>

            <!-- Particle Canvas Container -->
            <div class="relative h-72 w-full bg-slate-950 rounded-xl overflow-hidden border border-slate-800">
              <canvas id="particleCanvas" class="w-full h-full block"></canvas>
            </div>

            <!-- Particle Legend -->
            <div class="flex justify-around text-xs font-bold pt-1">
              <div class="flex items-center space-x-1.5">
                <span class="w-3 h-3 rounded-full bg-red-500 inline-block"></span>
                <span class="text-slate-300">반응물 [A]</span>
              </div>
              <div class="flex items-center space-x-1.5">
                <span class="w-3 h-3 rounded-full bg-amber-400 inline-block"></span>
                <span class="text-slate-300">중간체 [I]</span>
              </div>
              <div class="flex items-center space-x-1.5">
                <span class="w-3 h-3 rounded-full bg-emerald-400 inline-block"></span>
                <span class="text-slate-300">생성물 [P]</span>
              </div>
            </div>
          </div>

          <!-- Euler Simulation Concentration Chart -->
          <div class="bg-navy-900 p-4 rounded-xl border border-slate-800 space-y-3">
            <div class="flex justify-between items-center">
              <div class="flex items-center space-x-2">
                <span class="text-lg">📊</span>
                <h3 class="font-bold text-white text-sm">수치해석 농도 변화 그래프</h3>
              </div>
            </div>
            <div class="relative h-72 w-full">
              <canvas id="chartScreen4"></canvas>
            </div>
          </div>

        </div>
      </div>
    </section>

    <!-- ==================== [화면 5: 수식유도 노트] ==================== -->
    <section id="screen-screen5" class="hidden space-y-6">
      <div class="bg-panel p-6 rounded-xl border border-slate-700 shadow-xl space-y-6">
        <div>
          <div class="flex items-center space-x-3 mb-1">
            <span class="text-3xl">🎨</span>
            <h2 class="text-2xl font-bold text-amberneon">화학 반응속도론 인터랙티브 시각적 수식 유도 노트</h2>
          </div>
          <p class="text-slate-400 text-sm">
            기초 미분 정의부터 변수분리, 적분 영역 곡선 면적, 자연로그의 기하학적 탄생, 직선화 모핑 및 QSSA 정류상태까지 <strong>동적 그래픽 스텝퍼</strong>로 직관적으로 체득합니다.
          </p>
        </div>

        <!-- 탭 버튼 목록 -->
        <div class="flex flex-wrap gap-2 border-b border-slate-700 pb-4">
          <button onclick="setMathTab(0)" id="tab-math-0" class="px-4 py-2 rounded-xl font-bold border transition duration-200 bg-amberneon text-slate-900 border-amberneon border-glow-active">0차 반응 유도</button>
          <button onclick="setMathTab(1)" id="tab-math-1" class="px-4 py-2 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700">1차 반응 유도</button>
          <button onclick="setMathTab(2)" id="tab-math-2" class="px-4 py-2 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700">2차 반응 유도</button>
          <button onclick="setMathTab(3)" id="tab-math-3" class="px-4 py-2 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700">QSSA 엄밀한 수식 유도</button>
        </div>

        <!-- 시각적 수식 유도 스텝퍼 보드 -->
        <div class="bg-navy-900 border border-slate-700 rounded-xl p-5 space-y-4 shadow-2xl">
          <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 border-b border-slate-800 pb-3">
            <div>
              <span class="text-xs font-bold text-amberneon uppercase tracking-widest block">Interactive Visual Stepper</span>
              <h3 id="stepper-title" class="text-lg font-bold text-white">단계별 기하학 시각화</h3>
            </div>
            
            <!-- Step navigation control buttons -->
            <div class="flex items-center space-x-2">
              <button onclick="prevMathStep()" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-xs rounded-lg border border-slate-600 transition">
                ◀ 이전 단계
              </button>
              <span id="step-indicator" class="text-xs font-mono font-bold text-amberneon px-2 py-1 bg-slate-950 rounded-md border border-slate-800">
                1 / 5 단계
              </span>
              <button onclick="nextMathStep()" class="px-3 py-1.5 bg-amberneon hover:bg-amber-300 text-slate-900 font-bold text-xs rounded-lg transition shadow">
                다음 단계 ▶
              </button>
              <button onclick="toggleAutoPlayMath()" id="btn-math-play" class="px-3 py-1.5 bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs rounded-lg transition">
                ▶️ 자동 재생
              </button>
            </div>
          </div>

          <!-- Step Indicators Bar -->
          <div class="grid grid-cols-5 gap-1.5" id="step-pills-container">
            <!-- Dynamic JS injection for step pills -->
          </div>

          <!-- Canvas + Explanation Grid -->
          <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-center">
            <!-- Left: Visual Canvas Diagram -->
            <div class="lg:col-span-7 bg-slate-950 p-4 rounded-xl border border-slate-800 relative min-h-[300px] flex items-center justify-center">
              <canvas id="mathDerivationCanvas" class="w-full h-full block rounded-lg"></canvas>
            </div>

            <!-- Right: Dynamic LaTeX Step Equation Card -->
            <div class="lg:col-span-5 bg-slate-800/80 p-5 rounded-xl border border-slate-700 space-y-3 min-h-[300px] flex flex-col justify-between">
              <div id="math-step-explanation" class="space-y-3">
                <!-- Dynamic text injection -->
              </div>
              <div class="p-3 bg-navy-900/90 rounded-lg border border-amberneon/30 text-xs text-amber-300">
                💡 <strong>시각적 키포인트:</strong> <span id="math-step-keypoint">그래픽 요소를 확인해 보세요.</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Dynamic Detailed MathJax Content Card -->
        <div id="math-content" class="bg-navy-900 p-6 rounded-xl border border-slate-800 min-h-[400px] shadow-inner text-slate-200">
          <!-- Dynamic JS Injection -->
        </div>
      </div>
    </section>

  </main>

  <!-- [하단 Footer] -->
  <footer class="bg-navy-800 border-t border-slate-800 py-6 text-center text-xs text-slate-500">
    <p>ChemKinetics Lab &copy; 2026. Designed for Advanced Chemistry & Calculus Education.</p>
  </footer>

  <!-- ==================== JavaScript Core App Engine ==================== -->
  <script>
    let chart2 = null, chart3 = null, chart4 = null;
    let order2 = 0;
    const defaultA0 = 1.0, defaultK = 0.1;

    let particles = [];
    const NUM_PARTICLES = 50;
    let particleAnimId = null;

    let currentMathTab = 0;
    let currentMathStep = 0;
    let mathAutoPlayTimer = null;

    // Visual Derivation Step Models Data
    const visualDerivationSteps = [
      // 0차 반응 visual steps
      [
        {
          title: "1/5단계: 미분 반응속도 정의와 미분 삼각형",
          keypoint: "시간 $dt$ 동안 농도가 $d[A]$ 만큼 변화하는 순간 접선 기울기가 곧 순간속도 $v = k$ 입니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">미분 반응속도 정의 ($v = -\\frac{d[A]}{dt}$)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              반응물 $[A]$의 농도가 감소하므로 변화율은 음수입니다. 극소 시간 $dt$에 대한 미분 삼각형을 시각적으로 나타냅니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon text-sm">
              $$ v = -\\frac{d[A]}{dt} = k [A]^0 = k $$
            </div>
          `
        },
        {
          title: "2/5단계: 변수분리법 ($d[A] = -k dt$)",
          keypoint: "농도 축 $d[A]$ 와 시간 축 $dt$ 를 독립된 변수로 좌우 변으로 분리합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">변수분리 (Separation of Variables)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              양변에 $dt$를 곱하여 농도 미분항과 시간 미분항으로 분리합니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-sm">
              $$ d[A] = -k \, dt $$
            </div>
          `
        },
        {
          title: "3/5단계: 정적분 영역 한계 설정 ($[A]_0 \\rightarrow [A]_t$)",
          keypoint: "속도 $k$ 곡선 하부의 사각형 적분 면적이 총 농도 감소량 $\\Delta [A]$ 와 일치합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">정적분 영역 설정</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              시간 $t=0 \\to t$ 범위 동안 적분합니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-sm">
              $$ \\int_{[A]_0}^{[A]_t} d[A] = -k \\int_{0}^{t} dt $$
            </div>
          `
        },
        {
          title: "4/5단계: 적분 속도식 유도 및 직선 그래프",
          keypoint: "기울기 $-k$ 와 $y$절편 $[A]_0$ 을 갖는 명확한 1차 직선 방정식이 형성됩니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">적분 속도식 (Integrated Law)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              적분을 계산하면 시간에 따라 일정하게 농도가 직선 감소함을 알 수 있습니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-base">
              $$ [A]_t = -kt + [A]_0 $$
            </div>
          `
        },
        {
          title: "5/5단계: 반감기 ($t_{1/2}$) 기하학적 지점",
          keypoint: "초기농도가 줄어들면 절반으로 도달하는 시간(반감기)도 비례하여 짧아집니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">반감기 ($t_{1/2} = \\frac{[A]_0}{2k}$)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              $[A]_t = \\frac{[A]_0}{2}$ 되는 시점을 계산합니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-sm">
              $$ t_{1/2} = \\frac{[A]_0}{2k} $$
            </div>
          `
        }
      ],
      // 1차 반응 visual steps
      [
        {
          title: "1/5단계: 미분 속도식과 농도에 가변하는 접선 기울기",
          keypoint: "농도가 높을수록 접선의 경사(순간 속도)가 급격하며, 농도가 낮아질수록 기울기가 완만해집니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">1차 미분 속도식 ($v = k[A]$)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              순간 속도가 남아있는 반응물 농도 $[A]$에 직접 비례합니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon text-sm">
              $$ -\\frac{d[A]}{dt} = k [A] $$
            </div>
          `
        },
        {
          title: "2/5단계: 변수분리 $\\frac{1}{[A]} d[A] = -k dt$",
          keypoint: "농도 변수 $[A]$를 좌변 분모로 배치하여 적분 준비 상태로 만듭니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">변수분리 (Separation)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              농도항을 좌변으로, 시간항을 우변으로 분리합니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-sm">
              $$ \\frac{1}{[A]} d[A] = -k \, dt $$
            </div>
          `
        },
        {
          title: "3/5단계: 쌍곡선 $y = \\frac{1}{[A]}$ 적분과 자연로그 $\\ln$ 의 탄생",
          keypoint: "미적분학 핵심: $\\int \\frac{1}{x} dx = \\ln x$ 에 따라 쌍곡선 밑 면적이 자연로그 $\\ln$ 을 만들어냅니다!",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">자연로그 적분 (Logarithmic Integration)</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              $\\frac{1}{[A]}$ 곡선의 정적분은 자연로그 차이 $\\ln[A]_t - \\ln[A]_0$ 가 됩니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-sm">
              $$ \\ln[A]_t - \\ln[A]_0 = -kt $$
            </div>
          `
        },
        {
          title: "4/5단계: 지수함수 감쇠 곡선 및 $\\ln[A]$ 직선화 변환",
          keypoint: "지수 표현 $[A]_t = [A]_0 e^{-kt}$ 와 자연로그 $y$축 변환 $\\ln[A]$ 직선화를 동시 비교합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">적분 속도식 & 직선화</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-sm space-y-1">
              <div>$$ [A]_t = [A]_0 e^{-kt} $$</div>
              <div class="text-sky-300 text-xs">$$ \\ln[A]_t = -kt + \\ln[A]_0 $$</div>
            </div>
          `
        },
        {
          title: "5/5단계: 일정성 반감기 ($t_{1/2} = \\frac{\\ln 2}{k}$)",
          keypoint: "초기농도 $[A]_0$에 전혀 영향을 받지 않으므로, 몇 번을 반으로 줄이든 걸리는 시간 간격이 동일합니다!",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">1차 반감기 보편성</h4>
            <p class="text-xs text-slate-300 leading-relaxed mb-2">
              농도가 $1 \\to 1/2 \\to 1/4 \\to 1/8$ 로 줄어드는 시간 간격이 일정합니다.
            </p>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-sm">
              $$ t_{1/2} = \\frac{\\ln 2}{k} \\approx \\frac{0.693}{k} $$
            </div>
          `
        }
      ],
      // 2차 반응 visual steps
      [
        {
          title: "1/5단계: 미분 속도식 ($v = k[A]^2$) 및 초반 급감 곡선",
          keypoint: "반응 초기 농도가 클 때는 속도가 제곱으로 대단히 빠르나, 농도가 조금만 떨어져도 속도가 급격히 둔화됩니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">2차 미분 속도식</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon text-sm">
              $$ -\\frac{d[A]}{dt} = k [A]^2 $$
            </div>
          `
        },
        {
          title: "2/5단계: 변수분리 $[A]^{-2} d[A] = -k dt$",
          keypoint: "거듭제곱 형태로 정리하여 다항식 적분 공식 적용을 준비합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">변수분리 및 거듭제곱</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-sm">
              $$ \\frac{1}{[A]^2} d[A] = -k \, dt $$
            </div>
          `
        },
        {
          title: "3/5단계: 역수 다항식 적분 $\\int x^{-2} dx = -\\frac{1}{x}$",
          keypoint: "$[A]^{-2}$ 곡선 하부 면적 정적분을 통해 농도의 역수 $\\frac{1}{[A]}$ 관계식이 도출됩니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">역수 적분 계산</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-sm">
              $$ -\\frac{1}{[A]_t} - \\left(-\\frac{1}{[A]_0}\\right) = -kt $$
            </div>
          `
        },
        {
          title: "4/5단계: 역수 직선 그래프 (양의 기울기 $+k$)",
          keypoint: "농도 자체가 아닌 역수 $\\frac{1}{[A]}$를 $y$축으로 잡으면 우상향하는 직선이 만들어집니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">역수 적분 속도식</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-sm">
              $$ \\frac{1}{[A]_t} = kt + \\frac{1}{[A]_0} $$
            </div>
          `
        },
        {
          title: "5/5단계: 2배씩 늘어나는 반감기 ($t_{1/2} = \\frac{1}{k[A]_0}$)",
          keypoint: "농도가 절반으로 떨어질 때마다 다음 절반까지 걸리는 시간이 2배, 4배로 계속 길어집니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">2차 반응 반감기</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-sm">
              $$ t_{1/2} = \\frac{1}{k[A]_0} $$
            </div>
          `
        }
      ],
      // QSSA visual steps
      [
        {
          title: "1/5단계: 연속 반응 $A \\xrightarrow{k_1} I \\xrightarrow{k_2} P$ 의 반응 속도 연립식",
          keypoint: "중간체 $[I]$는 $A$로부터 생성됨 동시에 $P$로 소비되는 이중 경로를 가집니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">연속 반응 메커니즘</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon text-sm">
              $$ \\frac{d[I]}{dt} = k_1 [A] - k_2 [I] $$
            </div>
          `
        },
        {
          title: "2/5단계: 적분인자 $e^{k_2 t}$ 를 활용한 미분방정식 해법",
          keypoint: "적분인자 곱을 통해 $\\frac{d}{dt}([I]e^{k_2 t})$ 꼴로 묶어서 완전해 $[I](t)$를 유도합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">중간체 $[I](t)$ 엄밀한 완전해</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-slate-200 text-xs">
              $$ [I](t) = \\frac{k_1 [A]_0}{k_2 - k_1} \\left(e^{-k_1 t} - e^{-k_2 t}\\right) $$
            </div>
          `
        },
        {
          title: "3/5단계: 정류상태 근사 가정 (QSSA: $\\frac{d[I]}{dt} \\approx 0$)",
          keypoint: "$k_2 \\gg k_1$ 조건에서는 중간체가 생성되는 족족 사라져 농도가 매우 낮은 정류 상태를 유지합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">QSSA 정류상태 근사</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-amberneon font-bold text-sm">
              $$ k_1 [A] - k_2 [I]_{ss} \\approx 0 \\implies [I]_{ss} \\approx \\frac{k_1}{k_2} [A] $$
            </div>
          `
        },
        {
          title: "4/5단계: 율속 단계(RDS)와 최종 생성 속도 단순화",
          keypoint: "복잡한 다단계 반응이라도 가장 느린 단계($k_1$)가 전체 생성물 $P$의 속도를 지배합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">율속 단계 (Rate-Determining Step)</h4>
            <div class="p-3 bg-navy-900 rounded-lg text-center font-mono text-emerald-300 font-bold text-sm">
              $$ \\frac{d[P]}{dt} = k_2 [I]_{ss} = k_1 [A] $$
            </div>
          `
        },
        {
          title: "5/5단계: 수치해석 곡선과 QSSA 근사의 일치성 시각화",
          keypoint: "실제 미분방정식 수치해와 QSSA 근사식이 거의 완벽하게 겹쳐짐을 그래픽으로 확인합니다.",
          html: `
            <h4 class="font-bold text-amberneon text-base mb-1">QSSA 검증 완료</h4>
            <p class="text-xs text-slate-300">중간체 축적 최소화 시 수시 해석과 분석적 근사의 오차가 거의 0에 달합니다.</p>
          `
        }
      ]
    ];

    function navigateTo(screenId) {
      const screens = ['home', 'concepts', 'screen2', 'screen3', 'screen4', 'screen5'];
      screens.forEach(s => {
        const el = document.getElementById(`screen-${s}`);
        if (el) el.classList.add('hidden');
      });

      const backBtn = document.getElementById('back-to-home-container');
      if (backBtn) {
        if (screenId === 'home') backBtn.classList.add('hidden');
        else backBtn.classList.remove('hidden');
      }

      const target = document.getElementById(`screen-${screenId}`);
      if (target) target.classList.remove('hidden');

      screens.slice(1).forEach(s => {
        const navBtn = document.getElementById(`nav-${s}`);
        if (navBtn) {
          if (s === screenId) {
            navBtn.classList.add('bg-amberneon', 'text-slate-900', 'font-bold');
            navBtn.classList.remove('text-slate-300');
          } else {
            navBtn.classList.remove('bg-amberneon', 'text-slate-900', 'font-bold');
            navBtn.classList.add('text-slate-300');
          }
        }
      });

      window.scrollTo({ top: 0, behavior: 'smooth' });
      if (screenId === 'screen2') initChartScreen2();
      if (screenId === 'screen3') calculateScreen3('slider');
      if (screenId === 'screen4') {
        updateQSSA();
        initParticleSim();
      } else {
        stopParticleSim();
      }
      if (screenId === 'screen5') setMathTab(0);
    }

    function drawMathStepCanvas(tabIdx, stepIdx) {
      const canvas = document.getElementById('mathDerivationCanvas');
      if (!canvas) return;
      
      const parent = canvas.parentElement;
      canvas.width = parent.clientWidth || 400;
      canvas.height = 280;
      const ctx = canvas.getContext('2d');
      const w = canvas.width;
      const h = canvas.height;

      ctx.clearRect(0, 0, w, h);

      ctx.strokeStyle = '#1e293b';
      ctx.lineWidth = 1;
      for (let x = 40; x < w; x += 40) {
        ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, h); ctx.stroke();
      }
      for (let y = 30; y < h; y += 30) {
        ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(w, y); ctx.stroke();
      }

      const originX = 50;
      const originY = h - 40;
      const graphW = w - 80;
      const graphH = h - 70;

      ctx.strokeStyle = '#94a3b8';
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.moveTo(originX, 20);
      ctx.lineTo(originX, originY);
      ctx.lineTo(w - 20, originY);
      ctx.stroke();

      ctx.fillStyle = '#94a3b8';
      ctx.font = '11px sans-serif';
      ctx.fillText('시간 t →', w - 60, originY + 25);

      if (tabIdx === 0) {
        ctx.fillText('농도 [A]', originX - 40, 25);
        const y0 = originY - graphH * 0.85;
        const yEnd = originY - graphH * 0.15;

        ctx.strokeStyle = '#38bdf8';
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(originX, y0);
        ctx.lineTo(originX + graphW * 0.8, yEnd);
        ctx.stroke();

        if (stepIdx === 0) {
          const tx = originX + graphW * 0.35;
          const ty = y0 + (yEnd - y0) * (0.35 / 0.8);
          const dt = 60;
          const dy = (yEnd - y0) * (dt / (graphW * 0.8));

          ctx.strokeStyle = '#fbbf24';
          ctx.lineWidth = 2;
          ctx.setLineDash([4, 4]);
          ctx.beginPath();
          ctx.moveTo(tx, ty);
          ctx.lineTo(tx + dt, ty);
          ctx.lineTo(tx + dt, ty + dy);
          ctx.stroke();
          ctx.setLineDash([]);

          ctx.fillStyle = '#fbbf24';
          ctx.fillText('dt', tx + dt / 2 - 5, ty - 6);
          ctx.fillText('-d[A]', tx + dt + 6, ty + dy / 2);
          ctx.fillText('기울기 = -k', tx - 10, ty - 15);
        } else if (stepIdx === 1 || stepIdx === 2) {
          ctx.fillStyle = 'rgba(251, 191, 36, 0.25)';
          ctx.fillRect(originX, originY - graphH * 0.5, graphW * 0.6, graphH * 0.5);
          ctx.strokeStyle = '#fbbf24';
          ctx.strokeRect(originX, originY - graphH * 0.5, graphW * 0.6, graphH * 0.5);

          ctx.fillStyle = '#fbbf24';
          ctx.fillText('속도 v = k (일정)', originX + 20, originY - graphH * 0.5 - 8);
          ctx.fillText('면적 = k · t = 총 농도 변화량 Δ[A]', originX + 15, originY - graphH * 0.25);
        } else if (stepIdx === 3) {
          ctx.fillStyle = '#fbbf24';
          ctx.beginPath(); ctx.arc(originX, y0, 6, 0, Math.PI * 2); ctx.fill();
          ctx.fillText('y절편: [A]₀', originX + 10, y0 + 4);
          ctx.fillText('직선 기울기 m = -k', originX + graphW * 0.4, y0 + 60);
        } else if (stepIdx === 4) {
          const midY = (y0 + yEnd) / 2;
          const midX = originX + graphW * 0.4;

          ctx.strokeStyle = '#ef4444';
          ctx.setLineDash([4, 4]);
          ctx.beginPath();
          ctx.moveTo(originX, midY); ctx.lineTo(midX, midY); ctx.lineTo(midX, originY);
          ctx.stroke(); ctx.setLineDash([]);

          ctx.fillStyle = '#ef4444';
          ctx.fillText('[A]₀ / 2', originX - 40, midY + 4);
          ctx.fillText('t₁/₂ = [A]₀ / 2k', midX - 25, originY + 15);
        }

      } else if (tabIdx === 1) {
        ctx.fillText('농도 [A]', originX - 40, 25);

        ctx.strokeStyle = '#38bdf8';
        ctx.lineWidth = 3;
        ctx.beginPath();
        for (let x = 0; x <= graphW * 0.85; x += 3) {
          const tNorm = x / (graphW * 0.85);
          const yVal = Math.exp(-2.2 * tNorm);
          const px = originX + x;
          const py = originY - graphH * 0.85 * yVal;
          if (x === 0) ctx.moveTo(px, py);
          else ctx.lineTo(px, py);
        }
        ctx.stroke();

        if (stepIdx === 0) {
          [0.1, 0.4, 0.7].forEach((tn) => {
            const px = originX + graphW * 0.85 * tn;
            const py = originY - graphH * 0.85 * Math.exp(-2.2 * tn);
            ctx.fillStyle = '#fbbf24';
            ctx.beginPath(); ctx.arc(px, py, 4, 0, Math.PI * 2); ctx.fill();

            ctx.strokeStyle = '#fbbf24';
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.moveTo(px - 20, py - 20 * (1.8 * Math.exp(-2.2 * tn)));
            ctx.lineTo(px + 20, py + 20 * (1.8 * Math.exp(-2.2 * tn)));
            ctx.stroke();
          });
          ctx.fillText('농도가 줄어들수록 접선 기울기 완만해짐', originX + 30, 40);
        } else if (stepIdx === 2) {
          ctx.fillStyle = '#f59e0b';
          ctx.fillText('1/[A] 쌍곡선 하부 정적분 영역 → ln(자연로그)', originX + 20, 40);
          
          ctx.fillStyle = 'rgba(56, 189, 248, 0.25)';
          ctx.beginPath();
          const startX = originX + 30;
          const endX = originX + 160;
          ctx.moveTo(startX, originY);
          for (let x = startX; x <= endX; x += 2) {
            const relX = (x - originX) / 100 + 0.5;
            const y = originY - (graphH * 0.4) / relX;
            ctx.lineTo(x, y);
          }
          ctx.lineTo(endX, originY);
          ctx.closePath();
          ctx.fill();
        } else if (stepIdx === 3) {
          ctx.strokeStyle = '#10b981';
          ctx.lineWidth = 2.5;
          ctx.beginPath();
          ctx.moveTo(originX, originY - graphH * 0.8);
          ctx.lineTo(originX + graphW * 0.8, originY - graphH * 0.2);
          ctx.stroke();

          ctx.fillStyle = '#10b981';
          ctx.fillText('ln[A] vs t 직선 그래프 (기울기 = -k)', originX + 30, originY - graphH * 0.6);
        } else if (stepIdx === 4) {
          const h1 = graphW * 0.25;
          const h2 = graphW * 0.50;
          const h3 = graphW * 0.75;

          ctx.strokeStyle = '#fbbf24';
          ctx.setLineDash([3, 3]);
          [h1, h2, h3].forEach((hx) => {
            ctx.beginPath();
            ctx.moveTo(originX + hx, originY);
            ctx.lineTo(originX + hx, 40);
            ctx.stroke();
            ctx.fillStyle = '#fbbf24';
            ctx.fillText(`t₁/₂`, originX + hx - 10, originY + 15);
          });
          ctx.setLineDash([]);
          ctx.fillText('동일한 반감기 간격 유지!', originX + 40, 35);
        }

      } else if (tabIdx === 2) {
        ctx.fillText('농도 [A]', originX - 40, 25);

        if (stepIdx === 3 || stepIdx === 4) {
          ctx.strokeStyle = '#a855f7';
          ctx.lineWidth = 3;
          ctx.beginPath();
          ctx.moveTo(originX, originY - graphH * 0.2);
          ctx.lineTo(originX + graphW * 0.8, originY - graphH * 0.85);
          ctx.stroke();

          ctx.fillStyle = '#a855f7';
          ctx.fillText('y축: 1/[A] (우상향 직선, 기울기 = +k)', originX + 20, 40);
          ctx.fillText('절편: 1/[A]₀', originX - 35, originY - graphH * 0.2);
        } else {
          ctx.strokeStyle = '#ec4899';
          ctx.lineWidth = 3;
          ctx.beginPath();
          for (let x = 0; x <= graphW * 0.8; x += 3) {
            const tNorm = x / (graphW * 0.8);
            const yVal = 1 / (1 + 4 * tNorm);
            const px = originX + x;
            const py = originY - graphH * 0.85 * yVal;
            if (x === 0) ctx.moveTo(px, py);
            else ctx.lineTo(px, py);
          }
          ctx.stroke();
          ctx.fillStyle = '#ec4899';
          ctx.fillText('초반 급격한 농도 감소 곡선', originX + 30, 40);
        }

      } else if (tabIdx === 3) {
        ctx.fillText('농도', originX - 30, 25);

        const maxT = graphW * 0.8;
        ctx.lineWidth = 2.5;

        ctx.strokeStyle = '#ef4444';
        ctx.beginPath();
        for (let x = 0; x <= maxT; x += 3) {
          const tNorm = x / maxT;
          const y = Math.exp(-1.2 * tNorm);
          const px = originX + x;
          const py = originY - graphH * 0.8 * y;
          if (x === 0) ctx.moveTo(px, py); else ctx.lineTo(px, py);
        }
        ctx.stroke();

        ctx.strokeStyle = '#fbbf24';
        ctx.lineWidth = 3.5;
        ctx.beginPath();
        for (let x = 0; x <= maxT; x += 3) {
          const tNorm = x / maxT;
          const y = 0.12 * (Math.exp(-1.2 * tNorm) - Math.exp(-8 * tNorm));
          const px = originX + x;
          const py = originY - graphH * 0.8 * y;
          if (x === 0) ctx.moveTo(px, py); else ctx.lineTo(px, py);
        }
        ctx.stroke();

        ctx.strokeStyle = '#10b981';
        ctx.lineWidth = 2.5;
        ctx.beginPath();
        for (let x = 0; x <= maxT; x += 3) {
          const tNorm = x / maxT;
          const y = 1 - Math.exp(-1.2 * tNorm);
          const px = originX + x;
          const py = originY - graphH * 0.8 * y;
          if (x === 0) ctx.moveTo(px, py); else ctx.lineTo(px, py);
        }
        ctx.stroke();

        ctx.fillStyle = '#fbbf24';
        ctx.fillText('중간체 [I] 정류상태 (d[I]/dt ≈ 0 극소 평탄화)', originX + 40, originY - graphH * 0.25);
      }
    }

    function updateMathStepperUI() {
      const stepData = visualDerivationSteps[currentMathTab][currentMathStep];
      
      document.getElementById('stepper-title').innerText = stepData.title;
      document.getElementById('step-indicator').innerText = `${currentMathStep + 1} / 5 단계`;
      document.getElementById('math-step-explanation').innerHTML = stepData.html;
      document.getElementById('math-step-keypoint').innerText = stepData.keypoint;

      const pillsContainer = document.getElementById('step-pills-container');
      if (pillsContainer) {
        pillsContainer.innerHTML = [0, 1, 2, 3, 4].map(idx => `
          <button onclick="setMathStep(${idx})" class="h-2 rounded-full transition-all ${idx === currentMathStep ? 'bg-amberneon w-full' : 'bg-slate-700 hover:bg-slate-600 w-full'}"></button>
        `).join('');
      }

      if (window.MathJax && MathJax.typesetPromise) {
        MathJax.typesetPromise([document.getElementById('math-step-explanation')]).catch(err => console.error(err));
      }

      drawMathStepCanvas(currentMathTab, currentMathStep);
    }

    function setMathStep(stepIdx) {
      currentMathStep = stepIdx;
      updateMathStepperUI();
    }

    function prevMathStep() {
      currentMathStep = (currentMathStep - 1 + 5) % 5;
      updateMathStepperUI();
    }

    function nextMathStep() {
      currentMathStep = (currentMathStep + 1) % 5;
      updateMathStepperUI();
    }

    function toggleAutoPlayMath() {
      const btn = document.getElementById('btn-math-play');
      if (mathAutoPlayTimer) {
        clearInterval(mathAutoPlayTimer);
        mathAutoPlayTimer = null;
        btn.innerText = "▶️ 자동 재생";
        btn.className = "px-3 py-1.5 bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs rounded-lg transition";
      } else {
        btn.innerText = "⏸️ 일시 정지";
        btn.className = "px-3 py-1.5 bg-amber-600 hover:bg-amber-500 text-white font-bold text-xs rounded-lg transition";
        mathAutoPlayTimer = setInterval(() => {
          nextMathStep();
        }, 3000);
      }
    }

    function setMathTab(index) {
      currentMathTab = index;
      currentMathStep = 0;

      [0, 1, 2, 3].forEach(i => {
        const tab = document.getElementById(`tab-math-${i}`);
        if (i === index) {
          tab.className = "px-4 py-2 rounded-xl font-bold border transition duration-200 bg-amberneon text-slate-900 border-amberneon border-glow-active";
        } else {
          tab.className = "px-4 py-2 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700";
        }
      });

      const contentBox = document.getElementById('math-content');
      contentBox.innerHTML = mathNotes[index];

      if (window.MathJax && MathJax.typesetPromise) {
        MathJax.typesetPromise([contentBox]).catch(err => console.error(err));
      }

      updateMathStepperUI();
    }

    function getConcentration(order, a0, k, t) {
      if (order === 0) return Math.max(0, a0 - k * t);
      if (order === 1) return a0 * Math.exp(-k * t);
      if (order === 2) return a0 / (1 + a0 * k * t);
      return 0;
    }

    function getRate(order, k, conc) {
      if (conc <= 0) return 0;
      if (order === 0) return k;
      if (order === 1) return k * conc;
      if (order === 2) return k * Math.pow(conc, 2);
      return 0;
    }

    function setOrderScreen2(order) {
      order2 = order;
      [0, 1, 2].forEach(o => {
        const btn = document.getElementById(`btn-order2-${o}`);
        if (o === order) {
          btn.className = "px-5 py-2.5 rounded-xl font-bold border transition duration-200 bg-amberneon text-slate-900 border-amberneon border-glow-active";
        } else {
          btn.className = "px-5 py-2.5 rounded-xl font-bold border transition duration-200 bg-slate-800 text-slate-300 border-slate-600 hover:bg-slate-700";
        }
      });
      initChartScreen2();
    }

    function initChartScreen2() {
      const canvas = document.getElementById('chartScreen2');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      const timePoints = [];
      const concData = [];
      const maxTime = 15;
      const step = 0.2;

      for (let t = 0; t <= maxTime; t += step) {
        timePoints.push(parseFloat(t.toFixed(1)));
        concData.push(getConcentration(order2, defaultA0, defaultK, t));
      }

      if (chart2) chart2.destroy();

      const tangentLinePlugin = {
        id: 'tangentLine',
        afterDraw: (chart) => {
          if (chart.tooltip?._active?.length) {
            const activePoint = chart.tooltip._active[0];
            const activeCtx = chart.ctx;
            const xPixel = activePoint.element.x;
            const yPixel = activePoint.element.y;
            const index = activePoint.index;
            const t = timePoints[index];
            const conc = concData[index];
            const rate = getRate(order2, defaultK, conc);

            document.getElementById('screen2-hover-t').innerText = `${t.toFixed(1)} s`;
            document.getElementById('screen2-hover-conc').innerText = `${conc.toFixed(3)} M`;
            document.getElementById('screen2-hover-rate').innerText = `${rate.toFixed(4)} M/s`;

            const xAxis = chart.scales.x;
            const yAxis = chart.scales.y;

            const dt = 2.5;
            const t1 = Math.max(0, t - dt);
            const t2 = Math.min(maxTime, t + dt);
            const conc1 = conc + rate * (t - t1);
            const conc2 = conc - rate * (t2 - t);

            const x1Pixel = xAxis.getPixelForValue(t1);
            const y1Pixel = yAxis.getPixelForValue(conc1);
            const x2Pixel = xAxis.getPixelForValue(t2);
            const y2Pixel = yAxis.getPixelForValue(conc2);

            activeCtx.save();
            activeCtx.beginPath();
            activeCtx.moveTo(x1Pixel, y1Pixel);
            activeCtx.lineTo(x2Pixel, y2Pixel);
            activeCtx.lineWidth = 2.5;
            activeCtx.strokeStyle = '#fbbf24';
            activeCtx.setLineDash([6, 6]);
            activeCtx.stroke();

            activeCtx.beginPath();
            activeCtx.arc(xPixel, yPixel, 6, 0, 2 * Math.PI);
            activeCtx.fillStyle = '#fbbf24';
            activeCtx.shadowColor = 'rgba(251, 191, 36, 0.8)';
            activeCtx.shadowBlur = 10;
            activeCtx.fill();
            activeCtx.restore();
          }
        }
      };

      chart2 = new Chart(ctx, {
        type: 'line',
        data: {
          labels: timePoints,
          datasets: [{
            label: `[A] 농도 (${order2}차 반응)`,
            data: concData,
            borderColor: '#38bdf8',
            backgroundColor: 'rgba(56, 189, 248, 0.12)',
            borderWidth: 3,
            fill: true,
            pointRadius: 0,
            pointHoverRadius: 6,
            tension: 0.25
          }]
        },
        plugins: [tangentLinePlugin],
        options: {
          responsive: true,
          maintainAspectRatio: false,
          interaction: { mode: 'nearest', intersect: false },
          scales: {
            x: {
              type: 'linear',
              title: { display: true, text: '시간 t (초)', color: '#94a3b8', font: { weight: 'bold' } },
              grid: { color: '#334155' },
              ticks: { color: '#94a3b8' }
            },
            y: {
              title: { display: true, text: '농도 [A] (M)', color: '#94a3b8', font: { weight: 'bold' } },
              grid: { color: '#334155' },
              ticks: { color: '#94a3b8' },
              min: 0,
              max: 1.1
            }
          },
          plugins: {
            legend: { labels: { color: '#f8fafc', font: { size: 13 } } },
            tooltip: {
              callbacks: {
                label: (ctx) => `[A]: ${ctx.parsed.y.toFixed(3)} M`
              }
            }
          }
        }
      });
    }

    function calculateScreen3(source) {
      const order = parseInt(document.getElementById('screen3-order').value);

      let a0, k, t;

      if (source === 'text') {
        a0 = parseFloat(document.getElementById('screen3-a0-input').value) || 0.1;
        k = parseFloat(document.getElementById('screen3-k-input').value) || 0.01;
        t = parseFloat(document.getElementById('screen3-t-input').value) || 0.0;

        document.getElementById('screen3-a0').value = a0;
        document.getElementById('screen3-k').value = k;
        document.getElementById('screen3-t').value = t;
      } else {
        a0 = parseFloat(document.getElementById('screen3-a0').value);
        k = parseFloat(document.getElementById('screen3-k').value);
        t = parseFloat(document.getElementById('screen3-t').value);

        document.getElementById('screen3-a0-input').value = a0.toFixed(2);
        document.getElementById('screen3-k-input').value = k.toFixed(2);
        document.getElementById('screen3-t-input').value = t.toFixed(2);
      }

      let tHalf = 0;
      let at = 0;
      let formulaText = "";

      if (order === 0) {
        tHalf = a0 / (2 * k);
        at = Math.max(0, a0 - k * t);
        formulaText = "공식: t₁/₂ = [A]₀ / 2k";
      } else if (order === 1) {
        tHalf = Math.LN2 / k;
        at = a0 * Math.exp(-k * t);
        formulaText = "공식: t₁/₂ = ln(2) / k";
      } else if (order === 2) {
        tHalf = 1 / (k * a0);
        at = a0 / (1 + a0 * k * t);
        formulaText = "공식: t₁/₂ = 1 / (k[A]₀)";
      }

      const percent = a0 > 0 ? Math.max(0, (at / a0) * 100).toFixed(1) : "0.0";

      document.getElementById('screen3-res-thalf').innerText = (isFinite(tHalf) && tHalf >= 0) ? `${tHalf.toFixed(2)} s` : "N/A";
      document.getElementById('screen3-thalf-formula').innerText = formulaText;
      document.getElementById('screen3-res-at').innerText = `${at.toFixed(3)} M`;
      document.getElementById('screen3-res-percent').innerText = percent;

      const timePoints = [];
      const concData = [];
      const maxTime = Math.max(t * 1.8, isFinite(tHalf) ? tHalf * 2.2 : 20, 10);
      const step = maxTime / 50;

      for (let time = 0; time <= maxTime; time += step) {
        timePoints.push(parseFloat(time.toFixed(1)));
        concData.push(getConcentration(order, a0, k, time));
      }

      const canvas = document.getElementById('chartScreen3');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      if (chart3) chart3.destroy();

      chart3 = new Chart(ctx, {
        type: 'line',
        data: {
          labels: timePoints,
          datasets: [{
            label: `[A] 농도 감쇄 곡선`,
            data: concData,
            borderColor: '#fbbf24',
            backgroundColor: 'rgba(251, 191, 36, 0.15)',
            borderWidth: 3,
            fill: true,
            pointRadius: 0
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: {
              type: 'linear',
              title: { display: true, text: '시간 t (초)', color: '#94a3b8' },
              grid: { color: '#334155' },
              ticks: { color: '#94a3b8' }
            },
            y: {
              title: { display: true, text: '농도 [A] (M)', color: '#94a3b8' },
              grid: { color: '#334155' },
              ticks: { color: '#94a3b8' },
              min: 0,
              max: a0 * 1.05
            }
          },
          plugins: {
            legend: { labels: { color: '#f8fafc' } }
          }
        }
      });
    }

    function updateQSSA() {
      const k1 = parseFloat(document.getElementById('slider-k1').value);
      const k2 = parseFloat(document.getElementById('slider-k2').value);

      document.getElementById('val-k1').innerText = k1.toFixed(2);
      document.getElementById('val-k2').innerText = k2.toFixed(2);

      const ratio = k2 / k1;
      const statusBox = document.getElementById('qssa-status-box');
      const statusTitle = document.getElementById('qssa-status-title');
      const statusDesc = document.getElementById('qssa-status-desc');
      const statusIcon = document.getElementById('qssa-status-icon');

      if (ratio >= 5) {
        statusBox.className = "p-4 rounded-xl border transition-all duration-300 bg-emerald-950/40 border-emerald-500/50 shadow-lg";
        statusIcon.innerText = "✅";
        statusTitle.innerText = "QSSA 정류상태 근사 조건 만족 (k₂ ≫ k₁)";
        statusDesc.innerText = `k₂/k₁ = ${ratio.toFixed(1)} 로 중간체 [I]의 빠른 소멸로 농도가 매우 낮고 d[I]/dt ≈ 0 가 잘 성립합니다.`;
      } else {
        statusBox.className = "p-4 rounded-xl border transition-all duration-300 bg-amber-950/40 border-amber-500/50 shadow-lg";
        statusIcon.innerText = "⚠️";
        statusTitle.innerText = "QSSA 오차 존재 (k₂ 와 k₁ 차이 부족)";
        statusDesc.innerText = `k₂/k₁ = ${ratio.toFixed(1)} 로 중간체 [I]가 계 내에 유의미하게 축적되므로 d[I]/dt ≈ 0 근사에 오차가 발생합니다.`;
      }

      const dt = 0.05;
      const maxTime = 30;
      const times = [];
      const dataA = [], dataI = [], dataP = [];

      let A = 1.0, I = 0.0, P = 0.0;

      for (let t = 0; t <= maxTime; t += dt) {
        times.push(parseFloat(t.toFixed(2)));
        dataA.push(A);
        dataI.push(I);
        dataP.push(P);

        const dA = -k1 * A;
        const dI = k1 * A - k2 * I;
        const dP = k2 * I;

        A += dA * dt;
        I += dI * dt;
        P += dP * dt;

        if (A < 0) A = 0;
        if (I < 0) I = 0;
      }

      const canvas = document.getElementById('chartScreen4');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      if (chart4) chart4.destroy();

      chart4 = new Chart(ctx, {
        type: 'line',
        data: {
          labels: times,
          datasets: [
            { label: '반응물 [A]', data: dataA, borderColor: '#ef4444', borderWidth: 2.5, pointRadius: 0 },
            { label: '중간체 [I]', data: dataI, borderColor: '#fbbf24', borderWidth: 3.5, pointRadius: 0 },
            { label: '생성물 [P]', data: dataP, borderColor: '#10b981', borderWidth: 2.5, pointRadius: 0 }
          ]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: {
              type: 'linear',
              title: { display: true, text: '시간 t (초)', color: '#94a3b8' },
              grid: { color: '#334155' },
              ticks: { color: '#94a3b8' }
            },
            y: {
              title: { display: true, text: '농도 (M)', color: '#94a3b8' },
              grid: { color: '#334155' },
              ticks: { color: '#94a3b8' },
              min: 0,
              max: 1.05
            }
          },
          plugins: {
            legend: { labels: { color: '#f8fafc', font: { size: 12 } } }
          }
        }
      });
    }

    function initParticleSim() {
      const canvas = document.getElementById('particleCanvas');
      if (!canvas) return;
      const rect = canvas.getBoundingClientRect();
      canvas.width = rect.width || 400;
      canvas.height = rect.height || 280;

      resetParticleSim();

      if (!particleAnimId) {
        animateParticles();
      }
    }

    function resetParticleSim() {
      const canvas = document.getElementById('particleCanvas');
      if (!canvas) return;
      const w = canvas.width;
      const h = canvas.height;

      particles = [];
      for (let i = 0; i < NUM_PARTICLES; i++) {
        particles.push({
          x: Math.random() * (w - 20) + 10,
          y: Math.random() * (h - 20) + 10,
          vx: (Math.random() - 0.5) * 2.5,
          vy: (Math.random() - 0.5) * 2.5,
          type: 'A',
          radius: 5
        });
      }
    }

    function stopParticleSim() {
      if (particleAnimId) {
        cancelAnimationFrame(particleAnimId);
        particleAnimId = null;
      }
    }

    function animateParticles() {
      const canvas = document.getElementById('particleCanvas');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      const w = canvas.width;
      const h = canvas.height;

      const k1 = parseFloat(document.getElementById('slider-k1')?.value || 0.1);
      const k2 = parseFloat(document.getElementById('slider-k2')?.value || 1.5);

      ctx.clearRect(0, 0, w, h);

      particles.forEach(p => {
        p.x += p.vx;
        p.y += p.vy;

        if (p.x <= p.radius || p.x >= w - p.radius) p.vx *= -1;
        if (p.y <= p.radius || p.y >= h - p.radius) p.vy *= -1;

        if (p.type === 'A') {
          if (Math.random() < k1 * 0.015) {
            p.type = 'I';
          }
        } else if (p.type === 'I') {
          if (Math.random() < k2 * 0.025) {
            p.type = 'P';
          }
        }

        ctx.beginPath();
        ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);

        if (p.type === 'A') {
          ctx.fillStyle = '#ef4444';
          ctx.shadowColor = 'rgba(239, 68, 68, 0.6)';
        } else if (p.type === 'I') {
          ctx.fillStyle = '#fbbf24';
          ctx.shadowColor = 'rgba(251, 191, 36, 0.8)';
        } else {
          ctx.fillStyle = '#10b981';
          ctx.shadowColor = 'rgba(16, 185, 129, 0.6)';
        }

        ctx.shadowBlur = 6;
        ctx.fill();
        ctx.shadowBlur = 0;
      });

      particleAnimId = requestAnimationFrame(animateParticles);
    }

    const mathNotes = [
      // 0차 반응 상세 유도
      `
      <div class="space-y-5">
        <div class="border-b border-slate-700 pb-3">
          <span class="text-xs font-bold text-amberneon uppercase tracking-widest block mb-1">Step-by-Step Rigorous Derivation</span>
          <h3 class="text-2xl font-black text-white">0차 반응 (Zero-Order Reaction) 완벽 수식 유도</h3>
        </div>
        
        <div class="space-y-4 text-sm sm:text-base leading-relaxed">
          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">1단계: 미분 속도식 (Differential Rate Law) 정의</h4>
            <p class="text-slate-300 text-sm mb-2">
              반응 속도는 시간당 반응물 $[A]$의 농도 감소율로 정의되며, 0차 반응은 속도가 농도의 0제곱에 비례합니다 ($[A]^0 = 1$):
            </p>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-amber-300">
              $$ v = -\\frac{d[A]}{dt} = k [A]^0 = k $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">2단계: 변수분리법 (Separation of Variables)</h4>
            <p class="text-slate-300 text-sm mb-2">
              농도 변수 $d[A]$와 시간 변수 $dt$를 양변으로 각각 분리합니다:
            </p>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center">
              $$ d[A] = -k \, dt $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">3단계: 적분 한계 설정 및 정적분 (Definite Integration)</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center space-y-2">
              <div>$$ \\int_{[A]_0}^{[A]_t} d[A] = -k \\int_{0}^{t} dt $$</div>
              <div>$$ [A]_t - [A]_0 = -kt $$</div>
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-amberneon/40">
            <h4 class="font-bold text-amberneon text-base mb-2">4단계: 적분 속도식 및 1차 함수(직선) 개형</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-amberneon text-lg font-bold">
              $$ [A]_t = -kt + [A]_0 $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">5단계: 반감기 ($t_{1/2}$) 공식 대입 유도</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center space-y-2">
              <div class="text-amberneon font-bold">$$ t_{1/2} = \\frac{[A]_0}{2k} $$</div>
            </div>
          </div>
        </div>
      </div>
      `,

      // 1차 반응 상세 유도
      `
      <div class="space-y-5">
        <div class="border-b border-slate-700 pb-3">
          <span class="text-xs font-bold text-amberneon uppercase tracking-widest block mb-1">Step-by-Step Rigorous Derivation</span>
          <h3 class="text-2xl font-black text-white">1차 반응 (First-Order Reaction) 완벽 수식 유도</h3>
        </div>
        
        <div class="space-y-4 text-sm sm:text-base leading-relaxed">
          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">1단계: 미분 속도식 정의</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-amber-300">
              $$ -\\frac{d[A]}{dt} = k [A] $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">2단계: 변수분리법 및 정적분</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center space-y-2">
              <div>$$ \\int_{[A]_0}^{[A]_t} \\frac{1}{[A]} d[A] = -k \\int_{0}^{t} dt $$</div>
              <div>$$ \\ln[A]_t - \\ln[A]_0 = -kt $$</div>
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-amberneon/40">
            <h4 class="font-bold text-amberneon text-base mb-2">3단계: 적분 속도식</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center space-y-2">
              <div class="text-amberneon font-bold text-lg">$$ [A]_t = [A]_0 e^{-kt} $$</div>
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">4단계: 반감기 ($t_{1/2}$) 유도</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center">
              <div class="text-amberneon font-bold">$$ t_{1/2} = \\frac{\\ln 2}{k} \\approx \\frac{0.693}{k} $$</div>
            </div>
          </div>
        </div>
      </div>
      `,

      // 2차 반응 상세 유도
      `
      <div class="space-y-5">
        <div class="border-b border-slate-700 pb-3">
          <span class="text-xs font-bold text-amberneon uppercase tracking-widest block mb-1">Step-by-Step Rigorous Derivation</span>
          <h3 class="text-2xl font-black text-white">2차 반응 (Second-Order Reaction) 완벽 수식 유도</h3>
        </div>
        
        <div class="space-y-4 text-sm sm:text-base leading-relaxed">
          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">1단계: 미분 속도식 및 변수분리</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-amber-300">
              $$ -\\frac{d[A]}{dt} = k [A]^2 \\implies \\frac{1}{[A]^2} d[A] = -k \, dt $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-amberneon/40">
            <h4 class="font-bold text-amberneon text-base mb-2">2단계: 적분 속도식 유도</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-amberneon font-bold text-lg">
              $$ \\frac{1}{[A]_t} = kt + \\frac{1}{[A]_0} $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">3단계: 반감기 ($t_{1/2}$) 대입 유도</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center">
              <div class="text-amberneon font-bold">$$ t_{1/2} = \\frac{1}{k [A]_0} $$</div>
            </div>
          </div>
        </div>
      </div>
      `,

      // QSSA 상세 유도
      `
      <div class="space-y-5">
        <div class="border-b border-slate-700 pb-3">
          <span class="text-xs font-bold text-amberneon uppercase tracking-widest block mb-1">Step-by-Step Advanced Kinetics Derivation</span>
          <h3 class="text-2xl font-black text-white">QSSA 정류상태 근사 및 미분방정식 유도</h3>
        </div>
        
        <div class="space-y-4 text-sm sm:text-base leading-relaxed">
          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">1단계: 연속 반응 속도식</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-sky-300">
              $$ \\frac{d[I]}{dt} = k_1 [A] - k_2 [I] $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">2단계: QSSA 근사 조건 ($\frac{d[I]}{dt} \approx 0$)</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-amberneon font-bold">
              $$ [I]_{ss} \\approx \\frac{k_1}{k_2} [A] $$
            </div>
          </div>

          <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700">
            <h4 class="font-bold text-amberneon text-base mb-2">3단계: 최종 생성물 반응속도 단순화</h4>
            <div class="p-3 bg-navy-900 rounded-lg font-mono text-center text-emerald-400 font-bold">
              $$ \\frac{d[P]}{dt} = k_2 [I]_{ss} = k_1 [A] $$
            </div>
          </div>
        </div>
      </div>
      `
    ];

    // 앱 초기화
    window.addEventListener('DOMContentLoaded', () => {
      navigateTo('home');
    });
  </script>
</body>
</html>
