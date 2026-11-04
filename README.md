# 미스터블루 (207760) 기업 노트

`stock-tracker` 저장소의 구조(정적 페이지 + Netlify Functions)를 그대로 가져와 미스터블루용으로 다시 구성한 사이트입니다. 원본 `stock-tracker`는 수정하지 않았습니다.

## 폴더 구성

```
├─ public/                      ← Netlify publish 폴더
│   ├─ index.html               화면 뼈대 (섹션 01~15)
│   ├─ app.js                   렌더링·차트·필터·밸류 계산기
│   ├─ market.js                '주가가 왜 움직였나' + '주변 시장'
│   ├─ img/                     무협 4대천왕 신작 표지
│   └─ data/mrblue-data.js      ★ 내용 데이터 — 업데이트는 여기만
├─ netlify/functions/
│   ├─ stock.js                 KIS 시세 (기본 종목 207760)
│   ├─ news.js                  네이버 뉴스 (기본 검색어 '미스터블루')
│   └─ notes.js                 사이트에서 기록 추가 (Blobs 저장소 'mrblue-notes')
├─ netlify.toml                 publish = "public", functions = "netlify/functions"
└─ package.json
```

## Netlify 배포

1. Netlify → **Add new site → Import an existing project → GitHub → `ezejwn/misterblue`** 선택
   - Branch: `main` · Build command: 비워둠 · Publish directory: `public` (netlify.toml이 자동 지정)
   - ⚠️ 드래그 앤 드롭 배포는 함수가 안 올라가므로 반드시 Git 연결로 배포
2. Site configuration → **Environment variables**:

| 이름 | 필수 | 내용 |
|---|---|---|
| `KIS_APPKEY` | ✅ (시세) | 한국투자증권 Open API 앱키 |
| `KIS_APPSECRET` | ✅ (시세) | 〃 시크릿 |
| `NAVER_CLIENT_ID` | | 네이버 검색 API (실시간 뉴스) |
| `NAVER_CLIENT_SECRET` | | 〃 |
| `NOTE_ADMIN_TOKEN` | | 사이트에서 기록 추가·삭제용 비밀 문자열 (아무 긴 문자열) |

   기존 stock-tracker 사이트와 같은 키를 써도 됩니다(토큰은 앱키별로 저장돼 서로 꼬이지 않음).
3. Deploys → **Trigger deploy → Clear cache and deploy site**
4. 확인: `https://<사이트주소>.netlify.app/.netlify/functions/stock?type=price` 에서 `"ok":true` 가 나오면 연결 성공

| 열었을 때 | 의미 |
|---|---|
| 404 | 함수 미배포 — Git 연결로 배포했는지 확인 |
| `환경변수가 없습니다` | 2번 후 재배포 안 함 |
| `토큰 발급 실패` | 키 오류 또는 1분 1회 발급 제한 — 1분 뒤 재시도 |

환경변수가 없어도 페이지는 뜹니다. 시세는 참고가(10.21 종가 2,055원)로, 뉴스·기록 추가는 꺼진 상태로 표시됩니다.

## 정보 업데이트

`public/data/mrblue-data.js`를 고쳐 push하면 자동 재배포됩니다.

| 새로 생긴 것 | 고칠 곳 |
|---|---|
| 미팅·IR·통화 | `LOG` 맨 위 |
| 기사 | `NEWS` 맨 위 (중요하면 `LOG`에도) |
| 분기 실적 (3Q26 등) | `QUARTERS` 행 추가, `BS`·`META.equity`·`META.ttmSales` 갱신 |
| IP·신작·게임 진척 | `ITEMS`의 `stage`·`size`·`updated` |
| 답을 들은 질문 | `OPEN`의 `status`·`answer`·`date` |
| 투자 관점 | `THESIS` |
| 수정 내역 | `CHANGELOG` 맨 위 |

황성 작가 표지 2종(화산검종·불사신의)은 원본이 커서 자리만 표시해 두었습니다. `public/img/`에 jpg(가로 600px 정도)를 넣고 `index.html`의 `.ph` 칸을 `<img src="img/파일명.jpg">`로 바꾸면 됩니다.

## 공개 범위
netlify.app 주소는 링크를 아는 누구나 볼 수 있습니다(검색 노출만 `noindex`로 막음). 내부 엑셀 수치·보도자료 초안·미공개 2차 표지가 들어 있으니 공유 범위에 유의하세요.
