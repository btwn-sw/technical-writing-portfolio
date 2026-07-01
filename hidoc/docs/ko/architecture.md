# 아키텍처

HiDoc은 규제 원문, 원자 단위 의무, 출력물 매핑, 생성 문서 정의를 각각 분리한 4레이어 아키텍처를 사용합니다. 각 레이어는 하나의 책임만 담당합니다.

<br>

## 전체 구조

```
레이어 1 — 규제        regulations 테이블
               ↓
레이어 2 — 원자        atom_generation 테이블
               ↓
레이어 3 — 매핑        atom_output_mapping 테이블
               ↓
레이어 4 — 출력        compliance_outputs 테이블
```

레이어는 위에서 아래 방향으로만 참조합니다. 규제 레이어는 출력물을 모르고, 출력 레이어는 원문 조항을 모릅니다. 이 분리 구조 덕분에 새로운 규제를 추가할 때 레이어 1과 2만 수정하면 됩니다. 생성 엔진은 변경하지 않습니다.

<br>

## 레이어 1 — 규제

**테이블:** `regulations`

지원 규제의 메타데이터를 저장합니다. 이름, 관할권, 버전, 조항 분석 시 AI 사용 허용 여부를 포함합니다.

**현재 지원 규제:**

| ID | 규제 | 관할권 |
|---|---|---|
| GDPR_2016 | 일반 개인정보 보호 규정 | EU |
| ISMS_P_2023 | 정보보호 및 개인정보보호 관리체계 | 한국 |
| SOC2_2017 | 서비스 조직 통제 2 | 미국 |
| EU_AI_ACT_2024 | EU 인공지능법 | EU |
| KR_ECOMMERCE | 전자상거래법 제6조 | 한국 |
| KR_TAX | 국세기본법 제85조의3 | 한국 |

<br>

## 레이어 2 — 원자

**테이블:** `atom_generation`

각 행은 하나의 원자입니다. 원자는 규제 조항에서 추출한 단일 컴플라이언스 의무입니다.

**원자 ID 형식:** `{규제}_{연도}_ART{조항}_{순번}`

예시: `GDPR_2016_ART13_001` — GDPR Art.13에서 추출한 첫 번째 원자.

**주요 컬럼:**

| 컬럼 | 역할 |
|---|---|
| `id` | 원자 고유 식별자 |
| `regulation_id` | `regulations` 테이블 참조 |
| `source_clause` | 원문 규제 조항 텍스트 |
| `obligation_type` | 원자의 의무 유형 (`DISCLOSE`, `IMPLEMENT`, `MAINTAIN`, `PERFORM`, `VERIFY`, `REPORT`, `CONTRACT`) |
| `principle_context` | 설계 근거 — 이 원자를 이렇게 추출한 이유 |
| `condition` | 적용 조건 (`NULL` = 항상 적용, 또는 조건식) |

**조건 예시:**
- `NULL` — 모든 회사에 적용
- `if_special_category_data` — 건강정보·생체정보 등 특수 범주 데이터를 처리하는 경우에만 적용
- `if_uses_consent` — 동의를 법적 근거로 사용하는 경우에만 적용

**현재 커버리지:** GDPR 79개 원자 (Art.5–Art.33) + 한국 법정 보존 의무 5개 원자

<br>

## 레이어 3 — 매핑

**테이블:** `atom_output_mapping`

각 행은 하나의 원자를 하나의 출력물에 연결하며, 그 기여 역할과 의무 유형을 명시합니다.

**행 ID 형식:** `{원자_ID}__{출력물_ID}`

예시: `GDPR_2016_ART33_001__DOC_IR_NOTIFY_AUTH`

**주요 컬럼:**

| 컬럼 | 역할 |
|---|---|
| `atom_id` | `atom_generation` 테이블 참조 |
| `output_id` | `compliance_outputs` 테이블 참조 |
| `role` | `PRIMARY` — 핵심 소스 · `SUPPORTING` — 보완 |
| `obligation_type` | `DISCLOSE` · `IMPLEMENT` · `MAINTAIN` · `PERFORM` · `VERIFY` · `REPORT` · `CONTRACT` |
| `condition` | 원자의 조건을 상속하거나 재정의 |
| `injection_order` | `DOC_` 출력물 내 원자 주입 순서 — 낮을수록 먼저 |

**현재 매핑 수:** 147개 행

<br>

## 레이어 4 — 출력

**테이블:** `compliance_outputs`

각 행은 시스템이 생성할 수 있는 문서·증거·절차·통제·정책 중 하나를 정의합니다.

**출력물 ID 형식:** `{유형}_{도메인}_{원칙}`

**출력 유형:**

| 접두사 | 유형 | 예시 |
|---|---|---|
| `DOC_` | 문서 섹션 | `DOC_PP_RETENTION`, `DOC_DPA_PROC_OBLIG` |
| `EVD_` | 증거 산출물 | `EVD_TRN_SECURITY`, `EVD_INC_LOG` |
| `OPR_` | 운영 절차 | `OPR_INC_NOTIFY`, `OPR_DEL_EXECUTE` |
| `TCH_` | 기술적 통제 | `TCH_ENC_AT_REST`, `TCH_MFA_USER` |
| `CTR_` | 계약 산출물 | `CTR_DPA_STANDARD`, `CTR_SUB_PROC` |
| `POL_` | 정책 기술서 | `POL_SEC_ENCRYPTION`, `POL_AI_PRINCIPLES` |

**현재 출력물 수:** 97개 행

**설계 원칙:** 출력물 ID는 조항 번호가 아닌 규제 원칙을 기반으로 명명합니다. `DOC_PP_RETENTION`은 "보존 의무를 다루는 개인정보처리방침 섹션"을 의미합니다. 특정 조항 번호에 종속되지 않기 때문에 같은 원칙을 공유하는 여러 규제에서 동일한 출력물을 재사용할 수 있습니다.

<br>

## 핵심 설계 결정

**왜 섹션이 아닌 원자인가?**

초기 버전은 규제 조항을 문서 섹션에 직접 매핑했습니다. 하나의 조항이 여러 출력물에 기여해야 할 때 이 구조가 무너졌습니다. 예를 들어, 보안에 관한 Art.5(1)(f)는 `DOC_PP_RIGHTS`와 `TCH_ENC_AT_REST` 모두에 기여합니다. 원자가 이 문제를 해결합니다. 하나의 원자가 여러 매핑을 가질 수 있습니다.

**왜 `compliance_outputs`와 `atom_output_mapping`을 분리했는가?**

출력물은 시스템이 생성할 수 있는 것을 정의하고, 매핑은 각 출력물에 어떤 원자가 기여하는지를 정의합니다. 분리하면 출력물이 규제 독립적이 됩니다. `DOC_PP_RETENTION`은 GDPR·ISMS-P·한국 법령의 원자를 동시에 수용할 수 있습니다.

**왜 `injection_order`인가?**

`DOC_` 출력물 내에서 원자는 일관된 텍스트를 생성하기 위해 특정 순서로 주입됩니다. `injection_order` 컬럼이 이 순서를 제어합니다. 생성 엔진에 하드코딩하지 않습니다.

<br>

*← [README로 돌아가기](../ko/README.md)*

<br>
<br>