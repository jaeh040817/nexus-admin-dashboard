# nexus-admin-dashboard

1. 기능의 목적
배재대학교 경영대학 학술제 운영진이 행사 진행 상황(현재 입장 인원, 투표 현황, 심사 진행률 등)을 한눈에 파악하고, 돌발 상황에 신속하게 대응할 수 있도록 돕는 중앙 통제 화면입니다.

2. 사용자
배재대학교 경영대학 학술제 운영진 및 관리자 (일반 학생/참가자는 접근 불가)

3. 사용자 흐름
관리자 계정으로 로그인합니다.

대시보드 페이지(/admin/dashboard)에 접속합니다.

상단의 StatCard(요약 카드)를 통해 전체 입장객 수와 투표율을 빠르게 확인합니다.

스크롤을 내리며 CheckInStatus, VoteStatus 등 세부 현황을 모니터링합니다.

4. 필요한 화면
대시보드 메인 화면 (단일 페이지): 여러 페이지로 이동할 필요 없이, 하나의 화면 안에 모든 현황 상자(컴포넌트)들이 바둑판처럼 배치된 형태.

5. React Component 구조 (화면 조각 설계)
💡 설명: 화면을 레고 블록처럼 여러 조각으로 나누어 조립하는 방식입니다.

DashboardPage (대시보드 전체를 담는 큰 판)

StatCardList (상단 요약 박스들을 묶어주는 역할)

StatCard (총 입장객 수, 총 투표수 등을 보여주는 아주 작은 네모 박스)

CheckInStatus (시간대별 입장 현황이나 현재 QR 체크인 상태를 보여주는 박스)

VoteStatus (실시간 투표 참여율을 보여주는 박스)

JudgeStatus (심사위원들의 채점표 제출 현황을 보여주는 박스)

TeamStatus (각 참가 팀의 발표 준비 및 진행 상태를 보여주는 박스)

6. Next.js Route 구조 (웹사이트 주소 설계)
💡 설명: 이 대시보드 화면에 접속하기 위한 인터넷 주소(URL)를 정합니다.

app/admin/dashboard/page.tsx

접속 주소: [웹사이트주소.com/admin/dashboard](https://웹사이트주소.com/admin/dashboard)

7. TypeScript Type (데이터 규칙)
💡 설명: 화면에 띄울 데이터가 '숫자'인지 '글자'인지 미리 엄격하게 규칙을 정해두는 것입니다.

TypeScript
// 전체 통계 데이터 규칙
type DashboardStats = {
  totalCheckIn: number; // 총 입장객 (숫자)
  totalVotes: number;   // 총 투표수 (숫자)
  judgingProgress: number; // 심사진행률 퍼센트 (숫자)
};

// 팀 상태 데이터 규칙
type TeamStatusInfo = {
  teamId: string;    // 팀 고유번호 (글자)
  teamName: string;  // 팀명 (글자)
  isReady: boolean;  // 준비완료 여부 (참/거짓)
};

8. 데이터 모델 (가짜 데이터 형태)
💡 설명: 실제 데이터베이스 연결 전, 화면에 임시로 띄워둘 가짜 데이터(Mock Data)입니다.

mockDashboardData.ts 파일 생성

입장 인원: 124명

투표 현황: 총 200명 중 150명 투표 완료 (75%)

심사 제출 현황: 심사위원 5명 중 3명 제출 완료

팀별 상태: '알파팀(심사완료)', '베타팀(심사대기중)' 등

9. Supabase Table/API 필요사항
💡 설명: 나중에 가짜 데이터를 버리고 '진짜 데이터베이스(Supabase)'를 연결할 때 필요한 창고 이름입니다.

필요한 테이블 (데이터 창고): users(사용자), check_ins(입장기록), votes(투표기록), evaluations(심사기록), teams(팀정보)

API 필요사항: 위 테이블들에서 '현재 개수'와 '상태'만 빠르게 계산해서 가져오는 읽기 전용 기능(GET)이 필요합니다.

10. 정상 시나리오
관리자가 대시보드에 접속하면 로딩 빙글빙글(스피너)이 1초 정도 돌고 난 뒤, 가짜 데이터(Mock Data)가 적용된 5개의 컴포넌트가 화면에 예쁘게 배치되어 나타납니다.

11. 오류/예외 시나리오
데이터가 없을 때: 아직 행사 시작 전이라 투표나 체크인 데이터가 '0'일 경우, 화면이 깨지지 않고 "아직 수집된 데이터가 없습니다"라고 친절하게 표시됩니다.

불러오기 실패: 서버 에러로 데이터를 가져오지 못하면 "데이터를 불러오는 중 오류가 발생했습니다. 새로고침을 눌러주세요."라는 에러 박스가 뜹니다.

12. 권한 및 보안 검증
일반 학생이나 로그인을 하지 않은 사람이 /admin/dashboard 주소로 치고 들어오면, 화면을 보여주지 않고 즉시 '로그인 페이지'나 '메인 홈'으로 강제로 쫓아냅니다(Redirect).

13. 모바일/PC 반응형 설계
💡 설명: 스마트폰으로 볼 때와 컴퓨터로 볼 때 화면 배치가 어떻게 달라지는지 정합니다.

PC 화면: 화면이 넓으므로 StatCard 4개가 가로로 한 줄에 나란히 보이고, 아래 현황 박스들도 바둑판처럼 2열/3열로 배치됩니다.

모바일 화면: 화면이 좁으므로 모든 카드와 현황 박스가 세로로 한 줄(1열)로 길게 나열되어, 스크롤을 위아래로 내리면서 확인할 수 있게 됩니다.

14. 공식 Repository로 이식하기 쉬운 Directory 구조
💡 설명: 나중에 팀 폴더로 통째로 옮기기 쉽도록 처음부터 약속된 폴더 구조로 정리합니다.

Plaintext
/app
  /admin/dashboard
    page.tsx        <-- 대시보드 최종 조립 화면
/features
  /dashboard
    /components     <-- StatCard, CheckInStatus 등 화면 조각들 보관
    /types          <-- TypeScript 규칙 보관
    /api            <-- 데이터 불러오는 로직 (임시로 Mock Data 로직 보관)

15. 구현 STEP 1~N (작업 순서)
STEP 1 (10/8 완료): 개인 깃허브 Repository 생성 및 README(설계서) 작성

STEP 2: Next.js 초기 프로젝트 세팅 및 폴더 구조 만들기

STEP 3: 화면에 띄울 가짜 데이터(mockData.ts) 만들기

STEP 4: 5개의 필수 컴포넌트(StatCard 등)의 빈 껍데기와 테두리 만들기 (UI 기초)

STEP 5: 빈 껍데기에 가짜 데이터 연결해서 글자/숫자 띄우기

STEP 6: Tailwind CSS를 사용해 색상 입히고, 모바일/PC 크기 조절(반응형) 테스트하기 (10/11 Prototype 마감 목표)

16. 직접 검증 가능한 테스트 체크리스트
[ ] 브라우저 주소창에 /admin/dashboard를 입력했을 때 오류 없이 접속되는가?

[ ] StatCard, CheckInStatus, VoteStatus, JudgeStatus, TeamStatus 5개가 모두 화면에 보이는가?

[ ] 가짜 데이터(Mock Data)에 적어둔 숫자가 화면에 똑같이 출력되는가?

[ ] 인터넷 브라우저 창 크기를 줄여서 모바일 크기로 만들었을 때, 글자가 잘리거나 깨지지 않고 세로로 잘 정렬되는가?
