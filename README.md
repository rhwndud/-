import React, { useState, useEffect, useRef } from 'react';
import { 
  Brain, Search, Info, MessageSquare, AlertCircle, 
  RotateCcw, Sparkles, CheckCircle2, ChevronRight,
  Send, BookOpen, Layers, UserCheck, ShieldAlert, ArrowRight, Lock, KeyRound, Bot, Sparkle
} from 'lucide-react';

const STAGES_DATA = [
  // STAGE 1: EASY (지우)
  {
    id: 1,
    title: "1단계: 축제 발표자 명단 누락",
    difficulty: "⭐ 기초 (사실과 해석 분리)",
    characterName: "지우",
    characterRole: "영상제작 조장",
    hairColor: "#3B82F6",
    shirtColor: "#3B82F6",
    difficultyDesc: "🔒 처음에는 단서가 0개입니다. 비난하지 않고 물어보아야 단서가 해금됩니다.",
    incidentText: "학교 축제 영상 제작 발표회 하루 전, 완성된 발표자 명단에 내 이름이 빠져있는 것을 발견했다.",
    hiddenClues: [
      { id: 'c1', title: "단톡방 기록", text: "지우: 'oo이가 영상 편집이랑 발표 둘 다 맡으면 너무 부담스러울 텐데...'", stepUnlocked: false },
      { id: 'c2', title: "담당 선생님 지침", text: "선생님: '발표자는 무조건 1명으로 제한한다. 역할 분담 확실히 해라.'", stepUnlocked: false },
      { id: 'c3', title: "지우의 메모지", text: "메모: 'oo이 편집 집중할 수 있게 발표는 내가 자청하기'", stepUnlocked: false }
    ],
    truthText: "지우는 내가 밤새 편집하느라 고생한 것을 알고, 발표까지 맡기면 부담될까 봐 선생님 지침(발표자 1명)에 따라 발표를 자청한 것이었다.",
    turns: [
      {
        turnIndex: 1,
        prompt: "명단에서 내 이름이 빠진 걸 본 첫 순간, 지우에게 뭐라고 말할까?",
        options: [
          { id: '1-0', level: 0, badge: "충동/회피", text: "치사하게 나 빼고 너 혼자 공 차지하려고? 나 안 해! (가방 싸서 나간다)", reply: "어...? oo아 갑자기 왜 그래?! 말을 해야 알지...!", exp: 'sad', score: 0, feedback: "[충동 도피] 사실 확인 없이 대화를 끊었습니다. (단서 획득 실패)", unlocksClueId: null },
          { id: '1-1', level: 1, badge: "일방/통제", text: "네 마음대로 내 이름 뺀 거지? 당장 다시 내 이름 넣어서 선생님한테 재제출해.", reply: "다짜고짜 그렇게 명령하듯 말하면 어떡해... 사정이 있었어.", exp: 'sad', score: 5, feedback: "[일방 추궁] 상대를 경계하게 만들어 진실을 듣지 못했습니다. (단서 획득 실패)", unlocksClueId: null },
          { id: '1-2', level: 2, badge: "호혜/타협", text: "발표 혼자 하고 싶으면 말로 하지. 대신 편집본은 네가 마무리해줄래?", reply: "아니야, 난 혼자 공을 차지하려던 게 아니야! 선생님 지침이 있었거든...", exp: 'surprised', score: 12, feedback: "[조건 타협] 거래를 시도했으나 상대의 지침 사정을 알게 되었습니다.", unlocksClueId: 'c2' },
          { id: '1-3', level: 3, badge: "협력/조율", text: "지우야, 발표자 명단에 내 이름이 없던데 혹시 어떤 이유로 조율된 건지 물어봐도 될까?", reply: "아, oo아! 가만히 설명해주려고 했어. 네가 편집으로 밤샸는데 발표까지 시키는 건 미안했거든...", exp: 'neutral', score: 20, feedback: "[협력 탐색] 비난 없는 질문으로 지우의 속마음과 단톡방 기록 단서를 알아냈습니다!", unlocksClueId: 'c1' }
        ]
      },
      {
        turnIndex: 2,
        prompt: "지우가 어색해하며 설명을 이어가려 한다. 어떻게 반응할까?",
        options: [
          { id: '2-0', level: 0, badge: "충동/회피", text: "됐어, 변명 듣기 싫으니까 그만해.", reply: "변명이 아닌데... 들어주지도 않으면 어떡해...", exp: 'sad', score: 0, feedback: "[대화 거부] 상대의 해명 기회를 봉쇄했습니다.", unlocksClueId: null },
          { id: '2-1', level: 1, badge: "일방/통제", text: "이유가 뭐든 조장 마음대로 이름 뺀 건 잘한 거 하나 없어.", reply: "나도 고민 많이 하고 정한 건데 그렇게 말하니까 서운하다.", exp: 'sad', score: 5, feedback: "[일방 지적] 자신의 기준만 고집했습니다.", unlocksClueId: null },
          { id: '2-2', level: 2, badge: "호혜/타협", text: "선생님이 발표자 1명만 정하라고 하신 거야? 그럼 다음 발표는 내가 하게 해줘.", reply: "응 맞아! 선생님이 1명만 쓰라고 하셨어. 다음엔 당연히 네가 발표해!", exp: 'happy', score: 15, feedback: "[순서 타협] 선생님 지침 단서를 확정지었습니다.", unlocksClueId: 'c2' },
          { id: '2-3', level: 3, badge: "협력/조율", text: "네가 혼자 결정하느라 고민이 많았을 것 같아. 내 부담을 고려해준 건지 궁금해.", reply: "맞아! 여기 내 메모지에도 적어뒀는데, 너 편집 부담 줄여주려고 자청한 거야!", exp: 'happy', score: 20, feedback: "[공감 조망] 상대의 배려 의도와 숨겨진 메모지 단서를 발견했습니다!", unlocksClueId: 'c3' }
        ]
      },
      {
        turnIndex: 3,
        prompt: "지우가 나를 배려해서 한 행동이었음을 알게 되었다. 뭐라고 말해줄까?",
        options: [
          { id: '3-0', level: 0, badge: "충동/회피", text: "아... 그래? (민망해서 고개 숙이고 딴짓함)", reply: "어... 미안해할 필요는 없어!", exp: 'neutral', score: 0, feedback: "[어색한 도피] 감정 표현을 머뭇거렸습니다.", unlocksClueId: null },
          { id: '3-1', level: 1, badge: "일방/통제", text: "날 배려한 건 알겠는데, 그래도 다음부터 상의 없이 정하진 마.", reply: "응... 알았어. 앞으론 미리 물어볼게.", exp: 'sad', score: 5, feedback: "[훈계 통제] 감사보다 규칙을 강조했습니다.", unlocksClueId: null },
          { id: '3-2', level: 2, badge: "호혜/타협", text: "날 배려해줘서 고마워! 그럼 발표 연습할 때 대본 만드는 건 내가 도와줄게.", reply: "와 진짜? 대본 같이 써주면 너무 든든하지!", exp: 'happy', score: 15, feedback: "[기브앤테이크] 고마움을 행동으로 나누었습니다.", unlocksClueId: null },
          { id: '3-3', level: 3, badge: "협력/조율", text: "내 피곤함까지 신경 써준 마음이 정말 고마워. 지우 네 덕분에 편집에 집중할 수 있겠다!", reply: "알아줘서 고마워! 우리 이번 축제 영상 1등 하자!", exp: 'happy', score: 20, feedback: "[통합 감사] 상대의 선의를 완전하게 수용했습니다.", unlocksClueId: null }
        ]
      },
      {
        turnIndex: 4,
        prompt: "발표 준비 방향을 함께 조율해볼까?",
        options: [
          { id: '4-0', level: 0, badge: "충동/회피", text: "난 편집 다 했으니까 먼저 집에 갈게!", reply: "어...? 응 그래 고생했어...", exp: 'neutral', score: 0, feedback: "[서둘러 마침] 협력 과정을 마치지 않고 떠났습니다.", unlocksClueId: null },
          { id: '4-1', level: 1, badge: "일방/통제", text: "발표할 때 내가 만든 영상 의도 확실하게 설명해야 해, 알겠지?", reply: "응, 네가 지시한 대로 스크립트 작성할게.", exp: 'neutral', score: 5, feedback: "[지시적 태도] 대등한 협력 대신 지시했습니다.", unlocksClueId: null },
          { id: '4-2', level: 2, badge: "호혜/타협", text: "네가 발표 멋지게 해주면, 내일 내가 맛있는 매점 빵 쏠게!", reply: "오 진짜? 맛있는 거 사주면 힘내서 잘해야지!", exp: 'happy', score: 15, feedback: "[유쾌한 보상] 흥미로운 보상으로 동기부여했습니다.", unlocksClueId: null },
          { id: '4-3', level: 3, badge: "협력/조율", text: "영상에 담긴 핵심 메시지 슬라이드를 같이 검토해보자. 네가 발표하기 편하게 맞춰줄게.", reply: "고마워 oo아! 같이 맞추니까 훨씬 안심된다!", exp: 'happy', score: 20, feedback: "[공동 작업] 상대의 수행을 돕는 통합 조율을 이뤘습니다.", unlocksClueId: null }
        ]
      },
      {
        turnIndex: 5,
        prompt: "대화를 마치며 앞으로의 소통에 대해 이야기하자.",
        options: [
          { id: '5-0', level: 0, badge: "충동/회피", text: "응 내일 봐~ (빠르게 퇴장)", reply: "응 내일 봐!", exp: 'neutral', score: 0, feedback: "[단순 종료] 약속 없이 마쳤습니다.", unlocksClueId: null },
          { id: '5-1', level: 1, badge: "일방/통제", text: "앞으로는 나한테 불리해 보이는 결정도 무조건 동의받고 진행해.", reply: "알겠어, 앞으론 동의를 구할게.", exp: 'sad', score: 5, feedback: "[일방 가이드] 상호 신뢰보다는 규칙만 내세웠습니다.", unlocksClueId: null },
          { id: '5-2', level: 2, badge: "호혜/타협", text: "다음에는 서로 고민되는 게 있으면 바로 카톡으로 신호 주기다!", reply: "좋아! 바로바로 신호 줄게!", exp: 'happy', score: 15, feedback: "[상호 신호] 유연한 소통 규칙을 만들었습니다.", unlocksClueId: null },
          { id: '5-3', level: 3, badge: "협력/조율", text: "오늘 대화해보길 정말 잘했어. 혼자 지레짐작하지 않고 편하게 물어볼게!", reply: "나도 솔직하게 말해줘서 너무 고마웠어. 최고의 팀워크다!", exp: 'happy', score: 20, feedback: "[신뢰 확립] 지레짐작하지 않는 소통을 내면화했습니다.", unlocksClueId: null }
        ]
      }
    ]
  },
  // STAGE 2: EASY-MEDIUM (민호)
  {
    id: 2,
    title: "2단계: 메시지 읽씹과 무관심한 복도",
    difficulty: "⭐⭐ 초급 (개인화 오류 극복)",
    characterName: "민호",
    characterRole: "짝꿍 및 동아리 부원",
    hairColor: "#10B981",
    shirtColor: "#059669",
    difficultyDesc: "🔒 민호가 나를 무시하는 건지, 개인 사정 때문인지 대화로 단서를 캐내세요.",
    incidentText: "어제 민호에게 보낸 중요 동아리 질문 톡을 '읽고 무시'당했다. 오늘 아침 복도에서 눈이 마주쳤는데 민호는 인사도 없이 쌩 지나쳤다.",
    hiddenClues: [
      { id: 'c1', title: "도서관 퇴실 앱 기록", text: "새벽 2시 45분: '민호 님이 독서실 열람실 퇴실 처리되었습니다.'", stepUnlocked: false },
      { id: 'c2', title: "민호의 SNS 메세지", text: "상태메시지: '멘탈 바닥... 잠 좀 자고 싶다.'", stepUnlocked: false },
      { id: 'c3', title: "민호 가방의 약 봉투", text: "상담센터 및 신경정신과 처방 봉투", stepUnlocked: false }
    ],
    truthText: "민호는 입시 스트레스와 부모님과의 갈등으로 밤샘 시험 후 심각한 번아웃 상태였다. 나를 무시한 게 아니라 제정신이 아니었던 것이었다.",
    turns: [
      {
        turnIndex: 1,
        prompt: "복도에서 나를 지나쳐 간 민호에게 어떤 방식으로 다가갈까?",
        options: [
          { id: '1-0', level: 0, badge: "충동/회피", text: "(나도 눈 찌푸리며) 기분 더럽네. 앞으로 평생 인사 안 해!", reply: "...", exp: 'sad', score: 0, feedback: "[충동 보복] 오해를 확인할 생각조차 안 하고 자리를 피했습니다.", unlocksClueId: null },
          { id: '1-1', level: 1, badge: "일방/통제", text: "야 민호야, 너 내 톡 읽씹하고 복도에서 인사 안 받는 이유가 뭐야? 당장 말해.", reply: "어...? 아... 미안, 내가 지금 정신이 없어서...", exp: 'surprised', score: 5, feedback: "[일방 추궁] 민호가 방어적으로 물러섰습니다.", unlocksClueId: null },
          { id: '1-2', level: 2, badge: "호혜/타협", text: "민호야, 바쁜 일 있어? 톡 답장해 주면 매점 사탕 하나 줄게!", reply: "아... 톡? 미안, 휴대폰 볼 여유가 아예 없었어.", exp: 'neutral', score: 12, feedback: "[조건 유도] 민호의 바쁜 분위기 단서를 살짝 감지했습니다.", unlocksClueId: 'c1' },
          { id: '1-3', level: 3, badge: "협력/조율", text: "민호야, 안색이 많이 피곤해 보이는데 혹시 오늘 무슨 힘든 일 있어?", reply: "아... oo아... 알아봐 줘서 고마워. 사실 요며칠 잠을 한 시간도 못 자고 밤새워서 제정신이 아니야.", exp: 'sad', score: 20, feedback: "[맥락 관심] 따뜻한 질문으로 민호의 새벽 퇴실 기록과 지친 상태를 알게 되었습니다!", unlocksClueId: 'c1' }
        ]
      },
      {
        turnIndex: 2,
        prompt: "민호가 멍한 눈으로 한숨을 쉰다. 메시지 읽씹에 대해 어떻게 물어볼까?",
        options: [
          { id: '2-0', level: 0, badge: "충동/회피", text: "피곤하면 다야? 사람 무시하는 건 예의가 없지! (발걸음을 돌린다)", reply: "아니... 내 말 좀 들어봐...", exp: 'sad', score: 0, feedback: "[충동 비난] 민호가 입을 닫아버렸습니다.", unlocksClueId: null },
          { id: '2-1', level: 1, badge: "일방/통제", text: "아무리 피곤해도 톡 답장 하나 보내는 게 그렇게 어렵냐?", reply: "미안하다... 스마트폰을 켜둘 멘탈조차 아니었어...", exp: 'sad', score: 5, feedback: "[일방 비판] 비난이 앞서 민호의 진짜 사정을 못 들었습니다.", unlocksClueId: null },
          { id: '2-2', level: 2, badge: "호혜/타협", text: "어제 물어본 동아리 질문 답변만 간단히 해주면 더 이상 안 괴롭힐게!", reply: "아! 그 질문... 폰 프로필에 '아무하고도 연락 안 함' 써둘 정도로 힘든 상태였거든. 미안!", exp: 'neutral', score: 15, feedback: "[실용 타협] 민호의 SNS 상태메시지 단서를 파악했습니다.", unlocksClueId: 'c2' },
          { id: '2-3', level: 3, badge: "협력/조율", text: "어제 톡 답장이 없어서 혹시 나한테 서운한 게 있나 걱정했는데, 개인 사정 때문이었구나?", reply: "너한테 서운하긴 커녕! 입시 때문에 멘탈 터져서 내 SNS 프로필에도 써둔 상황이었어. 오해하게 해서 너무 미안해!", exp: 'surprised', score: 20, feedback: "[오해 해소] 개인화 오류를 바로잡고 SNS 상태 메시지 단서를 얻었습니다!", unlocksClueId: 'c2' }
        ]
      },
      {
        turnIndex: 3,
        prompt: "민호가 입시 스트레스로 번아웃이 왔음을 털어놓았다. 나의 반응은?",
        options: [
          { id: '3-0', level: 0, badge: "충동/회피", text: "에휴... 입시는 다 힘든 거지 뭐. 나 갈게.", reply: "응... 그래...", exp: 'sad', score: 0, feedback: "[공감 결여] 회피성 대화로 민호를 외롭게 만들었습니다.", unlocksClueId: null },
          { id: '3-1', level: 1, badge: "일방/통제", text: "아무리 힘들어도 주변 사람한테 티 내면서 상처 주는 건 나쁜 습관이야.", reply: "맞는 말이긴 한데... 지금 훈계 들으니까 더 지친다...", exp: 'sad', score: 5, feedback: "[훈계 지적] 부담을 주었습니다.", unlocksClueId: null },
          { id: '3-2', level: 2, badge: "호혜/타협", text: "입시 스트레스 진짜 심하지! 오늘 점심때 매점 가서 피자빵 사줄 테니 기분 풀어라!", reply: "고맙다 야! 매점 가면서 가방 정리도 좀 해야겠다.", exp: 'happy', score: 15, feedback: "[위로 지원] 민호의 마음을 누그러뜨렸습니다.", unlocksClueId: null },
          { id: '3-3', level: 3, badge: "협력/조율", text: "그런 무거운 부담을 혼자 짊어지고 있었다니 정말 힘들었겠다. 가방에 약 봉투도 보이는데 병원 다닐 정도였어?", reply: "oo아... 알아보아 주고 걱정해 줘서 고마워. 상담 다니면서 겨우 견디고 있어.", exp: 'happy', score: 20, feedback: "[깊은 수용] 민호의 약 봉투 단서와 마음속 깊은 아픔을 수용했습니다!", unlocksClueId: 'c3' }
        ]
      },
      {
        turnIndex: 4,
        prompt: "동아리 업무와 민호의 컨디션을 어떻게 정리할까?",
        options: [
          { id: '4-0', level: 0, badge: "충동/회피", text: "동아리 일은 알아서 해~ 관여 안 할게.", reply: "어...? 일은 해야 하는데...", exp: 'neutral', score: 0, feedback: "[무책임 방관] 업무를 방치했습니다.", unlocksClueId: null },
          { id: '4-1', level: 1, badge: "일방/통제", text: "동아리 질문은 내가 다 알아서 처리할 테니 너는 시험공부나 해.", reply: "그래도 내 담당인데 무조건 빼는 건 미안한데...", exp: 'sad', score: 5, feedback: "[일방 통제] 권한을 배제했습니다.", unlocksClueId: null },
          { id: '4-2', level: 2, badge: "호혜/타협", text: "급한 동아리 질문만 내가 3개 해결해 줄 테니, 민호 너는 컨디션 회복해라!", reply: "우와 진짜? 고마워! 나중에 두 배로 도울게!", exp: 'happy', score: 15, feedback: "[역할 분담] 부담을 나누었습니다.", unlocksClueId: null },
          { id: '4-3', level: 3, badge: "협력/조율", text: "이번 주 동아리 일정은 내가 조율해서 연장해 둘게. 필요하면 언제든 도움 요청해!", reply: "고마워... 너처럼 이해심 깊은 친구가 있어서 다행이다.", exp: 'happy', score: 20, feedback: "[통합 배려] 목표와 상황을 모두 살려냈습니다.", unlocksClueId: null }
        ]
      },
      {
        turnIndex: 5,
        prompt: "대화를 마치며 소통 약속을 하자.",
        options: [
          { id: '5-0', level: 0, badge: "충동/회피", text: "그래, 힘내라. 바이!", reply: "응 바이!", exp: 'neutral', score: 0, feedback: "[단순 종료] 약속 없는 마침.", unlocksClueId: null },
          { id: '5-1', level: 1, badge: "일방/통제", text: "앞으로는 아무리 바빠도 나한테 읽씹은 절대 하지 마라.", reply: "응... 노력해 볼게.", exp: 'sad', score: 5, feedback: "[일방 규칙] 경계만 강조했습니다.", unlocksClueId: null },
          { id: '5-2', level: 2, badge: "호혜/타협", text: "다음부터 연락 힘들 때는 '바쁨' 이모티콘 하나만 보내주기다!", reply: "오케이! 그 이모티콘은 꼭 보낼게!", exp: 'happy', score: 15, feedback: "[실용 합의] 실질적 규칙을 마련했습니다.", unlocksClueId: null },
          { id: '5-3', level: 3, badge: "협력/조율", text: "상대의 태도만 보고 나 때문이라고 지레짐작하지 않고 먼저 물어보는 게 중요하다는 걸 느꼈어!", reply: "나도 먼저 사정을 설명하도록 노력할게. 고마워!", exp: 'happy', score: 20, feedback: "[성찰 신뢰] 개인화 오류를 극복한 대화 조율 완성!", unlocksClueId: null }
        ]
      }
    ]
  },
  // STAGE 3: MEDIUM (소율)
  {
    id: 3,
    title: "3단계: 동아리 점심 모임 소외감",
    difficulty: "⭐⭐⭐ 중급 (거절 민감성 & 파국화 차단)",
    characterName: "소율",
    characterRole: "동아리 단짝 친구",
    hairColor: "#F59E0B",
    shirtColor: "#D97706",
    difficultyDesc: "🔒 소율이가 갑자기 입을 다문 이유! 따지면 방어적으로 변하니 부드럽게 탐색하세요.",
    incidentText: "점심시간에 동아리방 문을 열고 들어가는데, 소율이와 부원들이 나를 보더니 황급히 대화를 뚝 멈추고 서로 눈짓을 주고받았다.",
    hiddenClues: [
      { id: 'c1', title: "가방 옆 영수증", text: "소율이 가방 옆 수제 케이크 전문점 영수증", stepUnlocked: false },
      { id: 'c2', title: "소율의 책상 메모", text: "메모: 'oo이 깜짝 생일 파티 동선 및 롤링페이퍼'", stepUnlocked: false },
      { id: 'c3', title: "소율의 핸드폰 알림", text: "D-1 비밀 리허설 준비물 챙기기 알림", stepUnlocked: false }
    ],
    truthText: "소율이와 부원들은 내일 내 생일을 맞아 동아리실에서 '깜짝 파티'를 준비하고 있었다! 내가 갑자기 들어와서 들통날까 봐 당황해 입을 닫았던 것이다.",
    turns: [
      {
        turnIndex: 1,
        prompt: "동아리실 문을 열자 분위기가 싸늘해졌다. 소율이에게 첫 마디를 어떻게 건넬까?",
        options: [
          { id: '1-0', level: 0, badge: "충동/회피", text: "미안, 내가 방해했나 보네. 나 나갈게! (문 쾅 닫고 나간다)", reply: "앗...! oo아, 잠시만!! 도망치듯 나가면 어떡해...?", exp: 'sad', score: 0, feedback: "[충동 도피] 오해를 확인할 기회를 버리고 도망쳤습니다.", unlocksClueId: null },
          { id: '1-1', level: 1, badge: "일방/통제", text: "너네 지금 나 빼놓고 내 뒷담화하고 있었던 거지? 당장 사실대로 말해!", reply: "어...? 뒷담화라니?! oo아, 다짜고짜 그렇게 따지면 어떡해?", exp: 'sad', score: 5, feedback: "[일방 추궁] 압박으로 소율이가 입을 더 굳게 다물었습니다.", unlocksClueId: null },
          { id: '1-2', level: 2, badge: "호혜/타협", text: "너네 중요한 얘기 중이었어? 내가 매점 다녀오면 솔직히 말해줄래?", reply: "앗, 아니야! 매점 가방 옆에 둔 거 영수증 때문에 당황해서 그래!", exp: 'surprised', score: 12, feedback: "[조건 타협] 소율이 가방 옆의 케이크 영수증 단서를 포착했습니다!", unlocksClueId: 'c1' },
          { id: '1-3', level: 3, badge: "협력/조율", text: "다들 되게 진지하게 이야기하고 있던데, 혹시 내가 들어오기 전에 재미있는 일 있었어?", reply: "앗...!! 어, 아무것도 아니야! (가방 옆 케이크 영수증을 얼른 숨기려 함)", exp: 'surprised', score: 20, feedback: "[중립 관찰] 부담 없는 질문으로 소율이의 당황한 행동과 케이크 영수증 단서를 얻었습니다!", unlocksClueId: 'c1' }
        ]
      },
      {
        turnIndex: 2,
        prompt: "소율이가 눈을 피하며 당황해한다. 어떻게 대처할까?",
        options: [
          { id: '2-0', level: 0, badge: "충동/회피", text: "(책상을 살짝 차며) 거짓말 좀 하지 마! 나 바보 취급해?", reply: "아니... 발로 책상을 치면 어떡해...", exp: 'sad', score: 0, feedback: "[충동 폭력] 감정을 제어하지 못했습니다.", unlocksClueId: null },
          { id: '2-1', level: 1, badge: "일방/통제", text: "숨기는 거 다 안 털어놓으면 나 당장 동아리 탈퇴할 거야.", reply: "헐... 탈퇴라니? 너무 극단적이야 ㅠㅠ", exp: 'surprised', score: 5, feedback: "[최후 통첩] 위협으로 상대를 당황시켰습니다.", unlocksClueId: null },
          { id: '2-2', level: 2, badge: "호혜/타협", text: "소율아, 아까 영수증 봤는데 맛있는 거 사줄 거면 모른 척해 줄게!", reply: "어?! 그걸 봤어?! 실은... 내일 파티 메모도 적고 있었는데...", exp: 'surprised', score: 15, feedback: "[유쾌 흥정] 소율의 책상 메모 단서를 끌어냈습니다!", unlocksClueId: 'c2' },
          { id: '2-3', level: 3, badge: "협력/조율", text: "케이크 영수증도 그렇고 기쁜 일 준비 중인 것 같은데, 나한테 말하기 어려운 사정이 있어?", reply: "아... oo아... 실은 책상 위에 '깜짝 생일 파티 메모' 적고 있었는데 네가 와서 숨긴 거였어!", exp: 'neutral', score: 20, feedback: "[배려 탐색] 상대의 입장을 안심시켜 책상 메모 단서를 알아냈습니다!", unlocksClueId: 'c2' }
        ]
      },
      {
        turnIndex: 3,
        prompt: "소율이가 망설이다가 진실을 털어놓으려 한다. 내 반응은?",
        options: [
          { id: '3-0', level: 0, badge: "충동/회피", text: "답답해! 말하기 싫으면 말아라! (달려나감)", reply: "야, 야! oo아!! 잡으러 가야겠다!", exp: 'surprised', score: 0, feedback: "[충동 탈출] 자리를 떠나버렸습니다.", unlocksClueId: null },
          { id: '3-1', level: 1, badge: "일방/통제", text: "친구 사이에 비밀 만드는 거 진짜 비겁한 거야. 당장 대답해.", reply: "비겁하다니... 네 생각해서 준비한 건데 서운하다...", exp: 'sad', score: 5, feedback: "[도덕 비난] 상대를 비난했습니다.", unlocksClueId: null },
          { id: '3-2', level: 2, badge: "호혜/타협", text: "너희끼리 비밀 모임 만드는 거면 나도 딴 친구들이랑 모임 만든다?", reply: "아니야 절대 나쁜 비밀이 아니야! 폰 알림도 맞춰뒀단 말야!", exp: 'surprised', score: 10, feedback: "[보복 경고] 핸드폰 알림 단서를 언급하게 만들었습니다.", unlocksClueId: 'c3' },
          { id: '3-3', level: 3, badge: "협력/조율", text: "분위기가 어색해서 따돌림당하는 줄 알고 서운했지만, 소율이 네가 나쁜 의도가 아님을 믿어. 솔직히 말해줄래?", reply: "oo아! 사실 내일 네 생일이라 '비밀 파티 리허설' 알림 맞춰두고 준비 중이었어!! 들켜서 아쉽다!", exp: 'happy', score: 20, feedback: "[신뢰 형성] 나의 솔직함과 신뢰로 비밀 파티 알림 단서를 풀었습니다!", unlocksClueId: 'c3' }
        ]
      },
      {
        turnIndex: 4,
        prompt: "나를 위한 깜짝 생일 파티 준비였다! 반응은?",
        options: [
          { id: '4-0', level: 0, badge: "충동/회피", text: "아 뭐야... 난 또... (부끄러워서 도망감)", reply: "어...? 어디 가? 쑥스러워서 그래?", exp: 'surprised', score: 0, feedback: "[부끄러움 도피] 도망쳤습니다.", unlocksClueId: null },
          { id: '4-1', level: 1, badge: "일방/통제", text: "아무리 파티라도 사람 무안하게 문 열자마자 입을 딱 닫는 건 잘못됐지.", reply: "지적이 먼저 나오네... 서운하다.", exp: 'sad', score: 5, feedback: "[지적 우선] 서운함을 안겼습니다.", unlocksClueId: null },
          { id: '4-2', level: 2, badge: "호혜/타협", text: "대박! 날 위한 파티였다니! 대신 내일 케이크 맛있게 먹어줄게!", reply: "헤헤 좋아! 내일 기대해~", exp: 'happy', score: 15, feedback: "[유쾌 타협] 분위기를 전환했습니다.", unlocksClueId: null },
          { id: '4-3', level: 3, badge: "협력/조율", text: "날 위해서 파티를 준비 중이었다니 정말 감동이야. 혼자 따돌림이라 지레짐작해서 미안해!", reply: "히히 네가 기뻐하니 정말 다행이다!", exp: 'happy', score: 20, feedback: "[감사 표현] 인지 재구성을 이뤄냈습니다.", unlocksClueId: null }
        ]
      },
      {
        turnIndex: 5,
        prompt: "오해가 풀린 순간, 앞으로의 소통 규칙을 정하자.",
        options: [
          { id: '5-0', level: 0, badge: "충동/회피", text: "응 알겠어. 나 교실 갈게!", reply: "응~ 나중에 단톡방 확인해!", exp: 'neutral', score: 0, feedback: "[단순 종결] 마무리.", unlocksClueId: null },
          { id: '5-1', level: 1, badge: "일방/통제", text: "앞으로 동아리 부원끼리 내 얘기할 때는 사전에 미리 말해줘.", reply: "서프라이즈를 미리 말할 순 없잖아...", exp: 'sad', score: 5, feedback: "[비현실 통제] 무리한 규칙 강요.", unlocksClueId: null },
          { id: '5-2', level: 2, badge: "호혜/타협", text: "다음부터 이런 일 있으면 살짝 힌트라도 줘! 모른 척해 줄 테니까~", reply: "키키 좋아! 눈짓으로 힌트 줄게!", exp: 'happy', score: 15, feedback: "[상호 합의] 타협적 신호를 만들었습니다.", unlocksClueId: null },
          { id: '5-3', level: 3, badge: "협력/조율", text: "소율아, 오늘처럼 어색해도 혼자 나쁘게 생각지 않고 먼저 물어볼게!", reply: "응! 그렇게 말해주니 너무 든든해!", exp: 'happy', score: 20, feedback: "[통합 조율] 근본 신뢰를 세웠습니다.", unlocksClueId: null }
        ]
      }
    ]
  },
  // STAGE 4: HARD (태양)
  {
    id: 4,
    title: "4단계: 발표 자료에 대한 비판",
    difficulty: "⭐⭐⭐⭐ 상급 (방어적 공격성 조율)",
    characterName: "태양",
    characterRole: "동아리 부부장",
    hairColor: "#EF4444",
    shirtColor: "#DC2626",
    difficultyDesc: "🔒 태양이가 무례하게 비판하는 이유! 감정적으로 받아치면 아무 단서도 얻지 못합니다.",
    incidentText: "내가 밤새워 만든 발표 슬라이드에 대해 태양이가 부원들 앞에서 '이건 구성도 엉망이고 수치도 틀렸어. 너무 유치한데?'라며 차갑게 지적했다.",
    hiddenClues: [
      { id: 'c1', title: "태양의 수정 메일", text: "태양이가 새벽에 오차 수치 5곳을 세밀하게 교정한 슬라이드 파일", stepUnlocked: false },
      { id: 'c2', title: "심사위원 감점 기준표", text: "심사위원 평가표: '통계 오차 1개당 -10점 감점 (엄격 적용)'", stepUnlocked: false },
      { id: 'c3', title: "태양의 책상 메모", text: "메모: 'oo이 디자인 감각 뛰어남. 통계 출처만 보완하면 1등 확정!'", stepUnlocked: false }
    ],
    truthText: "태양이는 직설적인 말투 때문에 오해받았지만, 심사위원의 깐깐한 기준 때문에 완벽한 발표를 만들어 팀을 1등으로 만들고 싶어 교정본까지 만들어둔 것이었다.",
    turns: [
      {
        turnIndex: 1,
        prompt: "부원들 앞에서 냉정한 비판을 받았다. 첫 반응을 어떻게 보일까?",
        options: [
          { id: '1-0', level: 0, badge: "충동/회피", text: "네가 뭔데 유치하다 마다야?! (노트북 쾅 닫고 나감)", reply: "아니... 사실을 지적한 건데 왜 화를 내...?", exp: 'surprised', score: 0, feedback: "[충동 분노] 자존심에 폭력적 반응을 보였습니다.", unlocksClueId: null },
          { id: '1-1', level: 1, badge: "일방/통제", text: "부원들 앞에서 사람 망신 주니까 좋아? 말투 당장 사과해.", reply: "말투는 미안한데... 슬라이드 오류는 진짜 문제야.", exp: 'sad', score: 5, feedback: "[일방 공격] 말투만 공격하여 피드백 내용을 놓쳤습니다.", unlocksClueId: null },
          { id: '1-2', level: 2, badge: "호혜/타협", text: "태양아, 사람들 앞에서 말하지 말고, 쉬는 시간에 따로 교정본 메일이라도 보내줄래?", reply: "아... 메일? 실은 어제 새벽에 교정본 메일 이미 만들어뒀어.", exp: 'neutral', score: 12, feedback: "[장소 타협] 태양이가 미리 준비한 교정 메일 단서를 발견했습니다!", unlocksClueId: 'c1' },
          { id: '1-3', level: 3, badge: "협력/조율", text: "자료에 문제가 있다고 하니 당황스럽지만, 태양이가 지적한 '수치 오류'가 어떤 건지 들어볼게.", reply: "바로 들어줘서 고마워. 내가 어제 새벽에 직접 교정한 메일 첨부파일 보면서 설명할게.", exp: 'neutral', score: 20, feedback: "[방어 해제] 감정을 누르고 질문하여 태양이의 교정 메일 단서를 확보했습니다!", unlocksClueId: 'c1' }
        ]
      },
      {
        turnIndex: 2,
        prompt: "태양이가 3번 슬라이드의 출처 누락과 수치 오류를 짚어준다.",
        options: [
          { id: '2-0', level: 0, badge: "충동/회피", text: "그렇게 잘 알면 네가 다 고쳐라! 난 안 해!", reply: "같이 완성해야지 왜 그래...", exp: 'sad', score: 0, feedback: "[책임 회피] 작업을 포기했습니다.", unlocksClueId: null },
          { id: '2-1', level: 1, badge: "일방/통제", text: "출처 좀 빠졌다고 '유치하다'는 표현까지 쓰는 건 네 인성 문제야.", reply: "표현이 거칠었던 건 인정해... 하지만 이번 심사위원 기준표가 너무 엄격했어.", exp: 'sad', score: 5, feedback: "[인성 비난] 심사위원 기준표 단서를 살짝 들었습니다.", unlocksClueId: 'c2' },
          { id: '2-2', level: 2, badge: "호혜/타협", text: "그럼 틀린 수치는 태양이 네가 고쳐주고, 난 디자인을 더 가다듬을게.", reply: "좋아! 이번 심사위원 감점 기준표 보니까 통계 오차 감점이 엄청 크더라고.", exp: 'happy', score: 15, feedback: "[역할 분담] 심사위원 감점 기준표 단서를 알아냈습니다!", unlocksClueId: 'c2' },
          { id: '2-3', level: 3, badge: "협력/조율", text: "심사 기준이 엄격해서 감점을 막으려고 급하게 지적한 거였구나?", reply: "맞아! 여기 심사 기준표 보면 통계 오차 감점이 -10점이야. 꼭 감점 막고 싶었어.", exp: 'happy', score: 20, feedback: "[의도 파악] 태양이의 진짜 목적과 심사 기준표 단서를 확인했습니다!", unlocksClueId: 'c2' }
        ]
      },
      {
        turnIndex: 3,
        prompt: "태양이의 진의가 밝혀졌다. 나의 기분과 상대의 의도를 조율하는 대화는?",
        options: [
          { id: '3-0', level: 0, badge: "충동/회피", text: "알았어... 내가 잘 못했네...", reply: "누구의 잘못도 아니야...", exp: 'neutral', score: 0, feedback: "[자책 회피] 과도하게 자책했습니다.", unlocksClueId: null },
          { id: '3-1', level: 1, badge: "일방/통제", text: "의도가 어찌 됐든 다음부터 부원들 앞에서는 무조건 내 편들어줘.", reply: "틀린 자료를 맞다고 할 순 없잖아...", exp: 'sad', score: 5, feedback: "[비현실 요구] 무조건적 편들기 요구.", unlocksClueId: null },
          { id: '3-2', level: 2, badge: "호혜/타협", text: "지적해 준 건 고마워! 대신 다음엔 표현을 더 부드럽게 조심해 줘.", reply: "미안! 직설적 말투 고쳐볼게. 실은 내 메모에도 네 디자인 좋다고 써뒀었어.", exp: 'happy', score: 15, feedback: "[방식 개선] 태양이의 메모 단서를 발견했습니다!", unlocksClueId: 'c3' },
          { id: '3-3', level: 3, badge: "협력/조율", text: "팀의 성공을 위해 깐깐하게 짚어준 정성이 고마워. 나도 비판을 성장의 기회로 볼게!", reply: "oo아! 내 메모에도 'oo이 디자인 감각이 훌륭하다'고 적어뒀는데 알아줘서 진짜 고마워!", exp: 'happy', score: 20, feedback: "[성장 사고] 비판을 승화시키고 태양이의 칭찬 메모 단서까지 해금했습니다!", unlocksClueId: 'c3' }
        ]
      },
      {
        turnIndex: 4,
        prompt: "수정 작업과 팀워크를 위해 어떻게 협력할까?",
        options: [
          { id: '4-0', level: 0, badge: "충동/회피", text: "수정안 완성되면 나한테 카톡이나 보내.", reply: "같이 확인해야 하는데...", exp: 'neutral', score: 0, feedback: "[소극 태도] 관여 축소.", unlocksClueId: null },
          { id: '4-1', level: 1, badge: "일방/통제", text: "교정안대로 내가 고칠 테니 더 이상 토 달지 마.", reply: "완성도를 올리려는 거였는데...", exp: 'sad', score: 5, feedback: "[일방 마무리] 검토 거부.", unlocksClueId: null },
          { id: '4-2', level: 2, badge: "호혜/타협", text: "내일 아침 일찍 만나서 같이 10분간 슬라이드 최종 점검하자!", reply: "좋아! 바짝 검토하면 완벽하겠다!", exp: 'happy', score: 15, feedback: "[실용 조율] 점검 시간을 마련했습니다.", unlocksClueId: null },
          { id: '4-3', level: 3, badge: "협력/조율", text: "태양이의 데이터 분석력과 내 디자인 감각을 합치면 최고의 발표가 될 거야!", reply: "대박! 우리 장점이 합쳐지면 1등 문제없다!", exp: 'happy', score: 20, feedback: "[시너지] 강점을 결합했습니다.", unlocksClueId: null }
        ]
      },
      {
        turnIndex: 5,
        prompt: "대화를 마치며 소통 규칙을 정해보자.",
        options: [
          { id: '5-0', level: 0, badge: "충동/회피", text: "이제 수정하러 가자.", reply: "응 가자!", exp: 'neutral', score: 0, feedback: "[단순 마침] 마침.", unlocksClueId: null },
          { id: '5-1', level: 1, badge: "일방/통제", text: "앞으로 부원들 지적할 때는 내 허락받고 지적해.", reply: "피드백을 통제하는 거야...?", exp: 'sad', score: 5, feedback: "[권위 통제] 권위적인 제한.", unlocksClueId: null },
          { id: '5-2', level: 2, badge: "호혜/타협", text: "앞으로 피드백 줄 땐 칭찬 하나 먼저 하고 지적하기 샌드위치 기법 쓰기다!", reply: "오! 샌드위치 피드백법 좋네!", exp: 'happy', score: 15, feedback: "[구체 규칙] 대화법을 적용했습니다.", unlocksClueId: null },
          { id: '5-3', level: 3, badge: "협력/조율", text: "비판적인 말 뒤에 숨겨진 솔직한 의도를 확인하는 대화가 왜 중요한지 느꼈어!", reply: "나도 따뜻하게 말하도록 노력할게. 고마워!", exp: 'happy', score: 20, feedback: "[심리 성숙] 비판 조율 능력을 완성했습니다.", unlocksClueId: null }
        ]
      }
    ]
  },
  // STAGE 5: EXPERT (하은)
  {
    id: 5,
    title: "5단계: 비밀 고민 유출 사건",
    difficulty: "⭐⭐⭐⭐⭐ 심화 (복합 의도 파악 & 경계 설정)",
    characterName: "하은",
    characterRole: "비밀을 나누던 가장 친한 친구",
    hairColor: "#8B5CF6",
    shirtColor: "#7C3AED",
    difficultyDesc: "🚨 내 비밀이 소문났다! 하은이가 날 배신한 건지 대화로 진짜 진실을 캐내야 합니다.",
    incidentText: "하은이에게만 털어놓았던 내 가정사/진로 고민을, 옆 반 아이가 알고 나에게 은근히 물어보았다. 하은이가 내 비밀을 소문내고 다닌 걸까?",
    hiddenClues: [
      { id: 'c1', title: "하은과 상담교사 톡", text: "하은: '선생님, 친구 oo이가 너무 힘들어하는데 제가 도울 방법이 없을까요?'", stepUnlocked: false },
      { id: 'c2', title: "상담실 방문 기록", text: "하은이가 어제 방과 후 전문 상담교사와 30분간 면담한 기록", stepUnlocked: false },
      { id: 'c3', title: "하은의 다이어리 한 줄", text: "'oo이의 고민을 혼자 감당하려니 너무 무겁다... 전문가에게 도움을 요청해야겠다.'", stepUnlocked: false }
    ],
    truthText: "하은이는 내가 극심한 방황을 겪자 혼자 감당하기 힘들어 전문 상담 선생님께 조언을 구했던 것이다. 그 과정에서 또래상담자인 옆 반 아이가 정황을 접하게 된 것이었다.",
    turns: [
      {
        turnIndex: 1,
        prompt: "내 비밀이 다른 사람 입에서 나왔다. 하은이에게 첫마디를 어떻게 건넬까?",
        options: [
          { id: '1-0', level: 0, badge: "충동/회피", text: "너 진짜 입 가볍다! 소문내니까 재미있냐?! (눈물 흘리며 달아남)", reply: "oo아!! 잠깐만!! 오해야, 소문낸 게 아니야!", exp: 'sad', score: 0, feedback: "[충동 분출] 오해를 파악하지 않고 달아났습니다.", unlocksClueId: null },
          { id: '1-1', level: 1, badge: "일방/통제", text: "너한테만 말한 비밀을 어떻게 옆 반 아이가 알아? 당장 절교야.", reply: "절교라니... oo아 내 말 좀 들어봐 ㅠㅠ", exp: 'sad', score: 5, feedback: "[일방 절교] 해명 기회를 차단했습니다.", unlocksClueId: null },
          { id: '1-2', level: 2, badge: "호혜/타협", text: "하은아, 소문 안 낸 거라고 해명하면 이번 한 번은 믿어줄게.", reply: "소문낸 게 절대 아니야! 상담 선생님 톡으로 문의드린 거였어...", exp: 'surprised', score: 12, feedback: "[조건 유예] 하은이와 상담교사의 톡 문의 단서를 들었습니다.", unlocksClueId: 'c1' },
          { id: '1-3', level: 3, badge: "협력/조율", text: "하은아, 오늘 옆 반 아이한테 내 고민 얘기가 나왔는데, 어떤 경위로 전달된 건지 말해줄 수 있어?", reply: "oo아... 놀랐지? 실은 네가 너무 힘들어해서 내가 혼자 안고 있기 버거워 상담 선생님께 도움 톡을 보냈었어.", exp: 'sad', score: 20, feedback: "[침착 경위] 하은이가 전달한 상담교사 톡 단서를 확보했습니다!", unlocksClueId: 'c1' }
        ]
      },
      {
        turnIndex: 2,
        prompt: "하은이가 날 걱정해서 상담 선생님께 조언을 구하다 일어난 일이라고 한다.",
        options: [
          { id: '2-0', level: 0, badge: "충동/회피", text: "누구한테든 내 허락 없이 말한 건 무조건 나쁜 거잖아!", reply: "너를 돕고 싶어서 그랬는데...", exp: 'sad', score: 0, feedback: "[동기 무시] 배신감만 표출했습니다.", unlocksClueId: null },
          { id: '2-1', level: 1, badge: "일방/통제", text: "날 걱정했든 말든 내 비밀을 알린 시점에서 넌 배신자야.", reply: "배신자라니... 그렇게 말하면 나도 할 말이 없다...", exp: 'sad', score: 5, feedback: "[낙인] 극단적 낙인을 찍었습니다.", unlocksClueId: null },
          { id: '2-2', level: 2, badge: "호혜/타협", text: "의도는 알겠어. 그럼 옆 반 아이한테 가서 비밀 엄수하라고 확실히 말해줄래?", reply: "응 당연하지! 어제 상담실 직접 방문해서 면담할 때도 신신당부했었거든!", exp: 'neutral', score: 15, feedback: "[실질 조치] 하은이의 상담실 방문 기록 단서를 알아냈습니다.", unlocksClueId: 'c2' },
          { id: '2-3', level: 3, badge: "협력/조율", text: "내가 너무 힘들어 보여서 하은이 너도 혼자 안고 있기 마음이 무거웠구나.", reply: "맞아 oo아... 어제 방과 후 상담실에 직접 방문 면담을 신청할 정도로 내 마음도 버거웠어... 미안해.", exp: 'sad', score: 20, feedback: "[중압감 수용] 하은이의 상담실 방문 단서와 심리적 한계를 수용했습니다!", unlocksClueId: 'c2' }
        ]
      },
      {
        turnIndex: 3,
        prompt: "하은이의 의도를 이해했지만 내 비밀이 드러나 상처받았다. 내 감정을 어떻게 표현할까?",
        options: [
          { id: '3-0', level: 0, badge: "충동/회피", text: "됐어... 이제 너한테 아무 말도 안 할래.", reply: "oo아... 제발 입 다물지 말아 줘...", exp: 'sad', score: 0, feedback: "[수동 공격] 대화를 거부했습니다.", unlocksClueId: null },
          { id: '3-1', level: 1, badge: "일방/통제", text: "앞으로는 상담 선생님이든 누구든 내 사생활에 일절 관여하지 마.", reply: "위급해 보여도 모른 척하라는 거야...?", exp: 'sad', score: 5, feedback: "[비현실 통제] 관여를 일방 봉쇄했습니다.", unlocksClueId: null },
          { id: '3-2', level: 2, badge: "호혜/타협", text: "고맙지만 당황스러웠어. 다음엔 날 돕고 싶더라도 꼭 나한테 먼저 허락을 구해줘.", reply: "응, 미안해. 내 다이어리에도 네 허락 먼저 구할 걸 후회한다고 썼었어.", exp: 'happy', score: 15, feedback: "[경계 설정] 하은의 다이어리 단서를 발견했습니다.", unlocksClueId: 'c3' },
          { id: '3-3', level: 3, badge: "협력/조율", text: "날 생각해 준 마음은 고마워. 하지만 알려졌을 때 불안도 컸어. 의도와 결과를 분리해서 생각해 보자.", reply: "oo아 지혜롭게 말해줘서 고마워... 내 다이어리 한 줄에도 네 허락 없이 조언 구한 걸 후회하고 있었어.", exp: 'happy', score: 20, feedback: "[의도 결과 분리] 고차원 조율과 하은의 다이어리 단서를 해금했습니다!", unlocksClueId: 'c3' }
        ]
      },
      {
        turnIndex: 4,
        prompt: "상담실 및 상황을 어떻게 정리할까?",
        options: [
          { id: '4-0', level: 0, badge: "충동/회피", text: "상담실도 안 가고 옆 반 아이도 평생 피할 거야.", reply: "피하기만 하면 해결이 안 되는데...", exp: 'sad', score: 0, feedback: "[회피 반응] 거부했습니다.", unlocksClueId: null },
          { id: '4-1', level: 1, badge: "일방/통제", text: "옆 반 아이한테 가서 당장 내 얘기 언급조차 말라고 소리치고 올 거야.", reply: "공격적으로 가면 일이 더 커질 텐데...", exp: 'sad', score: 5, feedback: "[공격 해결] 갈등 증폭.", unlocksClueId: null },
          { id: '4-2', level: 2, badge: "호혜/타협", text: "하은이 네가 오해였다고 정정해 주고, 나도 상담 선생님과 대화해 볼게.", reply: "좋아! 정정 메시지 바로 보낼게!", exp: 'happy', score: 15, feedback: "[상호 책임] 역할을 분담했습니다.", unlocksClueId: null },
          { id: '4-3', level: 3, badge: "협력/조율", text: "나를 걱정해 준 하은이 마음을 받아들여, 이번 기회에 나도 상담 선생님께 정식으로 털어놔볼게.", reply: "와... oo아! 정말 큰 용기다!", exp: 'happy', score: 20, feedback: "[긍정 승화] 위기를 성장의 기회로 반전시켰습니다.", unlocksClueId: null }
        ]
      },
      {
        turnIndex: 5,
        prompt: "대화를 마치며 깊은 우정 규칙을 완성해 보자.",
        options: [
          { id: '5-0', level: 0, badge: "충동/회피", text: "그래, 가보자.", reply: "응 가자!", exp: 'neutral', score: 0, feedback: "[단순 종료]" },
          { id: '5-1', level: 1, badge: "일방/통제", text: "다시 한번 내 사생활 유출하면 그땐 진짜 끝인 줄 알아.", reply: "명심할게.", exp: 'sad', score: 5, feedback: "[위협 경계] 위협성 경고.", unlocksClueId: null },
          { id: '5-2', level: 2, badge: "호혜/타협", text: "앞으로는 혼자 감당하기 힘든 비밀이면 '감당하기 힘들다'라고 미리 말해주기!", reply: "오케이! 솔직한 한계도 말할게!", exp: 'happy', score: 15, feedback: "[한계 공유] 신호 규칙을 만들었습니다.", unlocksClueId: null },
          { id: '5-3', level: 3, badge: "협력/조율", text: "오늘 대화 덕분에 서운함 뒤에 숨겨진 진심을 읽는 법을 배웠어. 용기 내줘서 고마워!", reply: "우정을 지켜줘서 정말 고마워. 최고의 친구야!", exp: 'happy', score: 20, feedback: "[최고 성숙] 위기를 깊은 우정으로 완성했습니다.", unlocksClueId: null }
        ]
      }
    ]
  }
];

