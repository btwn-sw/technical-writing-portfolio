# 작동 방식

사용자 입력부터 문서 스트리밍 출력까지의 전체 생성 흐름을 설명합니다.

<br>

## 전체 흐름

```
사용자가 규제를 선택하고 회사 프로파일을 입력
               ↓
엔진이 atom_output_mapping을 조회
               ↓
엔진이 회사 프로파일 조건을 적용
               ↓
엔진이 injection_order에 따라 원자를 정렬
               ↓
출력 섹션별 프롬프트 조립
               ↓
Claude API가 섹션 내용을 생성
               ↓
SSE로 클라이언트에 스트리밍
```

<br>

## 1단계 — 사용자 입력

사용자는 두 가지를 입력합니다.

**규제 선택:** 준수해야 할 규제를 하나 이상 선택합니다 (예: GDPR + ISMS-P). 선택한 규제에 매핑된 원자가 활성화됩니다.

**회사 프로파일:** 회사의 제품과 데이터 처리 방식을 설명하는 구조화된 필드 집합입니다. 프로파일 필드가 조건부 원자 주입을 제어합니다.

**생성에 영향을 주는 프로파일 필드:**

| 필드 | 효과 |
|---|---|
| `processes_special_category_data` | `if_special_category_data` 조건의 원자 활성화 |
| `uses_consent_as_legal_basis` | `if_uses_consent` 조건의 원자 활성화 |
| `has_data_processor_agreements` | DPA 관련 출력물 활성화 |
| `operates_in_eu` | GDPR 국경 간 데이터 이전 원자 활성화 |
| `company_name`, `dpo_contact` | 생성 텍스트에 실제 값으로 주입 |

<br>

## 2단계 — 원자 조회

엔진은 선택한 규제에 속한 모든 원자를 `atom_output_mapping`에서 조회합니다.

```sql
SELECT aom.*, ag.source_clause, ag.principle_context
FROM atom_output_mapping aom
JOIN atom_generation ag ON aom.atom_id = ag.id
WHERE ag.regulation_id IN (selected_regulation_ids)
ORDER BY aom.output_id, aom.injection_order
```

출력물별, 주입 순서별로 정렬된 원자-출력물 매핑 목록이 반환됩니다.

<br>

## 3단계 — 조건 필터링

엔진은 각 원자의 조건을 회사 프로파일과 대조합니다.

```
atom.condition = NULL                       → 항상 주입
atom.condition = 'if_uses_consent'          → profile.uses_consent = true일 때만 주입
atom.condition = 'if_special_category_data' → profile.processes_special_category_data = true일 때만 주입
```

조건이 맞지 않는 원자는 생성에서 제외됩니다. 이 과정을 통해 생성된 문서가 일반 템플릿이 아닌 회사의 실제 데이터 처리 방식을 반영합니다.

<br>

## 4단계 — 섹션 조립

각 `compliance_output`에 대해 엔진은 기여 원자를 `injection_order` 순서대로 조립합니다.

조립된 섹션은 다음 형태의 프롬프트 페이로드가 됩니다.

```
System: [생성 엔진 제어 원칙]
        [스타일·톤 지침]
        [출력 유형 컨텍스트: DOC_ / EVD_ / OPR_ / TCH_ / CTR_ / POL_]

User:   [원자 1 원문 조항 + principle_context]
        [원자 2 원문 조항 + principle_context]
        ...
        [회사 프로파일 값]
        [출력 목표: DOC_PP_RETENTION 생성]
```

<br>

## 5단계 — 생성 엔진 제어

모든 생성 호출에 네 가지 원칙이 적용됩니다.

**Information Discipline**
엔진은 원자 매핑에서 명시적으로 허가한 내용만 주입합니다. 원자 내용 없이는 생성하지 않습니다. 모델은 원자가 제공하는 범위를 넘어서는 내용을 생성할 수 없습니다.

**Mapping Completeness**
소스 원자가 완전히 매핑되지 않은 섹션은 생성하지 않습니다. 매핑이 불완전하면 최선의 추측 출력이 아닌 명시적 경고를 반환합니다.

**Generation Traceability**
생성된 모든 섹션에 기여한 원자 목록이 기록됩니다. 출력물에서 규제 조항까지 감사 추적이 가능합니다.

**`[INFORMATION NEEDED]` 프로토콜**
회사 프로파일 값이 없으면 엔진은 값을 추론하거나 생략하지 않습니다. `[INFORMATION NEEDED: field_name]` 플레이스홀더를 삽입합니다. 문서의 구조는 완성된 채로 유지되고, 사용자가 빈칸을 직접 채웁니다.

<br>

## 6단계 — 스트리밍 출력

엔진은 각 섹션을 Claude API(claude-sonnet-4-6)에 전달하고 응답을 Server-Sent Events(SSE)로 클라이언트에 스트리밍합니다.

섹션은 문서 순서대로 스트리밍됩니다. 전체 생성이 완료될 때까지 기다리지 않고, 클라이언트는 섹션이 도착하는 대로 실시간으로 렌더링합니다.

<br>

## View 모드

생성된 문서는 의도한 독자에 따라 세 가지 View 모드를 지원합니다.

| 모드 | 독자 | 내용 |
|---|---|---|
| `PUBLIC_VIEW` | 최종 사용자, 고객 | 개인정보처리방침, 소비자용 공개 문서 |
| `INTERNAL_REVIEW` | 법무·컴플라이언스 팀 | 원문 조항 주석이 포함된 전체 문서 |
| `AUDIT_SUBMISSION` | 규제 기관, 감사인 | 의무 추적 태그가 포함된 문서 |

<br>

## 톤 설정

생성 엔진은 네 가지 톤 프로파일을 지원합니다.

| 톤 | 대상 | 문체 |
|---|---|---|
| `FORMAL_REGULATORY` | 규제 기관, 법무팀, 엔터프라이즈 | 수동태, 조항 번호 인라인 표기, 법률 용어 유지 |
| `PROFESSIONAL_ACCESSIBLE` ★ 기본 | B2B SaaS, 스타트업 | 능동태, 법률 용어 최소화, 비즈니스 전문가 대상 |
| `FRIENDLY_PLAIN` | 소비자 앱, B2C | 2인칭 직접 주소, 짧은 문장 |
| `INDUSTRY_TECHNICAL` | 개발자 대상 B2B, API 제품 | 기술 용어, 구체적 수치, 구현 관점 |

<br>

*← [README로 돌아가기](../ko/README.md) · [아키텍처](../ko/architecture.md)*

<br>
<br>