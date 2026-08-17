# 올레포트 파트너 블로그 프로젝트 — 진행상황 (PROGRESS)

> **이 문서 하나로 이어서 작업할 수 있도록 작성된 상태 문서입니다.**
> 다음 세션의 Claude에게: 이 저장소 전체(특히 이 파일)를 읽으면 프로젝트 맥락이 복원됩니다. "다음 할 일" 섹션부터 이어가면 됩니다.

최종 업데이트: 2026-08-17 (Asia/Seoul)

---

## 1. 프로젝트 개요

- **목표**: 올레포트(allreport.co.kr) 파트너 프로그램으로 블로그 판매 수익 극대화
- **수익 구조**: 블로그 글 → 올레포트 자료 상세페이지 링크(`https://www.allreport.co.kr/search/detail.asp?sid=leesk55&pk={자료번호}`) → 구매 시 파트너 커미션 (업계 최고 80% 홍보 수익률 등업 구조)
- **파트너 ID**: `leesk55` (두 블로그 모두 기존 글에서 이 ID 사용 중 — 공개 게시글에 노출된 값이라 여기 기록함. 비밀번호·토큰은 이 저장소에 절대 저장하지 말 것)

## 2. 운영 자산

| 사이트 | 플랫폼 | 상태 | 비고 |
|---|---|---|---|
| kakao.ivyro.net (카카오레포트) | 그누보드(EUC-KR, SmartEditor2) | 활발 (일 방문 ~1,200) | 게시판: free=레포트, gallery=자기소개서, notice=실험과제, qa=방송통신. 관리자 계정 "최고관리자"로 게시 |
| hadesyi.cafe24.com (애플 레포트) | 워드프레스 (블록에디터) | 운영 중 | WebFetch로는 빈 페이지로 보임(브라우저 필요). 계정 hadesyi. 글은 REST API로 게시 가능 |
| 네이버 블로그 (m.blog.naver.com 등) | — | **실제 유입의 주력** | 주간 식별 유입의 대부분. 이 세션에서는 미작업 |
| dothome.co.kr 호스팅 사이트 | — | 유입 소량 확인 | 미확인 자산 |

- 올레포트 파트너 대시보드: https://www.allreport.co.kr/partner/ (로그인 필요, leesk55)
  - 인기자료 랭킹: `/partner/recom/inki.asp` (기간 mode=month|week, 구분 cat=카테고리명, 정렬 ord=down|income|price|pk — **ord=income(수익순)이 핵심**)
  - 홍보코드 생성: `/partner/recom/auto.asp`, 유입분석: `/partner/stats/refer.asp`, 판매분석: `/partner/stats/down.asp`

## 3. 완료된 작업 (2026-08-16 ~ 08-17)

### 분석·리서치
- 올레포트 수익모델·파트너 구조 분석, 두 블로그 플랫폼·게시판 구조 파악
- 파트너 대시보드에서 **최근 30일 카테고리별 수익순 랭킹** 수집 → `data/올레포트_수익랭킹_2026-08.md`
- 자료 12건 상세페이지 검증(제목·가격·구성) 완료
- **핵심 진단**: 주간 유입 895회 대비 최근 1개월 판매 2건 → 전환율 문제. 대응: 단일 자료 나열 대신 시즌 묶음 큐레이션 + 고단가 자료 우선 배치 + "목차·미리보기 확인" CTA

### 콘텐츠 제작·게시 (6건 모두 게시 완료 ✅)

| # | 사이트/게시판 | 제목(요약) | 게시 URL |
|---|---|---|---|
| K1 | 카카오/자기소개서 | 2026 하반기 채용 자소서 합격자료 6선 | https://kakao.ivyro.net/bbs/board.php?bo_table=gallery&wr_id=798 |
| K2 | 카카오/실험과제 | 어린이집 2학기·평가제 관찰일지·발달평가 모음 | https://kakao.ivyro.net/bbs/board.php?bo_table=notice&wr_id=1064 |
| K3 | 카카오/레포트 | 간호학과 2학기 실습 vSim·CPR·케이스 모음 | https://kakao.ivyro.net/bbs/board.php?bo_table=free&wr_id=4378 |
| H1 | hadesyi | 가을 자격증 기출·요약 (소방·산업안전·전기) | https://hadesyi.cafe24.com/14232/ |
| H2 | hadesyi | 사회복지 현장실습 서류 모음 | https://hadesyi.cafe24.com/14233/ |
| H3 | hadesyi | 독학사 3단계 가정학 족보 모음 | https://hadesyi.cafe24.com/14234/ |

