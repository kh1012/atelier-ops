# atelier-ops

Atelier 설치 앱의 상태 확인 2순위 파일을 두는 공개 저장소다.

앱은 먼저 사내 배포 서버(`/health`)에 묻는다. 거기에 못 닿을 때만 이 저장소의 `gate.json` 을 읽는다
(`https://raw.githubusercontent.com/kh1012/atelier-ops/main/gate.json`). 배포 PC 가 사내망 밖에 있어도 차단을 끄고
켤 수 있게 하려고 둔다.

## gate.json

| 칸                    | 뜻                                                                                     |
| --------------------- | -------------------------------------------------------------------------------------- |
| `gate`                | 차단 스위치. `false` 면 어떤 경우에도 막지 않는다                                      |
| `maintenance.on`      | `gate` 가 `true` 일 때 점검으로 막는다                                                 |
| `maintenance.message` | 점검으로 막을 때 앱에 띄울 사유                                                        |
| `graceHours`          | `gate` 가 `true` 일 때, 마지막 연결 뒤 이 시간이 지나면 막는다. 1 ~ 336 사이의 정수     |
| `updatedAt` · `note`  | 판정에 안 쓴다                                                                         |

모양이 틀린 파일(예: `gate` 가 참 · 거짓이 아님)은 못 읽은 것과 같게 본다. 그 파일로는 막지도 풀지도 않는다.

## 고치는 법

- 배포 PC 에서 `pnpm atelier:ops gate on|off` · `maintenance on "사유"|off` · `grace <시간>` 을 하면 이 파일도 함께
  고쳐진다. `pnpm atelier:ops mirror` 로 올라간 내용이 지금 상태와 같은지 본다
- github.com 에서 손으로 고쳐도 된다. `{"gate": false}` 처럼 줄여 써도 읽는다
- raw 는 CDN 이 몇 분 묵힌다. 고친 값이 앱에 닿기까지 10분쯤 걸릴 수 있다

## 알아 둘 것

- 서명이 없다. 이 저장소의 쓰기 권한이 믿음의 근거다
- 앱이 이 파일을 읽는 것은 0.1.87 부터다. 0.1.86 부터의 앱은 차단 자체를 기본으로 걸지 않는다(정책이 정해질 때까지)
- 설계: maxflow `apps/atelier/docs/2026-09-30-health-gate-design.md` 15절
