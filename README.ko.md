<p align="center"><img src="assets/quark-symbol.svg" width="72" height="72" alt=""></p>

<h1 align="center">Quark</h1>

<p align="center">
  <a href="https://skills.sh/sik-limited/quark"><img src="assets/skills-sh-badge.svg" width="136" height="20" alt="skills.sh"></a>
</p>

<p align="center"><a href="README.md">English</a> · <strong>한국어</strong> · <a href="README.ja.md">日本語</a> · <a href="README.zh-TW.md">繁體中文</a></p>

<p align="center">Codex, Claude Code, Cursor로 에이전틱 디자인을 할 때 쓰는 오픈소스 <a href="https://agentskills.io/">Agent Skills</a>와 <code>DESIGN.md</code> 참고 자료입니다.</p>

<p align="center"><a href="https://design.sik.limited/"><strong>Quark 웹사이트</strong></a> · 컴포넌트와 디자인 토큰 미리보기</p>

```sh
npx skills add sik-limited/quark
```

## 스킬

| 스킬 | 용도 |
| --- | --- |
| [`quark-ui`](skills/quark-ui/SKILL.md) | Quark 컴포넌트와 디자인 토큰으로 UI 만들기 |
| [`design-to-code`](skills/design-to-code/SKILL.md) | 스크린샷, Figma 프레임, 디자인 명세를 코드로 구현 |
| [`design-check`](skills/design-check/SKILL.md) | 기존 UI를 점검하고 수정 우선순위 정리 |
| [`q-clean`](skills/q-clean/SKILL.md) | 기존 UI를 컴포넌트로 정리하고 상태, 모션, 반응형, 접근성 문제 수정 |
| [`component-api`](skills/component-api/SKILL.md) | 여러 프레임워크에서 쓸 공용 컴포넌트의 속성, 상태, 합성 방식 설계 |
| [`design-tokens`](skills/design-tokens/SKILL.md) | 색상, 간격, 반경, 그림자, 모션, 테마의 토큰 체계 설계 |
| [`animate`](skills/animate/SKILL.md) | 전환, 스프링, 제스처와 모션 감소 대응을 포함한 UI 모션 구현 |
| [`interface-polish`](skills/interface-polish/SKILL.md) | 모서리 반경, 광학 정렬, 테두리, 아이콘 등 UI 디테일 점검 |
| [`typography`](skills/typography/SKILL.md) | 글자 크기, 행간, 줄바꿈, 숫자와 글꼴 로딩 점검 |
| [`layout`](skills/layout/SKILL.md) | 그룹, 정렬, 간격, 반응형 레이아웃과 RTL 점검 |
| [`accessibility`](skills/accessibility/SKILL.md) | WCAG 2.2 AA를 기준으로 시맨틱, 포커스, 키보드와 터치 조작 점검 |
| [`interface-copy`](skills/interface-copy/SKILL.md) | 레이블, 오류, 빈 상태와 확인 대화상자의 문구 작성 |
| [`stress-test`](skills/stress-test/SKILL.md) | 긴 텍스트, 빈 데이터, 좁은 화면과 오류 상태에서 UI가 깨지는 지점 확인 |
| [`interface-review`](skills/interface-review/SKILL.md) | 변경 사항을 검토하고 영향도에 따라 개선점을 정리 |

## 설치

Node.js와 npm을 설치한 뒤 프로젝트 폴더에서 사용할 에이전트의 명령을 실행하세요. 상단 명령으로 스킬과 에이전트를 모두 선택할 수 있습니다. [Skills CLI 문서](https://www.skills.sh/docs/cli)

### Codex

```sh
npx skills add sik-limited/quark --agent codex
```

### Claude Code

```sh
npx skills add sik-limited/quark --agent claude-code
```

### Cursor

```sh
npx skills add sik-limited/quark --agent cursor
```

`--skill quark-ui`를 붙이면 해당 스킬만 설치하고, `--skill '*'`를 붙이면 전체를 설치합니다. 여러 프로젝트에서 사용하려면 `--global`을 붙이세요. `q-clean`과 `design-check`는 다른 Quark 스킬도 함께 사용하므로 전체 설치를 권장합니다.

### Quark 웹사이트에서 설치

[Quark UI 스킬 페이지](https://design.sik.limited/ko/skills/quark-ui/)에서 `SKILL.md`를 내려받거나, 파일 URL을 Skills CLI에 직접 전달할 수 있습니다.

```sh
npx skills add https://design.sik.limited/skills/quark-ui/SKILL.md
```

수동으로 설치하려면 `skills/`에서 원하는 스킬 폴더를 에이전트의 스킬 디렉터리에 복사하세요. Claude 웹에서는 스킬 폴더를 ZIP으로 압축해 업로드할 수 있습니다. ChatGPT 웹에서는 워크스페이스에서 스킬 업로드를 지원할 때 가져올 수 있습니다. [Claude 도움말](https://support.claude.com/en/articles/12512180-use-skills-in-claude) · [ChatGPT 도움말](https://help.openai.com/en/articles/20001066-skills-in-chatgpt)

## 첫 사용

스킬을 설치한 에이전트에서 점검할 화면을 지정하고 다음과 같이 요청하세요.

```text
design-check로 이 설정 화면의 모바일 레이아웃, 키보드 포커스, 오류 상태를 점검해 줘. 발견한 문제와 수정 우선순위를 알려줘.
```

## DESIGN.md

참고할 문서를 골라 프로젝트 루트에 `DESIGN.md`로 저장하고 에이전트에게 UI 작업 전에 읽도록 요청하세요. Quark가 공개 디자인 시스템과 제품 화면을 바탕으로 정리한 독립 참고 자료입니다.

| 레퍼런스 | 참고할 요소 | 링크 |
| --- | --- | --- |
| Claude | 따뜻한 색상, 코럴 버튼, 에디토리얼 타이포그래피 | [문서](designs/claude/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/claude/) |
| KRDS | 공공 서비스 UI, 접근성, 폼과 내비게이션 | [문서](designs/krds/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/krds/) |
| Pinterest | 이미지 중심 탐색, 메이슨리 그리드, 필터 | [문서](designs/pinterest/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/pinterest/) |
| RIDI | 콘텐츠 탐색, 책 표지 카드, 순위 목록 | [문서](designs/ridi/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/ridi/) |
| SEED (당근) | 시맨틱 색상, 그리드, 한국어 UX 라이팅 | [문서](designs/seed/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/seed/) |
| Toss | 금융 UI, 모바일 터치 컨트롤, 버튼과 입력 상태 | [문서](designs/toss/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/toss/) |
| Wanted (Montage) | 반응형 그리드, 타이포그래피, 범용 컴포넌트 상태 | [문서](designs/wanted/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/wanted/) |
| xAI | 다크 UI, 흰색 버튼, 얇은 테두리와 평면 패널 | [문서](designs/xai/DESIGN.md) · [미리보기](https://design.sik.limited/ko/themes/xai/) |

## 라이선스

Quark가 작성한 스킬과 디자인 참고 문서는 [MIT 라이선스](LICENSE)로 공개합니다. 브랜드명, 상표, 글꼴 및 외부 자료의 권리는 각 권리자에게 있습니다.
