---
title: Othello Game With DotNetty
date: 2023-10-23 02:00:00 +0900
categories: [Blogging, DotNetty]
tags: [game,dotnetty,c#,tcp]
img_path: '/assets/img/posts/20231023'
pin: true
---

## 개요
DotNetty를 이용한 Othello 게임서버 및 윈폼 클라이언트 개발



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

### DotNettyOthelloClient : 윈폼 게임클라이언트 소스
- Config : log4net 및 클라이언 설정관련 파일

- Data : 클라에서만 사용하는 Enum 및 Constants

- Handlers : 각종 핸들러
  + ClientPackHandler : DotNetty Handler 관련
  + IDispatcherHandler : 서버에서 응답왔을때 GameClient 로직과 연결용
  + IFormHandler : GameClient 로직과 윈폼 연결용

- Models : 윈폼관련 클래스 객체

- Form1 : UI Form

- GameClient : Dotnetty GameClient 


### DotNettyOthelloLib : 공용소스
- Channel : DotNetty Channel 관련 (채널저장, 응답 등)

- Codec : 통신 패킷, Encoder, Decoder 관련

- Common : 공용소스

- Data : Enum 및 Constants

- Db : DB관련 소스

- Extensions : 확장팩 소스

- Helper : 헬퍼소스들 (Config 로더등)

- Log : 파일, 콘솔 등 각종 로그관련

- Packets : 서버-클라 통신패킷정의

- SRedis : 레디스관련 소스

- Utils : 유틸리티 소스


### DotNettyOthelloServer : 콘솔 게임서버소스
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


### 싱글게임
- ReqEnterSinglePlay : 싱글게임 입장요청 
- ResEnterSinglePlay : 싱글게임 입장응답

- ReqPutSinglePlayStone : 싱글게임 착수 요청(바둑돌 놓기)
- ResPutSinglePlayStone : 싱글게임 착수 응답(바둑돌 놓기)

- ReqResetSinglePlay : 싱글게임 재도전
- ResResetSinglePlay : 싱글게임 재도전

- ReqExitSinglePlay : 싱글게임 퇴장응답 (로비로 이동)
- ResExitSinglePlay : 싱글게임 퇴장응답 (로비로 이동)


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

- ReqPutBattlePlayStone : 대전게임 착수 요청(바둑돌 놓기)
- ResPutBattlePlayStone : 대전게임 착수 응답(바둑돌 놓기)
- PsPutBattlePlayStone : 대전게임 착수 알림(바둑돌 놓기)

- ReqExitBattleJoin : 대전게임 중단 요청 (대기방리스트로 이동)
- ResExitBattleJoin : 대전게임 중단 응답 (대기방리스트로 이동)
- PsExitBattleJoin : 대전게임 중단 알림 (대기방리스트로 이동)



