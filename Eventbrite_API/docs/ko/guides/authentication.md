# 인증 가이드

프라이빗 토큰(Private Token)을 사용해서 Eventbrite API 요청을 인증하세요.
이 가이드를 통해 Eventbrite 개발자 대시보드에서 토큰을 생성하고,
요청에 추가하고, 안전하게 저장할 수 있습니다.
Eventbrite API의 모든 엔드포인트는 인증을 요구합니다.

<br>

## 목차

- [Eventbrite 인증 방식](#eventbrite-인증-방식)
- [프라이빗 토큰 생성](#프라이빗-토큰-생성)
- [요청에 토큰 추가하기](#요청에-토큰-추가하기)
- [토큰 보안 관리](#토큰-보안-관리)
- [서버 사이드 OAuth 방식](#서버-사이드-oauth-방식)

<br>

## Eventbrite 인증 방식

Eventbrite는 OAuth 2.0을 사용합니다.
OAuth 2.0은 애플리케이션이 사용자를 대신해서 API에 접근할 수 있도록
권한을 부여하는 프레임워크입니다. 모든 API 요청에 유효한 토큰을 포함해야
하며, 토큰이 없는 경우 API는 `401 Unauthorized` 오류를 반환합니다.

Eventbrite는 두 가지 인증 자격 증명을 제공합니다. 사용 목적에 맞게 선택하세요.

| 자격 증명 | 설명 | 사용 시점 |
| --- | --- | --- |
| 프라이빗 토큰(Private Token) | 만료 기한 없이 개인 계정에 연결된 토큰 | 테스트, 서버 사이드 스크립트 |
| API 키 + 클라이언트 시크릿 | OAuth 인증 절차에 사용되는 앱 자격 증명 | 사용자 로그인이 필요한 프로덕션 앱 |

이 가이드는 모든 예제에서 **프라이빗 토큰(Private Token)**을 사용합니다.
사용자별 인증이 필요한 프로덕션 애플리케이션은
[서버 사이드 OAuth 흐름](#서버-사이드-oauth-흐름)을 참고하세요.

<br>

## 프라이빗 토큰 생성

**사전 요구사항:** Eventbrite 계정. 계정이 아직 없다면
[계정 만들기](https://www.eventbrite.com/signin/)를 클릭하세요.

1. [Eventbrite](https://www.eventbrite.com/signin/)에 로그인하세요.
2. 프로필을 클릭한 후 **계정 설정(Account Settings)**으로 이동하세요.
3. **Developer Links** → **API Keys**를 선택하세요.
4. **API 키 만들기(Create API Key)**를 클릭하고 필수 항목을 입력하세요.
5. **키 만들기(Create Key)** 를 클릭하세요.
6. **프라이빗 토큰(Private Token)**을 복사해서 안전한 곳에 저장하세요.
이 페이지를 벗어나면 다시 확인할 수 없습니다.
토큰을 잃어버렸다면 [API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서 새 토큰을 발급받으세요.

**확인:** 아래 요청을 보내세요. 계정 정보가 포함된 `200 OK` 응답을 반환하면 토큰이 정상적으로 작동하는 것입니다.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/"
```

**이미 API 키가 있다면:** [API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서
앱 우측의 **API 키, 클라이언트 시크릿 및 토큰 보기**를 클릭하고
프라이빗 토큰을 복사하세요.

<br>

## 요청에 토큰 추가하기

`Authorization` 헤더를 사용해서 요청을 인증하세요.
모든 환경에서 권장하는 방법입니다.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/"
```

`YOUR_PRIVATE_TOKEN`을 **프라이빗 토큰 생성** 단계에서 복사한 토큰으로 바꾸세요.

**쿼리 파라미터 방식 (비권장):** URL 파라미터로 토큰을 전달할 수도 있습니다.
단, 프로덕션 환경에서는 사용하지 마세요. URL에 포함된 토큰은 서버와 브라우저 로그에 기록되므로
보안 위험이 생길 수 있습니다.

```bash
https://www.eventbriteapi.com/v3/users/me/?token=YOUR_PRIVATE_TOKEN
```

<br>

## 토큰 보안 관리

프라이빗 토큰을 비밀번호처럼 관리하세요.
토큰이 외부에 노출되면 누구나 내 계정으로 API 요청을 보낼 수 있습니다.

- 소스 코드가 아닌 환경 변수에 토큰을 저장하세요.
- 개발 환경과 프로덕션 환경에서는 별도의 토큰을 사용하세요.
- 버전 관리 시스템에 토큰을 커밋하거나 공개 저장소에 포함하지 마세요.
- 더 이상 사용하지 않는 토큰은
[API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서
삭제하세요.

<br>

## 서버 사이드 OAuth 방식

사용자가 자신의 Eventbrite 계정으로 로그인하는 웹 앱처럼,
사용자별 인증이 필요한 경우에 서버 사이드 OAuth 방식을 사용하세요.

**사전 요구사항:**[API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서
발급받은 API 키와 클라이언트 시크릿.

### 1단계. 사용자를 리디렉션하기

사용자를 Eventbrite 인증 URL로 보내세요.

```bash
<https://www.eventbrite.com/oauth/authorize>
  ?response_type=code
  &client_id=YOUR_API_KEY
  &redirect_uri=YOUR_REDIRECT_URI
```

### 2단계. 인증 코드 받기

Eventbrite는 인증 코드를 쿼리 파라미터로 추가해서 사용자를 리디렉션 URI로 돌려보냅니다.

```bash
http://localhost:8080/oauth/redirect?code=YOUR_ACCESS_CODE
```

### 3단계. 코드를 액세스 토큰으로 교환하기

인증 코드, API 키, 클라이언트 시크릿을 포함한 POST 요청을 보내세요.

```bash
curl --request POST \
  --url "https://www.eventbrite.com/oauth/token" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --data "grant_type=authorization_code" \
  --data "client_id=YOUR_API_KEY" \
  --data "client_secret=YOUR_CLIENT_SECRET" \
  --data "code=YOUR_ACCESS_CODE" \
  --data "redirect_uri=YOUR_REDIRECT_URI"
```

### 4단계. 액세스 토큰 확인하기

요청이 성공하면 `access_token`이 포함된 JSON 객체를 반환합니다.
이 토큰을 프라이빗 토큰과 동일하게 모든 후속 요청의 `Authorization` 헤더에 포함해서 사용하세요.

<br>

## 다음 단계

- [API 레퍼런스](../api/api-reference.md)
- [코드 예제](../examples/code-examples.md)
- [빠른 시작 가이드](../get-started/qucik-start.md)

<br>
