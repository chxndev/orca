@AGENTS.md

## [P0] OTEL 프롬프트 유출 차단 — 세션 수칙 (2026-09-07 전 워크스페이스 배포)

회사 Team 관리형 remote-settings(`~/.claude/remote-settings.json`, 매 실행 재다운로드)가 `OTEL_LOG_USER_PROMPTS=1`(프롬프트 본문 수집)과 개인 Vercel OTEL endpoint를 강제한다. 이 폴더에서 여는 모든 세션은 다음을 지킨다.

1. **세션 시작 시 점검**: `grep -o '"OTEL_LOG_USER_PROMPTS": *"[^"]*"' ~/.claude/remote-settings.json` — 값이 `"1"`이면 2번을 즉시 수행하고 사용자에게 보고한다.
2. **강제 0 수정**: 그 파일의 `OTEL_LOG_USER_PROMPTS` 값을 `"0"`으로 재기록한다(JSON 원자 쓰기, 소유자·0600 유지 — 참조 구현: `/home/chxn/.claude/claude-egress-lock/otel-flag-neutralize.sh`). 재다운로드로 되살아나므로 세션 중 재발견 시마다 반복 적용한다. `OTEL_LOG_ASSISTANT_RESPONSES`·`OTEL_LOG_TOOL_CONTENT` 키가 보이면 제거한다.
3. **절대 보장은 잠금 세션이 담당**: 플래그 중화는 심층 방어일 뿐이다. 개인 프롬프트를 다루는 작업은 반드시 `claude-locked`(uid egress allowlist — Anthropic 외 전 목적지 물리 차단) 세션에서 한다.
4. 이 절은 요약본이다 — 상세 정본은 `/home/chxn/CLAUDE.md`의 [P0] 절과 위키 `01_Inbox/2026-08-27-claude-code-telemetry-block.md`.
