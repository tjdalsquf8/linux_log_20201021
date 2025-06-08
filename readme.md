Linux ReadME

# 12Week 

취약성 상위 5개
1. Content Security Policy(CSP) Header Not Set
CSP 헤더 설정으로 외부 스크립트 차단한다.
2. X - Content - Type - Options Header Missing
nosniff 헤더 추가하여 MIME 스니핑 방지한다.
3. Missing Anti - clickjacking Header
X - Frame - Options 설정하여 iframe 공격 방지한다.
4. Information Disclosure - Server Header
Apache 설정으로 ServerTokens와 ServerSignature 비활성화한다.
5. Absence of Anti - CSRF Tokens
요청마다 CSRF 보안 토큰 적용한다.

[ModSecurity 공격 탐지 실습 결과 요약]
총 5개의 공격 URL을 직접 입력하고 modsec_audit.log 로그를 통해 탐지 결과를 정리

1. 로그 번호     : ebf52461
   공격 유형     : Command Injection (XML 기반)
   탐지 메시지   : OS File Access Attempt (/etc/passwd 포함)
   룰 ID         : 930120
   위험도        : CRITICAL
   응답 코드     : 403 Forbidden

2. 로그 번호     : ebf52461
   공격 유형     : SQL Injection
   탐지 메시지   : SQL Injection Detected (id=1 AND 1=1)
   룰 ID         : 942100
   위험도        : CRITICAL
   응답 코드     : 403 Forbidden

3. 로그 번호     : ebf52461
   공격 유형     : XSS (Cross Site Scripting)
   탐지 메시지   : XSS Attack Detected via libinjection (onmouseover)
   룰 ID         : 941100
   위험도        : CRITICAL
   응답 코드     : 403 Forbidden

4. 로그 번호     : ebf52461
   공격 유형     : PHP 코드 삽입
   탐지 메시지   : High-Risk PHP Function Call Found (system 함수 사용)
   룰 ID         : 933160
   위험도        : CRITICAL
   응답 코드     : 403 Forbidden

5. 로그 번호     : 7bb0512e
   공격 유형     : 명령어 삽입 (cmd=cat /etc/passwd; ls)
   탐지 메시지   : 명령어 실행 시도 탐지됨 (Command Injection)
   룰 ID         : 자동 감지
   위험도        : CRITICAL
   응답 코드     : 403 Forbidden


# 13Week 간략 문제 풀이

net_ex.sh 코드 생성 후 아래 코드를 작성하여 백그라운드에서 실행시켰다.
#!/bin/bash
URL="http://speedtest.tele2.net/10MB.zip"

COUNT=2000
for i in $(seq 1 $COUNT); do
curl -s -o /dev/null "$URL" &
done
wait # 모든 백그라운드 curl이 완료될 때까지 대기


네크워크 부하를 테스트를 진행 후 CPU, 메모리에 끼치는 영향을 분석한 결과 :
네트워크 부하 테스트는 CPU에 가장 큰 영향을 줬으며 CPU 사용량이 85.6%->99.6%로 증가하였다. 또한 메모리도 병렬 프로세스로 인해 4.54gb->5.02gb로 점진적인 사용량 증가가 나타났다. curl은 단순 네트워크 요청처럼 보여도 대량 병렬 실행 시 CPU 및 메모리 부하가 크며 이는 시스템 응답 속도 저하 및 전체 부하 증가로 이어질 수 있다.
