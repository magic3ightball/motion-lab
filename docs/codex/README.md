# Motion Lab — 핸드오프

**목표**: 셰이더 + 이징/타이밍을 "만지면서" 배우는 놀이터. 이론 최소, 슬라이더와 A/B 비교로 체감.

## 구조
- `index.html` 한 파일. 빌드 없음, 순수 WebGL2 (풀스크린 삼각형 + 프래그먼트 셰이더).
- 이징은 JS에서 함수로 만들어 **256칸 LUT(R32F 텍스처)** 로 셰이더에 전달 → JS 커브 에디터와 셰이더가 항상 같은 커브를 씀. A/B 두 장.
- 시간: `st.T`(현재 사이클 안의 시간), `st.cycle`(사이클 번호). 사이클 길이 = 지속시간 + 스태거 + HOLD(0.6s).
  - 요소별 진행도 `prog(delay) = clamp((T - delay)/dur)`.
  - 타일은 사이클마다 새 색이 덮고, 덩어리는 짝수=합체/홀수=분리라서 이음새 없이 반복됨.
- 커브: cubic-bezier(핸들 드래그, y는 -0.45~1.45로 오버슈트 허용) 또는 spring(출렁임 → 감쇠비).

## 장면
1. 타일: `fract(uv*N)` 칸 분할, 스태거 방향 4종 (대각선/중심/랜덤/줄)
2. 파문: `length()` 링 6개, 반지름만 이징, 투명도는 linear
3. 덩어리: SDF 두 개 + `smin`, 속도 기반 squash & stretch

## 실행
`.claude/launch.json`의 `motion-lab` (python http.server, 포트 8916).

## 검증된 것
- 세 장면 렌더, A/B 분할, spring 프리셋, 스크럽 정지 (데스크톱 폭에서 스크린샷으로 확인).
- 모바일 375px: 가로 스크롤 없음. 캔버스 픽셀은 JS로 확인했지만 에뮬레이션 스크린샷에는 캔버스가 비어 보임(캡처 한계로 추정).
- 주의: 브라우저 패널이 백그라운드면 rAF가 느려져서 스크린샷이 늦게 갱신됨.

## 범위 밖 (나중에)
영상 내보내기, 설정 저장·공유, 소리 반응, 장면 추가.

## 배포 (Vercel)
- 공개 주소: https://ease-lab.vercel.app (프로젝트 `motion-lab`에 도메인 연결)
- 재배포: 이 폴더에서 `npx vercel@latest deploy --prod --yes`
- `.vercelignore`로 `docs/`는 제외. 자동 주소 `motion-lab-*.vercel.app`은 Vercel 로그인 보호가 걸려 있음.
- `motion-lab.vercel.app`은 다른 사람 소유라서 사용 불가.
