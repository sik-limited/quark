# Quark

**[Quark 웹사이트](https://design.sik.limited/)** · 컴포넌트와 디자인 토큰 미리보기

AI 코딩 에이전트로 웹 UI를 설계하고 구현하고 점검할 때 쓰는 [Agent Skills](https://agentskills.io/)와 `DESIGN.md`를 모았습니다.

## 스킬

| 스킬 | 용도 |
| --- | --- |
| [`quark-ui`](skills/quark-ui/SKILL.md) | Quark 컴포넌트와 토큰을 기존 프로젝트에 적용 |
| [`q-clean`](skills/q-clean/SKILL.md) | UI를 컴포넌트화하고 상태, 모션, 반응형, 접근성을 한 번에 정리 |
| [`design-check`](skills/design-check/SKILL.md) | 페이지나 컴포넌트를 여러 기준으로 점검하고 우선순위별 보고서 작성 |
| [`design-to-code`](skills/design-to-code/SKILL.md) | 이미지, Figma, 명세를 기존 컴포넌트와 토큰을 활용해 코드로 구현 |
| [`component-api`](skills/component-api/SKILL.md) | 공용 컴포넌트의 이름, 상태, 속성, 합성 방식과 프레임워크 간 동작 설계 |
| [`design-tokens`](skills/design-tokens/SKILL.md) | 색상, 간격, 반경, 그림자, 모션과 테마의 토큰 체계 설계 |
| [`animate`](skills/animate/SKILL.md) | 전환, 스프링, 제스처와 모션 감소 대응을 포함한 UI 모션 구현 |
| [`interface-polish`](skills/interface-polish/SKILL.md) | 모서리 반경, 광학 정렬, 테두리, 아이콘 등 UI 디테일 점검 |
| [`typography`](skills/typography/SKILL.md) | 글자 크기, 행간, 줄바꿈, 숫자와 글꼴 로딩 점검 |
| [`layout`](skills/layout/SKILL.md) | 그룹, 정렬, 간격, 반응형 레이아웃과 RTL 점검 |
| [`accessibility`](skills/accessibility/SKILL.md) | WCAG 2.2 AA를 기준으로 시맨틱, 포커스, 키보드와 터치 조작 점검 |
| [`interface-copy`](skills/interface-copy/SKILL.md) | 버튼, 레이블, 오류, 빈 상태와 확인 대화상자의 문구 작성 |
| [`stress-test`](skills/stress-test/SKILL.md) | 긴 텍스트, 빈 데이터, 좁은 화면과 오류 상태에서 UI가 깨지는 지점 확인 |
| [`interface-review`](skills/interface-review/SKILL.md) | 변경 사항을 검토하고 영향도에 따라 개선점을 정리 |

## 설치

### Codex, Claude Code, Cursor

Node.js와 npm이 설치된 환경에서, 스킬을 사용할 프로젝트 폴더에서 실행합니다. 에이전트를 고정하려면 `--agent codex`, `--agent claude-code` 또는 `--agent cursor`를 붙이세요. [Skills CLI 문서](https://www.skills.sh/docs/cli)

```sh
# 설치할 스킬과 에이전트 선택
npx skills add sik-limited/quark

# Quark UI만 Codex에 설치
npx skills add sik-limited/quark --skill quark-ui --agent codex

# 전체 스킬을 Claude Code에 설치
npx skills add sik-limited/quark --skill '*' --agent claude-code
```

여러 프로젝트에서 사용하려면 `--global`을 붙이세요. `q-clean`과 `design-check`는 여러 Quark 스킬을 함께 읽으므로 전체 설치를 권장합니다.

### Quark 웹사이트에서 가져오기

[Quark UI 웹 페이지](https://design.sik.limited/ko/skills/quark-ui/)에서 `SKILL.md`를 내려받을 수 있습니다. Skills CLI에 파일 URL을 전달해 설치할 수도 있습니다.

```sh
npx skills add https://design.sik.limited/skills/quark-ui/SKILL.md
```

수동으로 설치하려면 `skills/`에서 원하는 스킬 폴더를 에이전트의 스킬 디렉터리에 복사하세요. Claude 웹에서는 해당 폴더를 ZIP으로 압축해 업로드할 수 있습니다. ChatGPT 웹에서는 워크스페이스에서 스킬 업로드를 지원할 때 가져올 수 있습니다. [Claude 도움말](https://support.claude.com/en/articles/12512180-use-skills-in-claude) · [ChatGPT 도움말](https://help.openai.com/en/articles/20001066-skills-in-chatgpt)

## DESIGN.md

참고할 문서를 골라 프로젝트 루트에 `DESIGN.md`로 저장하고 에이전트에게 UI 작업 전에 읽도록 요청하세요. 문서에는 각 디자인 시스템에서 공개한 값과 Quark가 관찰하거나 예시로 제시한 값이 담겨 있습니다. [Quark 미리보기](https://design.sik.limited/ko/themes/)

- [Claude](designs/claude/DESIGN.md)
- [KRDS](designs/krds/DESIGN.md)
- [Pinterest](designs/pinterest/DESIGN.md)
- [RIDI](designs/ridi/DESIGN.md)
- [SEED (당근)](designs/seed/DESIGN.md)
- [Toss](designs/toss/DESIGN.md)
- [Wanted (Montage)](designs/wanted/DESIGN.md)
- [xAI](designs/xai/DESIGN.md)

## 라이선스

Quark가 작성한 스킬과 디자인 참고 문서는 [MIT](LICENSE)로 공개합니다. 문서에서 언급한 브랜드명, 상표, 글꼴 및 외부 자료의 권리는 각 권리자에게 있습니다.