원본 HTML: `posts/` 폴더. 전략·게시 방법: `게시_가이드_및_수익전략.md`

### 게시 자동화 기술 노트 (재사용 가능)

**그누보드(카카오레포트)** — 브라우저 자동화 필수:
1. `/bbs/write.php?bo_table={게시판}` 접속 (로그인 필요)
2. `#wr_subject`에 제목 입력, SmartEditor2에 `oEditors.getById['wr_content'].exec('PASTE_HTML',[html])` → `exec('UPDATE_CONTENTS_FIELD',[])`
3. ⚠️ `form.requestSubmit()`·`form.submit()` 직접 호출은 **토큰 검증 실패**("올바른 방법으로 이용해 주십시오") — 반드시 `document.getElementById('btn_submit').click()` 사용 (버튼에 토큰 발급 로직 바인딩)
4. ⚠️ 오류 시 alert 팝업이 자동화를 블로킹함 → 제출 전 `form.target`을 sandbox iframe(`sandbox="allow-same-origin allow-forms"`, allow-scripts 제외)으로 돌리면 팝업 없이 결과 확인 가능
5. 성공 판정: iframe이 `/bbs/board.php`(글 보기)로 이동

**워드프레스(hadesyi)** — REST API가 가장 깔끔:
1. `/wp-admin/post-new.php` 접속(로그인 필요) → `wpApiSettings.nonce` 확보
2. `fetch(wpApiSettings.root+'wp/v2/posts', {method:'POST', headers:{'X-WP-Nonce':nonce,...}, body:{title, content(HTML), status:'publish'}})` → 201 + 글 링크 반환

**기타**: allreport의 `/partner/*`, `/search/search.asp`는 robots 차단으로 WebFetch 불가(브라우저로 접근). `/search/detail.asp`는 WebFetch 가능. 사이트 인코딩 EUC-KR(파라미터 주의).

## 4. 다음 할 일 (우선순위순)

1. **네이버 블로그 변형본** (효과 최대 예상): 이번 6개 글 주제를 네이버 블로그용으로 문장 재작성(유사문서 방지 — 복붙 금지). 실제 유입 주력 채널. 네이버는 외부 상업 링크에 민감하므로 글당 링크 1~2개로 절제 권장
2. **방통대 2학기 콘텐츠** (9월 말~10월 초 게시): 중간과제물 공개 직후가 피크. 과목 후보: 철학의이해(pk=28311068), 사회복지시설과사회적경제(pk=50036166), 심리학에게묻다, 대학영어. 카카오 qa(방송통신) 게시판에
3. **월간 랭킹 갱신**: 매월 초 파트너 대시보드 인기자료(수익순) 확인 → 기존 글 자료 교체·신규 글 작성 (이 저장소 data/ 파일도 갱신)
4. **성과 측정**: `/partner/stats/down.asp`(판매)와 `/partner/stats/refer.asp`(유입) 주기 확인. 기준선: 2026-08-16 시점 월 판매 2건 → 게시 후 변화 추적
5. **시즌 캘린더**: 10월 어린이집 2학기 발달평가 피크(K2 글 끌어올리기), 11월 기사 실기 3회차(H1 갱신), 2~3월 신학기 적응일지 시즌
6. hadesyi 사이트에 카테고리 정리(현재 미분류) 및 기존 글과 내부링크 연결 — SEO 개선

## 5. 이어서 작업하는 방법 (다음 세션용)

1. 이 저장소를 Claude(Cowork)에게 보여주고: "PROGRESS.md 읽고 이어서 진행해줘"
2. 게시 작업이 필요하면 크롬 브라우저(Claude in Chrome 연결)에서 **사용자가 직접 로그인**해 둘 것: kakao.ivyro.net(관리자), hadesyi.cafe24.com/wp-admin, www.allreport.co.kr(leesk55), (필요시) blog.naver.com
   - 로그인은 Claude 작업 탭과 같은 크롬 프로필에서 해야 하며, 비밀번호는 채팅·저장소에 절대 남기지 말 것
3. 콘텐츠 작성 규칙: 실제 판매 데이터(수익순) 기반 자료 선정 → 상세페이지에서 제목·가격 검증 → 파트너 링크 `sid=leesk55` → 제휴 공시 문구 필수 → 표절 주의 안내 포함 (기존 글 스타일은 posts/ 참고)
