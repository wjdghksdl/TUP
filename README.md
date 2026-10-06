# 🤝 TUP - 공모전 팀 매칭 서비스 플랫폼


> **희망 공모전, 역할군(포지션), 기술 스택을 기반으로 공모전 팀원을 매칭해주는 웹 서비스입니다.** 단순한 게시판 형태의 구인을 넘어, 랜덤 자동 매칭(AutoTeamUp)과 조건 기반 수동 매칭(OpenTeamUp) 두 가지 방식을 제공하고, 양측의 동의/승인을 거쳐 팀을 확정하는 팀 빌딩 경험을 제공합니다.

<img width="2752" height="1536" alt="Gemini_Generated_Image_67u2ue67u2ue67u2" src="https://github.com/user-attachments/assets/3778088d-f0c1-4cbf-aa52-7867167f1d2a" />

## 🛠 Tech Stack

- **Backend:** Python, Django, Django REST Framework (DRF)
- **Database:** MySQL 8.0
- **Frontend:** React, JavaScript, CSS
- **Infrastructure & DevOps:** Docker, Docker Compose

## 📁 Repository Structure (Monorepo)

프론트엔드와 백엔드가 분리된 모노레포(Monorepo) 구조로 구성되어 있으며, `docker-compose`를 통해 전체 환경을 한 번에 실행할 수 있도록 설계했습니다.

- `/backend`: Django 기반의 RESTful API 서버 및 데이터베이스 모델링
- `/frontend`: React 기반의 SPA(Single Page Application) 사용자 인터페이스
- `docker-compose.yml`: 컨테이너 환경 통합 설정 파일

<img width="961" height="640" alt="image" src="https://github.com/user-attachments/assets/16e8f399-17e7-4d25-a193-50ada90c6924" />


## ⚙️ 핵심 구현 기능 (Backend & Architecture)

### 1. 팀 빌딩 및 매칭 RESTful API 설계

- 사용자 기본 정보, 기술 스택, 희망 역할군(메인/서브 포지션) 데이터를 등록하고 조회하는 API를 구축했습니다.
- 공모전 정보를 조회하고, 희망 공모전·역할군 조건에 맞는 팀원을 탐색/필터링하는 기능을 RESTful API 규격에 맞게 구현했습니다.
- 매칭 요청, 수락, 거절(동의/비동의)에 따른 상태 변화를 클라이언트(React)와 JSON 형태로 원활하게 통신할 수 있도록 서버 응답을 최적화했습니다.

매칭 방식은 두 가지로 나뉩니다.

**AutoTeamUp (자동 매칭)** — 대기열 진입 → 랜덤 팀 그룹화 → 2차 피드백(동의/비동의, 24시간 미응답 시 자동 비동의 처리) → 전원 동의 시 팀 확정

<img width="1376" height="768" alt="Gemini_Generated_Image_hgeh63hgeh63hgeh" src="https://github.com/user-attachments/assets/a1452f0b-45b0-432a-bd2a-5d750f666a1d" />



<img width="590" height="595" alt="image" src="https://github.com/user-attachments/assets/86e3e4db-f1f8-48f7-829f-9172ac530213" />


**OpenTeamUp (수동 매칭)** — 팀장/팀원 플로우로 구분되며, 팀장은 모집 역할군·공모전 분야·인원수·한 줄 소개·기술 스택을 입력해 팀을 생성하고 대기열의 팀원을 탐색·초대하며, 팀원은 팀장 리스트를 탐색·신청하는 양방향 승인 구조입니다. 수락 시 팀원이 합류하며, 4명이 구성되면 팀이 확정됩니다.

<img width="752" height="419" alt="image" src="https://github.com/user-attachments/assets/f6047f60-026a-4643-a181-37a4d02d5663" />



![Uploading image.png…]()



### 2. 관계형 데이터베이스(RDB) 설계 및 모델링

- 유저(User), 프로필(Profiles), 공모전(Competitions), 매칭 대기열(MatchingQueue), 팀(Teams) 등 13개 테이블로 매칭 비즈니스 로직을 RDB 구조로 도출했습니다.
- 유저와 팀의 다대다(N:M) 관계는 중간 테이블(TeamMembers)로 풀고, 매칭 과정은 대기열(MatchingQueue)과 이력(MatchingLogs: 매칭 타입·결과·재매칭 라운드·실패 사유)으로 분리해 데이터 정합성과 추적성을 확보했습니다.
- 팀 확정 이후의 기능(채팅방/메시지, 팀 피드백, 팀원 평가, 알림, 신고)을 별도 테이블로 분리해 확장 가능하도록 설계했습니다.

<img width="1067" height="633" alt="image" src="https://github.com/user-attachments/assets/8ba8d082-1622-441a-aad5-a27d9908057a" />


### 3. Docker 기반 독립적 개발/테스트 환경 구축

- `Dockerfile` 및 `docker-compose.yml`을 작성하여 프론트엔드(React), 백엔드(Django), 데이터베이스(MySQL)를 각각의 독립된 컨테이너로 분리했습니다.
- MySQL 헬스체크(`service_healthy`)를 적용해 DB가 준비된 이후 백엔드가 기동되도록 구성했습니다.
- 사전 준비(`backend/.env` 작성, `db/TUP_project.sql` 초기화 스크립트 배치) 후 명령어 한 줄(`docker-compose up --build`)로 운영체제(OS)에 구애받지 않고 동일한 개발/테스트 환경을 세팅할 수 있도록 구성했습니다.

## 🚀 기술적 고민 및 트러블슈팅 (Future Work)

### 1. N+1 문제 해결 및 쿼리 최적화 도입 필요성

- **상황:** 팀 목록과 각 팀의 구성원(TeamMembers) 및 프로필(Profiles)을 함께 조회하는 API처럼 연관 테이블을 순회하는 조회에서 **N+1 문제**가 발생할 가능성을 확인했습니다.
- **개선 계획:** 현재 데모 규모에서는 작동에 무리가 없으나, 데이터가 확장될 경우 데이터베이스 병목이 예상됩니다. 추후 Django ORM의 `select_related`(정방향 참조)와 `prefetch_related`(역방향 참조)를 적용하여 데이터베이스 I/O 횟수를 줄일 예정입니다.

### 2. 매칭 알고리즘 고도화 설계

- **상황:** 현재 자동 매칭은 희망 공모전과 역할군을 기준으로 대기열에서 랜덤하게 팀을 그룹화한 뒤, 팀원들의 2차 피드백(동의/비동의)으로 확정하는 방식입니다. 프로젝트 기간상 사용자 성향·활동 이력을 반영한 추천까지는 구현하지 못했습니다.
- **개선 계획:** 향후 팀 피드백·평가(Evaluation) 데이터가 누적되면, 이를 활용해 사용자 성향과 협업 이력을 반영한 추천 방식으로 매칭 로직을 확장할 계획입니다.
