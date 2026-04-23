# Infrastructure_DevOps Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-07-04 |
| 키워드 | `Kubernetes`, `k8s`, `Docker`, `Tailscale`, `OpenTofu`, `Pulumi`, `Ansible`, `Jenkins`, `Coolify`, `OpenUsage`, `web scraping`, `HTTP 429`, `GitOps`, `ArgoCD`, `cert-manager`, `WASM Docker`, `self-hosting`, `자체 호스팅`, `home computer access` |
| 관련문서 | [[Dev_General Index]], [[wiki_index]] |

## 개요

인프라·DevOps — Kubernetes·Docker·컨테이너리스(Rust/WASM)·Tailscale·OpenTofu·Pulumi·Ansible·Jenkins·web scraping·HTTP 429·셀프호스팅.

## 파일 목록 (10개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260317_204447_429 당신의 API에 속도 제한이 없다면 그것은 문제입니다]] | API 속도 제한의 필요성과 구현법 해설. 토큰 버킷·리키 버킷·고정 윈도우·슬라이딩 윈도우 4대 알고리즘을 비교하고 Resilience4j·커스텀 필터·Redis 기반 분산 속도 제한을 Java로 구현하는 예시(스크래퍼가 초당 5000 요청으로 데이터베이스 풀 95/100을 고갈시키는 시나리오)를 다룬다. | HTTP 429, 속도 제한 알고리즘, 토큰 버킷, 리키 버킷, 슬라이딩 윈도우, Resilience4j, Redis 분산 속도 제한, API 스크래퍼 방어 |
| [[20260322_115514_제가 실제로 사용하고 여러분도 즐겨찾기 해야 할 무료 오픈소스 도구 5가지]] | SOVANNARO가 추천하는 무료 OSS 5선: OpenUsage(Claude/Cursor/Copilot/Codex/Windsurf 사용 한도 단일 대시보드), Lucide Animated(Lucide 기반 애니메이션 아이콘 7000+ 스타), 스크린샷 스타일링 도구(프레임·그라데이션·3D·PNG 내보내기), Wiggle UI(shadcn/ui 호환 웹 위젯 모음·Apple Watch 스타일), Coolify(Vercel/Netlify 자체호스팅 대안, VPS/Raspberry Pi 호스팅 가능, 유료 클라우드 $5/월). | OpenUsage, Lucide Animated, Wiggle UI, Coolify, shadcn/ui 위젯, Vercel 자체 호스팅 대안, AI 사용량 대시보드, 스크린샷 스타일링, SOVANNARO, Raspberry Pi 호스팅 |
| [[20260326_160618_집 컴퓨터를 어디서든 접속 가능하게 만드는 방법 공용 IP 라우터 설정 없이]] | 공용 IP나 포트 포워딩 없이 집 컴퓨터에 원격 접속하는 10분 설정 가이드. Tailscale로 두 장치를 사설 VPN 네트워크(100.x.x.x)로 연결하고, NoMachine NX 프로토콜(포트 4000)로 완전한 원격 데스크톱 세션 구현. Linux/Windows/macOS 명령어 포함, nc -vz 연결 테스트 팁 제공. | Tailscale 사설 VPN, NoMachine NX 원격 데스크톱, CGNAT 우회, tailscale ip -4, 포트 포워딩 없는 원격 접속, Homebrew tailscale-app, Gwang-Jin |
| [[20260329_181952_48시간 만에 프로덕션운영 수준의 쿠버네티스Kubernetes 플랫폼을 구축했습니다 모든 난관과 그 해결책을 공개합니다]] | DevOps 채용 요구(운영 Kubernetes 경험)와 실습 기회의 격차를 해소하기 위해 48시간 내 완전한 DevSecOps K8s 플랫폼을 직접 구축한 실전기다. AWS EC2 3노드, Terraform, kubeadm, Calico, MetalLB, ArgoCD GitOps, HashiCorp Vault, External Secrets Operator, Traefik, cert-manager, Prometheus+Grafana를 포함한 5계층 아키텍처를 설명한다. Yaw Nana Gyamfi Prempeh 작성, 1,100 하트를 받은 DevSecOps 포트폴리오 구축 케이스 스터디. | kubeadm, ArgoCD, HashiCorp Vault, External Secrets Operator, Calico, MetalLB, Traefik, cert-manager, Prometheus, Grafana, DevSecOps |
| [[20260404_105000_컨테이너 없는 미래 Rust와 WASM이 Docker를 대체하려는 이유]] | 무거운 컨테이너 대신 Rust와 WebAssembly 조합이 더 작은 바이너리와 강한 샌드박스를 제공해 배포 패러다임을 바꿀 수 있다는 주장이다. Fermyon Spin 예시와 함께 WASM의 킬로바이트급 패키징, deny-by-default 보안, Cloudflare Workers류의 에지 런타임 흐름을 설명한다. | Rust, WebAssembly, WASM, Docker alternative, Fermyon Spin, Cloudflare Workers, sandbox, edge runtime |
| [[20260409_074053_공개 IP 라우터 설정 없이 집 컴퓨터를 어디서든 연결하는 방법 편집증적인 에디션]] | 공인 IP나 포트포워딩 없이도 집 컴퓨터에 안전하게 접속하는 원격 환경 구축 가이드다. 네트워크 노출을 최소화하면서 어디서든 접근 가능한 개인 컴퓨팅 환경을 만드는 방법을 설명한다. | remote access, no public IP, no port forwarding, home computer access, secure networking, paranoid edition |
| [[20260420_220431_2026년 최고의 웹 스크래핑 브라우저 8가지]] | 웹 스크래핑에 특화된 브라우저와 자동화 도구 8종을 비교하며 동적 페이지, 안티봇 우회, 세션 재현 능력을 실전 관점에서 살핀다. 일반 브라우저보다 스크래핑 친화적인 런타임을 고르는 것이 데이터 수집 성공률을 좌우한다는 결론이다. | web scraping browser, anti-bot, headless browser, scraping stack, dynamic pages, browser automation |
| [[20260420_220621_ERR_CONNECTION_REFUSED 로컬호스트 연결 거부 오류 분석 및 해결 방안]] | `ERR_CONNECTION_REFUSED`가 실제 원문이 아니라 localhost 서버 미실행, 포트 불일치, 방화벽, Docker 포트 포워딩 문제를 시사하는 브라우저 오류라는 점을 정리한다. `netstat`, `lsof`, 서버 로그 확인 같은 실전 진단 순서를 통해 원인 파악을 돕는다. | ERR_CONNECTION_REFUSED, localhost, port mismatch, firewall, Docker port forwarding, netstat, lsof |
| [[20260420_222834_도커Docker 시각적으로 파헤치기 2026년 초보자를 위한 실용 안내서]] | Docker의 이미지, 컨테이너, 볼륨, 네트워크 개념을 초보자가 시각적으로 이해할 수 있게 정리한 입문 안내서다. 명령어 암기보다 동작 구조를 머릿속에 그릴 수 있게 해 주는 실용 설명에 초점이 있다. | Docker, containers, images, volumes, networks, beginner guide, container mental model |
| [[20260421_142503_2026년 상위 13가지 오픈소스 자동화 도구]] | 인프라와 운영 자동화를 위해 2026년에 주목할 오픈소스 도구 13개를 비교 정리한 글이다. OpenTofu, Pulumi, Ansible, Jenkins, Chef, Spacelift Intent 같은 도구를 용도별로 묶어 선택 기준을 제시한다. | OpenTofu, Pulumi, Ansible, Jenkins, Chef, Spacelift Intent, 인프라 자동화 |

## 관련 카테고리

* > 상위: [[Dev_General Index]]
* > 최상위: [[wiki_index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