const DynamicCharacterSprite = ({ hairColor, shirtColor, expression }) => {
  const getMouth = () => {
    switch (expression) {
      case 'happy':
        return <path d="M 80 135 Q 100 160 120 135" fill="#F43F5E" stroke="#E11D48" strokeWidth="2" />;
      case 'sad':
        return <path d="M 85 145 Q 100 130 115 145" fill="none" stroke="#475569" strokeWidth="3.5" strokeLinecap="round" />;
      case 'surprised':
        return <ellipse cx="100" cy="140" rx="8" ry="11" fill="#F43F5E" />;
      case 'neutral':
      default:
        return <path d="M 85 140 L 115 140" stroke="#64748B" strokeWidth="3.5" strokeLinecap="round" />;
    }
  };

  const getEyebrows = () => {
    switch (expression) {
      case 'sad':
        return (
          <>
            <path d="M 65 82 L 85 88" stroke="#475569" strokeWidth="3" strokeLinecap="round" />
            <path d="M 135 88 L 115 82" stroke="#475569" strokeWidth="3" strokeLinecap="round" />
          </>
        );
      case 'surprised':
        return (
          <>
            <path d="M 65 75 Q 75 68 85 75" stroke="#475569" strokeWidth="3.5" strokeLinecap="round" />
            <path d="M 115 75 Q 125 68 135 75" stroke="#475569" strokeWidth="3.5" strokeLinecap="round" />
          </>
        );
      case 'happy':
        return (
          <>
            <path d="M 65 80 Q 75 74 85 80" stroke="#475569" strokeWidth="3.5" strokeLinecap="round" />
            <path d="M 115 80 Q 125 74 135 80" stroke="#475569" strokeWidth="3.5" strokeLinecap="round" />
          </>
        );
      case 'neutral':
      default:
        return (
          <>
            <line x1="65" y1="80" x2="85" y2="80" stroke="#475569" strokeWidth="3" strokeLinecap="round" />
            <line x1="115" y1="80" x2="135" y2="80" stroke="#475569" strokeWidth="3" strokeLinecap="round" />
          </>
        );
    }
  };

  return (
    <div className="w-40 h-52 md:w-48 md:h-60 relative mx-auto filter drop-shadow-[0_12px_20px_rgba(16,185,129,0.2)] transition-all duration-300 transform hover:scale-105">
      <svg viewBox="0 0 200 240" className="w-full h-full">
        <path d="M 140 70 C 175 60, 185 110, 165 135 C 150 115, 145 80, 140 70 Z" fill={hairColor} />
        <path d="M 55 185 C 55 155, 145 155, 145 185 L 160 240 L 40 240 Z" fill={shirtColor} />
        <path d="M 85 185 L 100 210 L 115 185 Z" fill="#FFFFFF" />
        <rect x="88" y="148" width="24" height="25" fill="#FED7AA" rx="4" />
        <ellipse cx="100" cy="115" rx="45" ry="48" fill="#FFEDD5" />
        {(expression === 'happy' || expression === 'surprised') && (
          <>
            <ellipse cx="70" cy="123" rx="9" ry="6" fill="#FDA4AF" opacity="0.7" />
            <ellipse cx="130" cy="123" rx="9" ry="6" fill="#FDA4AF" opacity="0.7" />
          </>
        )}
        {expression === 'happy' ? (
          <>
            <path d="M 68 100 Q 75 92 82 100" stroke="#1E293B" strokeWidth="3.5" fill="none" strokeLinecap="round" />
            <path d="M 118 100 Q 125 92 132 100" stroke="#1E293B" strokeWidth="3.5" fill="none" strokeLinecap="round" />
          </>
        ) : (
          <>
            <circle cx="75" cy="98" r="6.5" fill="#1E293B" />
            <circle cx="125" cy="98" r="6.5" fill="#1E293B" />
            <circle cx="77" cy="96" r="2" fill="#FFFFFF" />
            <circle cx="127" cy="96" r="2" fill="#FFFFFF" />
          </>
        )}
        {getEyebrows()}
        {getMouth()}
        <path d="M 50 90 C 40 30, 160 30, 150 90 C 130 65, 70 65, 50 90 Z" fill={hairColor} />
      </svg>
    </div>
  );
};

