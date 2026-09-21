🎙️ Voice Lab (상황 맞춤형 AI 스피치 분석 시스템)
FastAPI와 React를 기반으로 구축된 풀스택 웹 애플리케이션입니다. 사용자의 음성을 입력받아 음향적 특성(Prosody)과 발화 구조(NLP)를 다각도로 분석한 뒤, 면접·발표·일반 대화 등 상황에 최적화된 피드백을 제공합니다.

🛠 Tech Stack
Backend: FastAPI, Python

AI & Audio Processing: OpenAI Whisper, librosa, spaCy, pydub

Frontend: React (JavaScript)

⚙️ 핵심 백엔드 및 AI 구현 기능
1. 비동기 기반 오디오 전처리 파이프라인
FastAPI의 UploadFile을 통해 클라이언트로부터 오디오 스트림을 수신합니다.

tempfile과 pydub을 활용하여 다양한 포맷의 음성 데이터를 백엔드 서버에서 16kHz, Mono 채널의 통일된 WAV 파일로 안전하게 전처리합니다.

2. 다각도 음향 프로파일링 (librosa)
Speed (속도): Onset 강도와 Tempo를 분석해 발화의 물리적 속도(BPM) 측정.

Intonation (억양): YIN 알고리즘으로 목소리 높낮이(Pitch/F0)의 표준편차를 도출하여 단조로움/생동감 수치화.

Habit (습관): RMS(음량) 데이터를 통해 특정 임계값 이하의 묵음 구간 비율을 역산하여 주저함이나 말더듬 구간 감지.

3. NLP 기반 발화 구조 분석 (Whisper + spaCy)
Whisper 모델을 통해 전처리된 음성 데이터를 텍스트로 변환(STT)합니다.

spaCy를 활용해 텍스트를 문장 단위로 토큰화하고, '문장당 평균 단어 수(avg_sent_len)'를 계산하여 발화가 지나치게 단답형이거나 장황한지 구조적 안정성을 평가합니다.

4. 상황별(Role) 동적 채점 알고리즘 적용
면접(침착함), 발표(에너지), 대화(티키타카) 등 사용자가 설정한 상황에 따라 타겟 BPM과 허용 묵음(Silence) 비율 기준값을 동적으로 변경하여 상황에 맞는 맞춤형 정량 평가와 피드백 코멘트를 반환합니다.

🚀 회고 및 추후 개선 과제 (Future Work)
비동기 워커 도입 검토: 현재 동기적으로 동작하는 Whisper 모델 추론 및 오디오 분석 로직으로 인해 요청이 몰릴 경우 응답 지연이 발생할 수 있습니다. 이를 Celery나 FastAPI BackgroundTasks로 분리하여 병목 현상을 해소하고자 합니다.

파일 입출력(I/O) 최적화: 현재 로컬 디스크에 임시 파일을 생성해 처리하는 방식을, 메모리 상에서 바이트 스트림(io.BytesIO)을 직접 처리하는 방식으로 개선해 디스크 I/O 오버헤드를 줄일 계획입니다.
