---
title: CheckerBoard Battle With DotNetty
date: 2023-11-01 08:00:00 +0800
categories: [Blogging, DotNetty]
tags: [game,dotnetty,c#,tcp]
img_path: '/assets/img/posts/20231101'
pin: true
---

## 개요
DotNetty를 이용한 평면도전투 게임서버 및 윈폼 클라이언트 개발 <br/>
유저는 로그인후 캐릭터를 선택하여 상대방과 전투를 통해 전투데미지를 입히면 대전에서 승리함 <br/>
유저당 LP(Lift Point)는 기본 100Lp 이며, 대전당 공격을 통해 30Lp를 먼저 깍으면 승리함. <br/>
선택한 캐릭터별로 이동할수있는 범위와, 공격시 데미지가 다르며, <br/>
해당 캐릭터별 정보는 데이터테이블을 읽어서 세팅함. <br/>
대전당 30Lp가 필요함으로 총 Lp가 30미만으로 남은경우 방을 만들수도 없고, 대전에 참여할수도 없음. <br/>
이동가능한 범위내에서 셀을 클릭하면 캐릭터가 이동하며, <br/>
이동가능범위내에 상대방이 있을때 상대방 좌표를 클릭하면 공격으로 데미지를 입힘. <br/>


## 개발환경
- Language : C#
- Framework : .NET 6.0
- DB : MS-SQL (계정정보 저장용)
- Caching : Redis (세션정보 저장용) 


- Packages
  + Microsoft.EntityFrameworkCore
  + Microsoft.EntityFrameworkCore.SqlServer
  + StackExchange.Redis

  + Microsoft.Extensions.Configuration
  + Microsoft.Extensions.Configuration.FileExtensions
  + Microsoft.Extensions.Configuration.Json
  + Microsoft.Extensions.Logging.Console
  + DotNetty.Buffers
  + DotNetty.Codecs
  + DotNetty.Common
  + DotNetty.Handlers
  + DotNetty.Transport

  + log4net : 파일로그용
  + Newtonsoft.Json : Json핸들링



## 프로젝트 설명

### CheckerboardClient : 윈폼 게임클라이언트 소스
- Config : log4net 및 클라이언 설정관련 파일

- Data : 클라에서만 사용하는 Enum 및 Constants

- Handlers : 각종 핸들러
  + ClientPackHandler : DotNetty Handler 관련
  + IDispatcherHandler : 서버에서 응답왔을때 GameClient 로직과 연결용
  + IFormHandler : GameClient 로직과 윈폼 연결용

- Models : 윈폼관련 클래스 객체

- Form1 : UI Form

- GameClient : Dotnetty GameClient 


### CheckerboardLib : 공용소스
- Channel : DotNetty Channel 관련 (채널저장, 응답 등)

- Codec : 통신 패킷, Encoder, Decoder 관련

- Common : 공용소스

- Data : Enum 및 Constants

- DataTable : 엑셀파일을 Json으로 변환한 정책관련 소스

- Db : DB관련 소스

- Extensions : 확장팩 소스

- Helper : 헬퍼소스들 (Config 로더등)

- Log : 파일, 콘솔 등 각종 로그관련

- Packets : 서버-클라 통신패킷정의

- SRedis : 레디스관련 소스

- Utils : 유틸리티 소스


### CheckerboardServer : 콘솔 게임서버소스
- Config : log4net 및 서버구동 설정관련 파일

- Handlers : 각종 핸들러
  + PubSubHandler : 레디스 Subscribe 관련
  + ServerPacketHandler : 클라요청에 따른 서버처리 로직 (partial)
  + ServiceHandler : 클라요청에 따른 서버처리 로직 (partial)



## 게임통신 API

### 로그인
- ReqUserLogin : 로그인요청
- ResUserLogin : 로그인응답
- PsUserLogin : 유저 로그인알림


### 대전게임
- ReqEnterBattlePlay : 대전게임 입장요청 
- ResEnterBattlePlay : 대전게임 입장응답

- ReqCreateBattleRoom : 방만들기 요청 (상대방대기)
- ResCreateBattleRoom : 방만들기 응답 (상대방대기)
- PsCreateBattleRoom : 방만들기 알림

- ReqExitBattleRoom : 대기방 대기취소 요청 (대기방리스트로 이동)
- ResExitBattleRoom : 대기방 대기취소 응답 (대기방리스트로 이동)
- PsExitBattleRoom : 대기방 대기취소 알림 (대기방리스트로 이동)

- ReqJoinBattleRoom : 대기방입장 요청 (대전요청수락=대전시작)
- ResJoinBattleRoom : 대기방입장 응답 (대전요청수락=대전시작)
- PsJoinBattleRoom : 대전시작 알림

- ReqMoveBattleCharacter : 대전게임 캐릭터이동 요청
- ResMoveBattleCharacter : 대전게임 캐릭터이동 응답
- PsMoveBattleCharacter : 대전게임 캐릭터이동 알림

- ReqAttackBattleCharacter : 대전게임 캐릭터공격 요청
- ResAttackBattleCharacter : 대전게임 캐릭터공격 응답
- PsAttackBattleCharacter : 대전게임 캐릭터공격 알림

- ReqExitBattleJoin : 대전게임 중단 요청 (대기방리스트로 이동)
- ResExitBattleJoin : 대전게임 중단 응답 (대기방리스트로 이동)
- PsExitBattleJoin : 대전게임 중단 알림 (대기방리스트로 이동)



## 게임화면

### 데이터 테이블
캐릭터 이동시 Movement 값을, 공격시 HP값을 참고함.
![데이터테이블](20231101-00.PNG){: width="1079" height="552" }


### 게임실행 및 로그인
![클라이언트 실행](20231101-01.PNG){: width="817" height="553" }
_Login_


### 로비
![로비](20231101-02.PNG){: width="817" height="553" }
_Lobby_


### 대전게임
![대전플레이](20231101-03.PNG){: width="817" height="553" }
_Play Enter_

![대기방만들기](20231101-04.PNG){: width="1641" height="557" }
_Create Battle Room_

![대기방입장](20231101-05.PNG){: width="817" height="553" }
_Join Battle Room_

![대전시작](20231101-06.PNG){: width="1596" height="541" }
_Join Battle Room_

![대전시작](20231101-07.PNG){: width="1596" height="540" }
_Join Battle Room_

![캐릭터이동](20231101-08.PNG){: width="817" height="553" }
_Move Character_

![캐릭터이동](20231101-09.PNG){: width="817" height="553" }
_Move Character_

![캐릭터이동](20231101-10.PNG){: width="1641" height="550" }
_Attack Character_

![대전결과](20231101-11.PNG){: width="1646" height="557" }
_Battle Play Result_

![대전후](20231101-12.PNG){: width="1640" height="556" }
_Battle Play Result_

![LP부족](20231101-13.PNG){: width="815" height="556" }
_Not enough lp_

![LP부족](20231101-14.PNG){: width="1640" height="556" }
_Not enough lp_



## 게임소스

<a href="/assets/img/posts/20231101/CheckerboardApp.zip">소스 다운로드</a>