export default function App() {
  const [selectedStageId, setSelectedStageId] = useState(1);
  const [phase, setPhase] = useState('STAGE_SELECT'); // STAGE_SELECT, INCIDENT, CHAT_GAME, REPORT

  const currentStage = STAGES_DATA.find(s => s.id === selectedStageId) || STAGES_DATA[0];

  const [thought1, setThought1] = useState('');
  const [feeling1, setFeeling1] = useState('');
  const [certainty1, setCertainty1] = useState(80);

  // Clues state for real-time unlocking
  const [stageClues, setStageClues] = useState([]);
  const [showClueModal, setShowClueModal] = useState(false);

  const [currentTurn, setCurrentTurn] = useState(1);
  const [chatLog, setChatLog] = useState([]);
  const [totalScore, setTotalScore] = useState(0);
  const [characterExpression, setCharacterExpression] = useState('neutral');
  const [selectedChoiceHistory, setSelectedChoiceHistory] = useState([]);

  // Free-form Input mode states
  const [inputMode, setInputMode] = useState('CHOICE'); // 'CHOICE' | 'FREE'
  const [freeInputText, setFreeInputText] = useState('');
  const [isAiProcessing, setIsAiProcessing] = useState(false);

  const chatEndRef = useRef(null);

  useEffect(() => {
    chatEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [chatLog]);

  const handleSelectStage = (stageId) => {
    setSelectedStageId(stageId);
    setPhase('INCIDENT');
    setThought1('');
    setFeeling1('');
    setCertainty1(80);
    
    // Reset clues with stepUnlocked: false
    const stg = STAGES_DATA.find(s => s.id === stageId);
    if (stg) {
      setStageClues(stg.hiddenClues.map(c => ({ ...c, stepUnlocked: false })));
    }
    
    setCurrentTurn(1);
    setChatLog([]);
    setTotalScore(0);
    setCharacterExpression('neutral');
    setSelectedChoiceHistory([]);
    setInputMode('CHOICE');
    setFreeInputText('');
  };

  const handleStartChatPhase = () => {
    setPhase('CHAT_GAME');
    setChatLog([
      {
        id: 'sys-start',
        sender: 'system',
        text: `[상황 발생] ${currentStage.incidentText}`
      }
    ]);
  };

  const processTurnOutcome = (option) => {
    let clueNotificationText = null;

    if (option.unlocksClueId) {
      setStageClues(prevClues => 
        prevClues.map(c => {
          if (c.id === option.unlocksClueId && !c.stepUnlocked) {
            clueNotificationText = `🔍 [단서 발견] "${c.title}" 단서를 새로 찾아냈습니다!`;
            return { ...c, stepUnlocked: true };
          }
          return c;
        })
      );
    }

    const userMsg = {
      id: `user-turn-${currentTurn}`,
      sender: 'user',
      text: option.text,
      badge: option.badge,
      time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
    };

    const characterMsg = {
      id: `char-turn-${currentTurn}`,
      sender: 'character',
      text: option.reply,
      expression: option.exp,
      feedback: option.feedback,
      time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
    };

    const newLogs = [userMsg, characterMsg];
    
    if (clueNotificationText) {
      newLogs.push({
        id: `sys-clue-${currentTurn}`,
        sender: 'system',
        text: clueNotificationText,
        isClueAlert: true
      });
    }

    setChatLog(prev => [...prev, ...newLogs]);
    setTotalScore(prev => prev + option.score);
    setCharacterExpression(option.exp);
    setSelectedChoiceHistory(prev => [...prev, option]);

    if (currentTurn < 5) {
      setCurrentTurn(prev => prev + 1);
    } else {
      setTimeout(() => {
        setPhase('REPORT');
      }, 1200);
    }
  };

  const handleSelectOption = (option) => {
    processTurnOutcome(option);
  };

  const handleSendFreeText = async () => {
    if (!freeInputText.trim() || isAiProcessing) return;

    const userText = freeInputText.trim();
    setFreeInputText('');
    setIsAiProcessing(true);

    const lockedClues = stageClues.filter(c => !c.stepUnlocked);

    const systemPrompt = `Act as an interactive communication coach for high school students in a visual novel game.
Target character: ${currentStage.characterName} (${currentStage.characterRole}).
Incident Context: "${currentStage.incidentText}"
Truth of Situation: "${currentStage.truthText}"
Current Turn Number: ${currentTurn} / 5.
Remaining Locked Clues: ${JSON.stringify(lockedClues)}

Analyze the user's free-form input in Korean and evaluate their communication level based on Selman's negotiation framework:
Level 0: Impulse/Avoidance (Hostile, runaway, abrupt cut off)
Level 1: Unilateral/Control (Accusatory, demanding, pressuring)
Level 2: Reciprocal/Bargain (Trade-offs, practical compromise)
Level 3: Collaborative/Coordinating (Empathetic, asking open non-blaming questions to discover context/intent)

Return a strictly valid JSON object:
{
  "level": 0|1|2|3,
  "badge": "충동/회피" | "일방/통제" | "호혜/타협" | "협력/조율",
  "reply": "Character's spoken response in Korean matching character's role and emotional state",
  "exp": "happy" | "sad" | "surprised" | "neutral",
  "score": number from 0 to 20,
  "feedback": "Short educational feedback explaining why this response was evaluated this way (in Korean)",
  "unlocksClueId": "id string of unlocked clue from locked clues list if Level 2 or 3 and relevant to finding truth, otherwise null"
}`;

    try {
      const apiKey = "";
      const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;

      const payload = {
        contents: [
          {
            role: "user",
            parts: [{ text: `User Said: "${userText}"` }]
          }
        ],
        systemInstruction: {
          parts: [{ text: systemPrompt }]
        },
        generationConfig: {
          responseMimeType: "application/json"
        }
      };

      const res = await fetch(apiUrl, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });

      const data = await res.json();
      const rawJson = data.candidates?.[0]?.content?.parts?.[0]?.text;

      if (rawJson) {
        const parsed = JSON.parse(rawJson);
        const aiOption = {
          id: `ai-turn-${currentTurn}`,
          level: parsed.level ?? 2,
          badge: parsed.badge || "자유 답변",
          text: userText,
          reply: parsed.reply || "응... 네 말이 맞아.",
          exp: parsed.exp || "neutral",
          score: parsed.score ?? 10,
          feedback: parsed.feedback || "[AI 분석] 자유 답변 소통 평가",
          unlocksClueId: parsed.unlocksClueId || null
        };
        processTurnOutcome(aiOption);
      } else {
        throw new Error("Invalid AI payload");
      }
    } catch (err) {
      console.error("AI Response error fallback:", err);
      // Fallback fallback if API fails
      const fallbackOption = {
        id: `ai-fallback-${currentTurn}`,
        level: 2,
        badge: "자유 답변",
        text: userText,
        reply: "아... 솔직하게 말해줘서 고마워. 조금 더 자세히 이야기해볼래?",
        exp: "neutral",
        score: 10,
        feedback: "[경청 표현] 솔직하게 의사를 표현했습니다.",
        unlocksClueId: null
      };
      processTurnOutcome(fallbackOption);
    } finally {
      setIsAiProcessing(false);
    }
  };

  const calculateStyleProfile = () => {
    if (selectedChoiceHistory.length === 0) return { title: "충동적/회피형 소통", color: "text-rose-600 bg-rose-50 border-rose-200" };
    const levelCounts = { 0: 0, 1: 0, 2: 0, 3: 0 };
    selectedChoiceHistory.forEach(ch => {
      levelCounts[ch.level] = (levelCounts[ch.level] || 0) + 1;
    });
    
    let maxLevel = 0;
    let maxCount = -1;
    Object.keys(levelCounts).forEach(lvl => {
      if (levelCounts[lvl] >= maxCount) {
        maxCount = levelCounts[lvl];
        maxLevel = Number(lvl);
      }
    });

    switch (maxLevel) {
      case 3: 
        return { title: "협력적/조율형 대화가 (최상위)", color: "text-emerald-700 bg-emerald-50 border-emerald-300", desc: "상대의 의도와 맥락을 깊이 있게 듣고 경청하여 진실과 최고의 조율안을 이끌어냈습니다!" };
      case 2: 
        return { title: "호혜적/타협형 소통가", color: "text-sky-700 bg-sky-50 border-sky-300", desc: "기브앤테이크 조건과 실용적인 타협안으로 갈등을 해결하려는 스타일입니다." };
      case 1: 
        return { title: "일방적/자기주장형 소통가", color: "text-amber-800 bg-amber-50 border-amber-300", desc: "상대를 추궁하거나 비난하는 주장이 강해 핵심 단서를 끌어내는 데 어려움이 있었습니다." };
      case 0:
      default: 
        return { title: "충동적/회피형 소통가", color: "text-rose-700 bg-rose-50 border-rose-300", desc: "오해를 확인하지 않고 자리를 피하거나 분노하여 단서를 놓치고 원만한 소통이 어려웠습니다." };
    }
  };

  const styleProfile = calculateStyleProfile();
  const unlockedClueCount = stageClues.filter(c => c.stepUnlocked).length;

  return (
    <div className="min-h-screen bg-gradient-to-br from-emerald-50 via-teal-50/60 to-sky-50 text-slate-800 flex flex-col font-sans selection:bg-emerald-500 selection:text-white">
      
      {/* HEADER NAVBAR */}
      <header className="border-b border-emerald-100 bg-white/80 backdrop-blur-md sticky top-0 z-40 px-4 md:px-8 py-3.5 flex items-center justify-between shadow-sm">
        <div className="flex items-center gap-3">
          <div className="bg-gradient-to-tr from-emerald-500 to-teal-400 p-2.5 rounded-2xl shadow-md shadow-emerald-500/20 text-white font-black">
            <Brain className="w-6 h-6" />
          </div>
          <div>
            <div className="flex items-center gap-2">
              <h1 className="text-lg md:text-xl font-black bg-gradient-to-r from-emerald-600 via-teal-600 to-indigo-600 bg-clip-text text-transparent">
                마음-로그 (Mind-Log)
              </h1>
              <span className="text-[10px] bg-emerald-100 text-emerald-800 border border-emerald-300 px-2.5 py-0.5 rounded-full font-bold flex items-center gap-1">
                <Sparkle size={10} /> AI 대화 지원
              </span>
            </div>
            <p className="text-xs text-slate-500">대화를 통해 숨겨진 단서를 해금하는 5단계 대화 수련</p>
          </div>
        </div>

        {phase !== 'STAGE_SELECT' && (
          <button 
            onClick={() => setPhase('STAGE_SELECT')}
            className="flex items-center gap-1.5 px-3 py-1.5 bg-white hover:bg-slate-50 text-slate-600 text-xs font-semibold rounded-xl border border-slate-200 shadow-sm transition"
          >
            <RotateCcw size={14} /> 단계 선택
          </button>
        )}
      </header>

      {/* MAIN CONTAINER */}
      <main className="flex-1 max-w-5xl w-full mx-auto p-3 md:p-6 flex flex-col">

        {/* PHASE 0: STAGE SELECT MENU */}
        {phase === 'STAGE_SELECT' && (
          <div className="my-auto space-y-6 max-w-3xl mx-auto w-full py-4">
            <div className="text-center space-y-2">
              <div className="inline-flex p-3 bg-emerald-100 text-emerald-700 rounded-2xl border border-emerald-200">
                <Layers size={32} />
              </div>
              <h2 className="text-2xl md:text-3xl font-black text-slate-900">
                난이도별 대화 수련 시나리오 (1~5단계)
              </h2>
              <p className="text-slate-600 text-xs md:text-sm leading-relaxed max-w-xl mx-auto">
                🔒 **미리 제공되는 단서는 0개입니다!** <br/>
                선택지를 고르거나 **직접 AI 대화창에 문장을 직접 입력**하여 숨겨진 단서를 해금해 보세요!
              </p>
            </div>

            {/* 5 STAGES CARDS */}
            <div className="grid grid-cols-1 gap-3.5">
              {STAGES_DATA.map((stg) => (
                <div 
                  key={stg.id}
                  onClick={() => handleSelectStage(stg.id)}
                  className="bg-white/90 hover:bg-white border border-emerald-100 hover:border-emerald-400 p-5 rounded-2xl shadow-sm hover:shadow-xl transition-all cursor-pointer flex flex-col md:flex-row justify-between items-start md:items-center gap-4 group"
                >
                  <div className="space-y-1.5 flex-1">
                    <div className="flex items-center gap-2">
                      <span className="text-xs font-black px-2.5 py-0.5 rounded-md bg-emerald-100 text-emerald-800 border border-emerald-300">
                        {stg.difficulty}
                      </span>
                      <span className="text-xs font-bold text-slate-500">
                        인물: {stg.characterName} ({stg.characterRole})
                      </span>
                    </div>
                    <h3 className="text-base font-extrabold text-slate-900 group-hover:text-emerald-700 transition">
                      {stg.title}
                    </h3>
                    <p className="text-xs text-slate-600 leading-relaxed line-clamp-2">
                      "{stg.incidentText}"
                    </p>
                    <p className="text-[11px] text-amber-700 font-medium flex items-center gap-1">
                      <Lock size={12} /> {stg.difficultyDesc}
                    </p>
                  </div>

                  <button className="px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-white rounded-xl text-xs font-bold flex items-center gap-1 shrink-0 shadow-md shadow-emerald-600/20 group-hover:scale-105 transition">
                    도전하기 <ChevronRight size={16} />
                  </button>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* PHASE 1: INCIDENT RECORDING */}
        {phase === 'INCIDENT' && (
          <div className="my-auto bg-white/90 border border-emerald-100 rounded-3xl p-6 md:p-8 shadow-xl shadow-emerald-900/5 max-w-xl mx-auto w-full space-y-6 backdrop-blur-md animate-fade-in">
            <div className="flex items-center justify-between border-b border-slate-100 pb-3">
              <div className="flex items-center gap-2 text-emerald-800 font-bold text-lg">
                <AlertCircle className="text-emerald-600" /> 상황 접수 및 내 자동적 생각 기록
              </div>
              <span className="text-xs font-bold bg-emerald-100 text-emerald-800 px-2.5 py-0.5 rounded-full border border-emerald-300">
                {currentStage.difficulty}
              </span>
            </div>

            <div className="bg-emerald-50/60 p-4 rounded-2xl border border-emerald-100 space-y-2">
              <span className="text-xs font-bold text-emerald-800 uppercase tracking-wider block">발생한 애매한 상황</span>
              <p className="text-sm text-slate-800 leading-relaxed font-medium">
                "{currentStage.incidentText}"
              </p>
            </div>

            <div className="space-y-4">
              <div>
                <label className="text-xs font-semibold text-slate-600 mb-1.5 block">이 순간 머릿속에 떠오른 지레짐작 (자동적 사고)</label>
                <input 
                  type="text" 
                  value={thought1} 
                  onChange={e => setThought1(e.target.value)} 
                  placeholder="예: 나를 일부러 무시하고 따돌리는 게 틀림없어!" 
                  className="w-full bg-slate-50/80 border border-slate-200 rounded-xl p-3 text-sm text-slate-800 focus:outline-none focus:bg-white focus:border-emerald-500 transition shadow-inner" 
                />
              </div>

              <div>
                <label className="text-xs font-semibold text-slate-600 mb-1.5 block">솔직한 내 감정 단어</label>
                <input 
                  type="text" 
                  value={feeling1} 
                  onChange={e => setFeeling1(e.target.value)} 
                  placeholder="예: 서운함, 억울함, 화남, 당황스러움" 
                  className="w-full bg-slate-50/80 border border-slate-200 rounded-xl p-3 text-sm text-slate-800 focus:outline-none focus:bg-white focus:border-emerald-500 transition shadow-inner" 
                />
              </div>

              <div>
                <div className="flex justify-between text-xs font-semibold text-slate-600 mb-1.5">
                  <span>내 추측에 대한 확신도</span>
                  <span className="text-emerald-700 font-bold">{certainty1}%</span>
                </div>
                <input 
                  type="range" min="0" max="100" 
                  value={certainty1} 
                  onChange={e => setCertainty1(Number(e.target.value))} 
                  className="w-full accent-emerald-600 cursor-pointer" 
                />
              </div>
            </div>

            <div className="bg-amber-50 border border-amber-200 p-3 rounded-xl text-xs text-amber-900 font-medium flex items-center gap-2">
              <ShieldAlert size={16} className="text-amber-600 shrink-0" />
              <span>선택지를 고르거나, AI 직접 입력창에 내 말을 솔직하게 적어서 대화해보세요!</span>
            </div>

            <button
              disabled={!thought1 || !feeling1}
              onClick={handleStartChatPhase}
              className="w-full bg-emerald-600 hover:bg-emerald-500 disabled:bg-slate-200 disabled:text-slate-400 text-white font-bold py-3.5 rounded-xl transition shadow-lg shadow-emerald-600/20 flex items-center justify-center gap-2"
            >
              대화하러 가기 (0개 단서로 시작) <MessageSquare size={16} />
            </button>
          </div>
        )}

        {/* PHASE 2: VISUAL NOVEL & CHAT WITH AI INPUT */}
        {phase === 'CHAT_GAME' && (
          <div className="flex-1 flex flex-col bg-white/90 border border-emerald-100 rounded-3xl overflow-hidden shadow-2xl relative min-h-[640px] animate-fade-in">
            
            {/* TOP BAR */}
            <div className="bg-white/95 px-6 py-3 border-b border-emerald-100 flex items-center justify-between z-10 shadow-sm">
              <div className="flex items-center gap-3">
                <span className="text-xs font-bold text-emerald-800 bg-emerald-100 px-2.5 py-1 rounded-lg border border-emerald-300">
                  Turn {currentTurn} / 5
                </span>
                <span className="text-xs text-slate-700 font-bold hidden md:inline">
                  {currentStage.characterName} ({currentStage.characterRole})와의 대화
                </span>
              </div>

              <div className="flex items-center gap-2">
                <button
                  onClick={() => setShowClueModal(!showClueModal)}
                  className={`text-xs px-3 py-1.5 rounded-lg border flex items-center gap-1.5 font-bold transition ${
                    unlockedClueCount > 0 
                      ? 'bg-emerald-50 text-emerald-800 border-emerald-300 shadow-sm' 
                      : 'bg-slate-100 text-slate-500 border-slate-200'
                  }`}
                >
                  <KeyRound size={14} /> 해금된 단서 ({unlockedClueCount} / {stageClues.length})
                </button>
              </div>
            </div>

            {/* UNLOCKED CLUE MODAL */}
            {showClueModal && (
              <div className="absolute top-14 left-4 right-4 z-30 bg-white/95 border border-emerald-300 p-4 rounded-2xl backdrop-blur-md shadow-2xl space-y-3 animate-fade-in">
                <div className="flex justify-between items-center border-b border-emerald-100 pb-2">
                  <span className="text-xs font-bold text-emerald-800 flex items-center gap-1">
                    <KeyRound size={14} /> 대화를 통해 발견한 단서 목록
                  </span>
                  <button onClick={() => setShowClueModal(false)} className="text-xs text-slate-500 font-bold">닫기 ✕</button>
                </div>

                <div className="grid grid-cols-1 md:grid-cols-3 gap-2.5 text-xs">
                  {stageClues.map(c => (
                    <div 
                      key={c.id} 
                      className={`p-3 rounded-xl border transition-all ${
                        c.stepUnlocked 
                          ? 'bg-emerald-50 border-emerald-300 text-emerald-950 shadow-sm' 
                          : 'bg-slate-50 border-slate-200 text-slate-400 border-dashed'
                      }`}
                    >
                      <div className="flex items-center justify-between mb-1">
                        <span className="font-bold text-[11px]">
                          {c.stepUnlocked ? `🔓 ${c.title}` : `🔒 잠긴 단서`}
                        </span>
                        {c.stepUnlocked ? <CheckCircle2 size={14} className="text-emerald-600" /> : <Lock size={12} />}
                      </div>
                      <p className="text-[11px] leading-relaxed">
                        {c.stepUnlocked ? c.text : '경청하고 따뜻한 대화를 나누면 단서가 해금됩니다.'}
                      </p>
                    </div>
                  ))}
                </div>
              </div>
            )}

            {/* STAGE & CHAT LOG */}
            <div className="flex-1 bg-gradient-to-b from-emerald-100/70 via-teal-50/80 to-sky-100/70 p-4 md:p-6 flex flex-col md:flex-row gap-4 overflow-hidden relative">
              
              {/* CHARACTER SPRITE */}
              <div className="md:w-1/3 flex flex-col items-center justify-center p-2 z-10 shrink-0 border-b md:border-b-0 md:border-r border-emerald-200/60">
                <DynamicCharacterSprite 
                  hairColor={currentStage.hairColor} 
                  shirtColor={currentStage.shirtColor}
                  expression={characterExpression} 
                />
                <div className="text-center mt-2 space-y-1">
                  <span className="bg-white/90 text-emerald-800 text-xs font-bold px-3 py-1 rounded-full border border-emerald-300 inline-block shadow-sm">
                    {currentStage.characterName} ({currentStage.characterRole})
                  </span>
                </div>
              </div>

              {/* CHAT LOG */}
              <div className="md:w-2/3 flex-1 flex flex-col justify-between overflow-hidden bg-white/70 rounded-2xl border border-emerald-100 p-4 backdrop-blur-md shadow-inner">
                <div className="flex-1 overflow-y-auto space-y-3 pr-2 scrollbar-thin scrollbar-thumb-emerald-200">
                  {chatLog.map((msg) => {
                    if (msg.sender === 'system') {
                      return (
                        <div key={msg.id} className="text-center my-2">
                          <span className={`text-[11px] px-3 py-1 rounded-full font-bold inline-block shadow-sm ${
                            msg.isClueAlert 
                              ? 'bg-amber-100 text-amber-900 border border-amber-300 animate-bounce' 
                              : 'bg-slate-200/80 text-slate-600'
                          }`}>
                            {msg.text}
                          </span>
                        </div>
                      );
                    }

                    const isUser = msg.sender === 'user';
                    return (
                      <div 
                        key={msg.id} 
                        className={`flex flex-col ${isUser ? 'items-end' : 'items-start'} space-y-1 animate-fade-in`}
                      >
                        <div className="flex items-center gap-1.5 text-[10px] text-slate-500 font-semibold px-1">
                          <span>{isUser ? '나' : currentStage.characterName}</span>
                          {isUser && msg.badge && (
                            <span className="bg-slate-100 text-slate-700 px-1.5 py-0.5 rounded border border-slate-300 font-semibold">
                              {msg.badge}
                            </span>
                          )}
                        </div>
                        
                        <div 
                          className={`max-w-[85%] p-3.5 rounded-2xl text-xs md:text-sm leading-relaxed shadow-md ${
                            isUser 
                              ? 'bg-emerald-600 text-white rounded-tr-none shadow-emerald-600/20' 
                              : 'bg-white text-slate-800 border border-slate-200 rounded-tl-none'
                          }`}
                        >
                          {msg.text}
                        </div>

                        {!isUser && msg.feedback && (
                          <div className="text-[10px] bg-emerald-50 text-emerald-900 border border-emerald-200 px-3 py-1.5 rounded-xl mt-1 max-w-[85%] flex items-start gap-1.5 shadow-sm">
                            <Sparkles size={13} className="shrink-0 mt-0.5 text-emerald-600" />
                            <span>{msg.feedback}</span>
                          </div>
                        )}
                      </div>
                    );
                  })}
                  {isAiProcessing && (
                    <div className="flex items-center gap-2 text-xs text-emerald-700 bg-emerald-50 p-3 rounded-xl border border-emerald-200 animate-pulse">
                      <Bot size={16} /> AI 가 {currentStage.characterName}의 반응을 생성하고 분석 중입니다...
                    </div>
                  )}
                  <div ref={chatEndRef} />
                </div>
              </div>
            </div>

            {/* BOTTOM INPUT DOCK WITH MODE SWITCHING */}
            <div className="bg-white border-t border-emerald-100 p-4 md:p-5 z-20 shadow-lg">
              {currentTurn <= 5 ? (
                <div className="space-y-3 max-w-3xl mx-auto">
                  
                  {/* MODE TOGGLE SWITCH */}
                  <div className="flex items-center justify-between text-xs border-b border-slate-100 pb-2">
                    <span className="font-bold text-emerald-700 flex items-center gap-1">
                      Turn {currentTurn} 대화 방식 선택
                    </span>
                    <div className="flex items-center gap-1 bg-slate-100 p-1 rounded-xl border border-slate-200">
                      <button 
                        onClick={() => setInputMode('CHOICE')}
                        className={`px-2.5 py-1 rounded-lg text-[11px] font-bold transition ${
                          inputMode === 'CHOICE' ? 'bg-white text-emerald-800 shadow-sm' : 'text-slate-500'
                        }`}
                      >
                        선택지 선택
                      </button>
                      <button 
                        onClick={() => setInputMode('FREE')}
                        className={`px-2.5 py-1 rounded-lg text-[11px] font-bold transition flex items-center gap-1 ${
                          inputMode === 'FREE' ? 'bg-emerald-600 text-white shadow-sm' : 'text-slate-500'
                        }`}
                      >
                        <Bot size={12} /> AI 직접 입력
                      </button>
                    </div>
                  </div>

                  {/* OPTION A: PREDEFINED CHOICES */}
                  {inputMode === 'CHOICE' ? (
                    <div className="grid grid-cols-1 gap-2">
                      {currentStage.turns[currentTurn - 1]?.options.map((opt) => (
                        <button
                          key={opt.id}
                          onClick={() => handleSelectOption(opt)}
                          className="w-full text-left p-3 bg-slate-50 hover:bg-emerald-50 border border-slate-200 hover:border-emerald-400 rounded-xl text-xs md:text-sm text-slate-800 transition-all flex items-start gap-3 group shadow-sm"
                        >
                          <span className="text-[10px] font-bold px-2 py-0.5 rounded border shrink-0 mt-0.5 bg-white border-slate-300 text-slate-600 group-hover:border-slate-400">
                            {opt.badge}
                          </span>
                          <span className="leading-snug flex-1 font-medium">{opt.text}</span>
                        </button>
                      ))}
                    </div>
                  ) : (
                    /* OPTION B: AI FREE FORM TEXT INPUT */
                    <div className="space-y-2">
                      <p className="text-[11px] text-slate-500">
                        {currentStage.characterName}에게 말하고 싶은 대사를 자유롭게 작성하세요. AI가 반응과 단서 해금 여부를 판정합니다.
                      </p>
                      <div className="flex gap-2">
                        <textarea
                          rows={2}
                          value={freeInputText}
                          onChange={(e) => setFreeInputText(e.target.value)}
                          placeholder="예: 지우야 내 이름이 빠져서 당황했어. 혹시 어떤 이유가 있었는지 말해줄 수 있니?"
                          className="flex-1 bg-slate-50 border border-slate-200 rounded-xl p-2.5 text-xs md:text-sm focus:outline-none focus:bg-white focus:border-emerald-500 transition resize-none"
                        />
                        <button
                          disabled={!freeInputText.trim() || isAiProcessing}
                          onClick={handleSendFreeText}
                          className="bg-emerald-600 hover:bg-emerald-500 disabled:bg-slate-200 text-white font-bold px-4 rounded-xl text-xs flex items-center justify-center gap-1 shadow-md transition"
                        >
                          <Send size={16} /> 전송
                        </button>
                      </div>
                    </div>
                  )}

                </div>
              ) : (
                <div className="text-center py-2 text-xs text-emerald-700 font-bold">
                  5회의 대화가 완료되었습니다. 최종 대화 분석 리포트로 이동합니다...
                </div>
              )}
            </div>

          </div>
        )}

        {/* PHASE 3: FINAL REPORT */}
        {phase === 'REPORT' && (
          <div className="my-auto bg-white/90 border border-emerald-100 rounded-3xl p-6 md:p-8 shadow-xl shadow-emerald-900/5 max-w-2xl mx-auto w-full space-y-6 backdrop-blur-md animate-fade-in">
            
            <div className="text-center space-y-2">
              <div className="inline-flex p-3 bg-emerald-100 rounded-full border border-emerald-300 text-emerald-700">
                <UserCheck size={36} />
              </div>
              <h2 className="text-2xl font-black text-slate-900">
                대화 분석 및 인사이트 리포트
              </h2>
              <p className="text-xs text-slate-500">{currentStage.title} 대화 분석 결과</p>
            </div>

            {/* STYLE PROFILE */}
            <div className="bg-slate-50 p-5 rounded-2xl border border-slate-200 space-y-3">
              <div className="flex justify-between items-center border-b border-slate-200 pb-2">
                <span className="text-xs text-slate-500 font-semibold">나의 주요 소통 스타일</span>
                <span className={`text-xs font-bold px-3 py-1 rounded-full border ${styleProfile.color}`}>
                  {styleProfile.title}
                </span>
              </div>
              <p className="text-sm text-slate-800 leading-relaxed font-semibold">
                {styleProfile.desc}
              </p>
              <div className="flex justify-between items-center text-xs text-emerald-700 font-bold border-t border-slate-200 pt-2">
                <span>획득한 대화 점수: {totalScore} / 100점</span>
                <span>발견한 단서: {unlockedClueCount} / {stageClues.length}개</span>
              </div>
            </div>

            {/* TRUTH REVEAL */}
            <div className="bg-emerald-50 border border-emerald-200 p-4 rounded-2xl text-xs text-emerald-900 leading-relaxed space-y-1">
              <span className="font-bold text-emerald-800 block flex items-center gap-1">
                <Info size={14} /> 대화를 통해 드러난 실제 진실:
              </span>
              <p>{currentStage.truthText}</p>
            </div>

            {/* HISTORY BREAKDOWN */}
            <div className="bg-slate-50 p-4 rounded-2xl border border-slate-200 space-y-2 text-xs">
              <span className="font-bold text-slate-700 block mb-1">턴별 대화 기록 및 피드백:</span>
              <div className="space-y-2 max-h-44 overflow-y-auto pr-1">
                {selectedChoiceHistory.map((ch, idx) => (
                  <div key={idx} className="bg-white p-2.5 rounded-xl border border-slate-200 space-y-1 shadow-sm">
                    <div className="flex justify-between items-center">
                      <span className="font-bold text-slate-700">Turn {idx + 1}</span>
                      <span className="text-[10px] font-bold text-emerald-700">{ch.badge}</span>
                    </div>
                    <p className="text-slate-600 text-[11px]">"{ch.text}"</p>
                    <p className="text-slate-500 text-[10px]">{ch.feedback}</p>
                  </div>
                ))}
              </div>
            </div>

            <div className="flex gap-3">
              <button
                onClick={() => setPhase('STAGE_SELECT')}
                className="flex-1 bg-emerald-600 hover:bg-emerald-500 text-white font-bold py-3.5 rounded-xl transition shadow-lg shadow-emerald-600/20 flex items-center justify-center gap-2"
              >
                <RotateCcw size={16} /> 다른 단계 도전하기
              </button>
            </div>

          </div>
        )}

      </main>

      {/* FOOTER */}
      <footer className="border-t border-emerald-100 py-3.5 px-6 text-center text-xs text-slate-500 bg-white/60 backdrop-blur-md">
        CBT 및 인지재구성 기반 청소년 대인관계 수련 비주얼 노벨 | 마음-로그
      </footer>

    </div>
  );
}
