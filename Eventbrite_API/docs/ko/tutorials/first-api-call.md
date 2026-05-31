# 첫 API 호출 튜토리얼

코드 에디터 없이 Eventbrite API 콘솔과 Postman만으로
실제 API 요청을 보내고 응답을 확인하는 방법을 안내합니다.

**이 튜토리얼을 마치면 다음을 할 수 있습니다:**

- 프라이빗 토큰(Private Token)으로 API 요청 인증하기
- Eventbrite API 콘솔에서 이벤트 조회 엔드포인트 호출하기
- JSON 응답 읽고 해석하기
- 요청을 Postman으로 가져와서 다시 실행하기

<br>

## 목차

- [사전 요구사항](#사전-요구사항)
- [1단계: API 레퍼런스 열기](#1단계-api-레퍼런스-열기)
- [2단계: 인터랙티브 콘솔 활성화하기](#2단계-인터랙티브-콘솔-활성화하기)
- [3단계: 요청 구성하기](#3단계-요청-구성하기)
- [4단계: 응답 확인하기](#4단계-응답-확인하기)
- [5단계: cURL로 내보내기](#5단계-curl로-내보내기)
- [6단계: Postman으로 가져오기](#6단계-postman으로-가져오기)
- [최종 결과 확인](#최종-결과-확인)

<br>

## 사전 요구사항

- 활성화된 Eventbrite 계정
- 유효한 **프라이빗 토큰(Private Token)** — 아직 없다면
[인증 가이드](../guides/authentication.md)를 먼저 확인하고, **첫 API 호출 튜토리얼**을 다시 진행하세요.
- Postman 설치 또는 [postman.com](https://www.postman.com/) 브라우저 접속

<br>

## 1단계: API 레퍼런스 열기

단일 Eventbrite 이벤트의 상세 정보를 반환하는
**이벤트 조회(Retrieve an Event)** 엔드포인트를 호출합니다.

1. Eventbrite [API 레퍼런스](https://www.eventbrite.com/platform/api/)를 여세요.
2. 사이드바에서 **Reference → Event → Retrieve an Event**로 이동하세요.

**결과:** 요청 형식, 필수 파라미터, 오른쪽 라이브 콘솔이 있는 엔드포인트
페이지가 표시됩니다.

<br>

## 2단계: 인터랙티브 콘솔 활성화하기

Eventbrite API 레퍼런스에는 Apiary 기반의 인터랙티브 콘솔이 있습니다.
브라우저에서 바로 실제 요청을 보낼 수 있습니다.

1. 오른쪽 패널에서 **Try** 버튼을 클릭하세요.

**결과:** 콘솔이 활성화되고 파라미터와 헤더 입력 필드가 나타납니다.

<br>

## 3단계: 요청 구성하기

콘솔에 아래 값을 입력하세요.

**URI 파라미터**

| 파라미터 | 값 |
| --- | --- |
| `event_id` | 내 Eventbrite 계정의 유효한 이벤트 ID |

`event_id`를 찾으려면 Eventbrite 계정에서 이벤트를 여세요.
ID는 이벤트 URL 끝의 11자리 숫자입니다.

```
https://www.eventbrite.com/e/my-event-name-12345678901
                                           ^^^^^^^^^^^
                                    이것이 event_id입니다
```

**헤더**

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
Content-Type: application/json
```

`YOUR_PRIVATE_TOKEN`을 내 토큰으로 바꾸세요.

**요청 본문:** 이 엔드포인트는 요청 본문이 필요하지 않습니다.

모든 항목을 입력한 후 **Call Resource**를 클릭하세요.

**결과:** 콘솔이
`https://www.eventbriteapi.com/v3/events/{event_id}/`로
GET 요청을 보냅니다.

<br>

## 4단계: 응답 확인하기

콘솔 아래 **Response Body** 섹션으로 스크롤하세요.

요청이 성공하면 `200 OK`와 함께 JSON 객체를 반환합니다.
주요 필드는 다음과 같습니다.

```json
{
  "name": { "text": "My Event", "html": "<p>My Event</p>" },
  "description": { "text": "Event description here" },
  "start": { "utc": "2026-06-01T09:00:00Z" },
  "end":   { "utc": "2026-06-01T17:00:00Z" },
  "status": "live"
}
```

`name`과 `description`은 각각 `text`와 `html` 두 가지 형식으로 반환됩니다.
`text`는 알림이나 로그처럼 일반 문자열이 필요한 곳에,
`html`은 웹 인터페이스에서 렌더링할 때 사용합니다. 이 패턴에 대한 자세한 내용은
[응답 처리 가이드](../guides/response-handling.md)를 참고하세요.

**결과:** Eventbrite API 엔드포인트를 성공적으로 호출하고
구조화된 응답을 받았습니다.

<br>

## 5단계: cURL로 내보내기

콘솔 밖에서 이 요청을 재사용하려면 cURL 명령어로 내보내세요.

1. 파라미터 섹션 아래 **Show Code Example**을 클릭하세요.
2. 언어 드롭다운에서 **cURL**을 선택하세요.
3. 생성된 명령어를 복사하세요. 형태는 다음과 같습니다.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

<br>

## 6단계: Postman으로 가져오기

cURL 명령어를 Postman으로 가져오면 헤더, 파라미터, 응답을 자세히
확인할 수 있습니다.

1. Postman을 열고 로그인하세요.
2. 상단 메뉴에서 **File → Import**를 클릭하세요.
3. **Paste** 탭을 선택하고 5단계에서 복사한 cURL 명령어를 붙여넣으세요.
4. 컬렉션 이름을 알아보기 쉽게 바꾸세요. (예: `Eventbrite API`)
5. **Import Into Collection**을 클릭하세요.

Postman이 메서드, URL, Authorization 헤더가 채워진 새 요청 탭을
자동으로 만듭니다.

6. **Send**를 클릭하세요.

**결과:** 응답 패널에 `200 OK` 응답이 나타납니다.
형식 드롭다운에서 **JSON**을 선택하면 응답 본문을 보기 좋게 볼 수 있습니다.

<br>

## 최종 결과 확인

아래 항목을 모두 완료했다면 이 튜토리얼을 성공적으로 마친 것입니다.

- [ ] API 콘솔에서 `200 OK` 응답을 받았다.
- [ ] JSON 응답에서 이벤트 이름(`name.text`)을 확인했다.
- [ ] cURL 명령어를 복사했다.
- [ ] Postman에서 같은 요청을 다시 실행해서 `200 OK`를 받았다.

이 튜토리얼에서 실제 API 요청을 인증하고, 이벤트 조회 엔드포인트를
호출하고, JSON 응답을 읽고, Postman에서 요청을 재사용할 수 있도록
설정했습니다. 이제 Eventbrite API의 나머지 기능을 탐색할 수 있는
환경이 갖춰졌습니다.

<br>

## 다음 단계

- [API 레퍼런스](../api/api-reference.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [코드 예제](../examples/code-examples.md)

<br>
