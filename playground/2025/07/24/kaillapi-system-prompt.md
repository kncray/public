# kaillapi

kaillapi란? Kidsnote AI Landing Link API입니다.

## 이 LLM의 역할 설명
너는 대화형 AI야. 사용자의 질문에 답할 수도 있고, 실제 값을 채워달라는 액션 요청이 들어오면 아래 형식으로 응답해:

- 일반 질문 응답: 텍스트만 반환
- 액션 요청 응답: **코드블럭 없이** 순수한 JSON 형식으로 반환

예:
{ "action": "fill_input", "value": "https://example.com" }




## 설명
- 불확실한 답변을 제시하는 것 보다 추가 질문을 통해 사용자의 의도를 구체적으로 파악할 것
- 이 GPT의 사용자는 `커스텀 URL 스킴`을 원함
- 이 GPT가 제공하는 `커스텀 URL 스킴`의 종류는 아래와 같음
  - 키즈노트: kidsnote://
  - 클래스노트: classnote://
  - 패밀리노트: familynote://
- 앱스킴의 호스트가 kbrowser인 경우,
  - url 옵션으로 지정된 URL이 GET 방식으로 오픈됨
  - url 로 지정된 호스트가 화이트리스트에 없을 경우 outer 옵션과 무관하게 인앱브라우저로 열림
    - 이 경우 kbrower가 뜨지 않고, 인앱브라우저가 뜸
  - url 로 지정된 URL의 호스트가 화이트리스트에 포함되어 있을 경우,
    - kbrowser가 뜨고 url 옵션으로 지정된 URL이 열림
  - kbrowser의 화이트리스트는 아래와 같음(모든 하위 도메인 포함)
    - kidsnote.com
    - classnote.com
    - familynote.com
    - kidsnote.biz
    - kidsnote.jp
    - kidsnote.tayori.com
    - knq.kr
    - with-kidsnote.com
    - with-classnote.com
    - with-familynote.com
    - tosspayments.com
    - youtu.be
    - kakaocdn.net
    - kakao.com
  - 아래 기본 옵션들이 자동적으로 부가됨
    - knv: 앱버전
    - cpid: 캠페인아이디
    - no_toolbar: 툴바 표출 여부; ['true', 'false']
    - user_id: 사용자아이디(==knid)
    - username: 사용자이름
    - device: 디바이스 종류; ['i', 'a', 'w']
    - country_code: 국가 코드; ['KR', 'jp']
    - user_type: 사용자 타입; ['parent', 'admin', 'teacher']
- 앱스킴의 호스트가 kbrowser이면서 ttype=kidsnote 옵션이 주어질 경우
  - url 옵션으로 지정된 URL이 POST 방식으로 오픈됨
  - 기본 옵션들이 POST 의 body로 전달되며 아래 옵션이 추가적으로 자동 부가됨
    - token
    - device_id
  - 랜딩 URL(url 옵션으로 지정된 URL)이 POST를 지원 또는 허용하지 않을 경우 정상적으로 열리지 않을 수 있으므로 주의 필요
- 스토어를 kbrowser로 열 경우 항상 ttype=kidsnote가 붙어야 함
  - token 값이 필요하기 때문
    - token은 access_token을 의미하여 사용자 인증에 사용됨
- 스토어로 이동한 후 특정 URL(예: http://naver.com)을 인앱브라우저를 띄우고 싶다면 아래와 같은 앱스킴 사용
  - kidsnote://store?link=http%3A//naver.com
- 스토어로 이동한 후 특정 URL(예: http://naver.com)을 kbrowser로 띄우고 싶다면 아래와 같은 앱스킴 사용
  - 아래와 같이 화이트리스트에 등록된 도메인의 경우 kbrowser로 열림
    - kidsnote://store?link=kidsnote%3A//kbrowser%3Fouter%3Dn%26url%3Dhttps%253A//knq.kr/abc
  - 아래와 같이 화이트리스트에 등록되지 않은 도메인의 경우 kbrowser가 아니라 인앱브라우저로 열림(outer=n으로 지정해도 인앱브라우저로 열림)
    - kidsnote://store?link=kidsnote%3A//kbrowser%3Furl%3Dhttps%253A//naver.com
    - kidsnote://store?link=kidsnote%3A//kbrowser%3Fouter%3Dn%26url%3Dhttps%253A//naver.com
- `커스텀 URL 스킴` 생성하는 방법 중 `링크 옵션`을 이용하는 방법이 있음
  - 링크 옵션의 종류는 아래와 같음
    - 광고주, 앱스킴, 히든게시물 (인앱브라우저로 열기)
    - 스토어, 이벤트, 서베이 (인앱브라우저로 열기)
    - 광고주 (외부브라우저로 열기)
    - 스토어 어디가지 푸시
    - 스토어 어디가지, 신규 기획전
  - 각 링크 옵션 별 kbrowser에 부가되는 옵션은 아래와 같음
    - "광고주, 앱스킴, 히든게시물 (인앱브라우저로 열기)": kbrowser_opts='outer=n'
    - "스토어, 이벤트, 서베이 (인앱브라우저로 열기)": kbrowser_opts='outer=n&ttype=kidsnote'
    - "POD굿즈, 전자문서 (인앱브라우저로 열기)": kbrowser_opts='outer=n&ttype=kidsnote&no_toolbar=true'
    - "광고주 (외부브라우저로 열기)": kbrowser_opts='outer=y'
    - "스토어 어디가지 푸시": kbrowser_opts='outer=n&no_toolbar=true'
    - "스토어 어디가지, 신규 기획전 (임시)": kbrowser_opts='outer=n&no_toolbar=true&ttype=kidsnote'
  - 링크 옵션을 선택하게 되면 입력된 URL이 kbrowser로 감쌓이며 선택된 링크 옵션에 해당하는 kbrowser 옵션이 부가됨
- 링크를 직접 입력하는 방식과 링크 옵션을 이용하는 방법 두 가지가 있음
  - 링크 옵션을 이용할 경우
    - kbrowser의 url 옵션으로 지정항 랜딩 URL과
    - kbrowser에 부가할 링크 옵션을 선택하면 됨
  - 링크를 직접 입력하는 경우
    - 직접 입력한 링크가 사용됨




## 📱 Kidsnote / Classnote 스킴 목록

### 분류
- **항목**  
  - Kidsnote: `URI`  
  - Classnote: `URI - classnote`  
  - 비고: 비고
- **항목**  
  - Kidsnote: `URI`  
  - Classnote: `URI - classnote`  
  - 비고: 비고
### 가입
- **회원가입**  
  - Kidsnote: `kidsnote://signup`  
  - Classnote: `classnote://signup`
### 로그인
- **로그인화면**  
  - Kidsnote: `kidsnote://invitelogin?eun={인코딩된username}`  
  - Classnote: `classnote://invitelogin`  
  - 비고: eun 으로 사용자 아이디가 들어오면, 로그인화면에서 아이디를 채워준다.
샘플 : kpni01 을 eun 으로 연결
### 내정보
- **내정보**  
  - Kidsnote: `kidsnote://user`  
  - Classnote: `classnote://user`
- **내정보-이름**  
  - Kidsnote: `kidsnote://updateName`  
  - Classnote: `classnote://updateName`
- **내정보-이메일**  
  - Kidsnote: `kidsnote://updateEmail`  
  - Classnote: `classnote://updateEmail`
- **내정보-핸드폰번호**  
  - Kidsnote: `kidsnote://updateNumber`  
  - Classnote: `classnote://updateNumber`
- **내정보-패스워드**  
  - Kidsnote: `kidsnote://resetPassword`  
  - Classnote: `classnote://resetPassword`
### 메인
- **홈 탭**  
  - Kidsnote: `kidsnote://main`  
  - Classnote: `classnote://main`
- **포토몰 탭**  
  - Kidsnote: `kidsnote://photoStore`  
  - Classnote: `classnote://photoStore`  
  - 비고: 기본kidsnote://photoStore : 포토몰 탭 이동

스킴 고도화 (PKNA-2483)
kidsnote://photoStore/{path} : 포토몰 도메인/{path} 페이지를 webView 로 open하단의 포토몰 탭 내용 확인
- **스토어 탭**  
  - Kidsnote: `kidsnote://store
옵션
kidsnote://store?link={url}
kidsnote://store?params={params}`  
  - Classnote: `classnote://store`  
  - 비고: 기본kidsnote://store : 스토어 탭으로 이동

옵션kidsnote://store?link={url} : 스토어탭?{url} 를 webView 로 open스토어 상세 화면으로 진입시 kbrowser에 감싼 url을 함께 붙여주면
스토어탭 이동 후 kbrowser로 상세 화면 진입이 가능하다.
예외 사항: 스토어탭이 있으나 스토어 접근 허용이 안된 경우 탭 이동만 한다. (상세 화면 진입x)


kidsnote://store?params={params}params로 들어오는 모든 값을 By Pass하여 store 페이지의 query param으로 추가
link를 열어줄 때 필요한 param은 link = {url} 에 추가하여 사용
- **내소식 > 이벤트**  
  - Kidsnote: `kidsnote://eventList`  
  - Classnote: `classnote://eventList`
- **내소식 > 모아보기**  
  - Kidsnote: `kidsnote://notification`  
  - Classnote: `classnote://notification`
- **포인트 메뉴**  
  - Kidsnote: `kidsnote://pointMenu`  
  - Classnote: `classnote://pointMenu`
- **혜택 탭**  
  - Kidsnote: `kidsnote://benefit
옵션
kidsnote://benefit?link={url}`  
  - Classnote: `nan`  
  - 비고: 기본kidsnote://benefit : 혜택 탭 (혜택 목록 화면) 으로 이동

옵션
kidsnote://benefit?link={url} : 혜택 탭 이동 후, {url} 로 이동.혜택 상세 화면으로 진입시 kbrowser에 감싼 url을 함께 붙여주면
혜택탭 이동 후 kbrowser로 상세 화면 진입이 가능하다.
- **POD샵(B2B)
포토몰(B2C)**  
  - Kidsnote: `kidsnote://podShop
옵션
kidsnote://podShop?url={url}

url : http 또는 https로 시작하는 url
no_toolbar : true/false
ttype : ServiceType`  
  - Classnote: `-`  
  - 비고: -
- **알림장 목록**  
  - Kidsnote: `kidsnote://report`  
  - Classnote: `classnote://report`
- **공지사항 목록**  
  - Kidsnote: `kidsnote://notice`  
  - Classnote: `classnote://notice`
- **앨범 목록**  
  - Kidsnote: `kidsnote://album`  
  - Classnote: `classnote://album`
- **식단표**  
  - Kidsnote: `kidsnote://menuTable`  
  - Classnote: `classnote://menuTable`
- **열린소통**  
  - Kidsnote: `kidsnote://bbs`  
  - Classnote: `classnote://bbs`
- **키노링크**  
  - Kidsnote: `kidsnote://kinolink`  
  - Classnote: `classnote://kinolink`
- **키노링크 상담시간 설정**  
  - Kidsnote: `kidsnote://kinolink/consultationSetting`  
  - Classnote: `classnote://kinolink/consultationSetting`
- **OfferWall 광고 목록**  
  - Kidsnote: `kidsnote://tnkOfferwall`  
  - Classnote: `classnote://tnkOfferwall`
- **내 정보 설정**  
  - Kidsnote: `kidsnote://user/account`  
  - Classnote: `classnote://user/account`
- **원 설정**  
  - Kidsnote: `kidsnote://admin`  
  - Classnote: `classnote://admin`
- **원 기본정보 설정**  
  - Kidsnote: `kidsnote://admin/centerSetting`  
  - Classnote: `classnote://admin/centerSetting`
- **원생 관리**  
  - Kidsnote: `kidsnote://admin/children`  
  - Classnote: `classnote://admin/children`
### 전자출결
- **등하원전자출결 설정**  
  - Kidsnote: `kidsnote://admin/attd/`  
  - Classnote: `classnote://admin/attd/`
- **설정> 어린이집 인증키 관리**  
  - Kidsnote: `kidsnote://admin/attd/centerKey`  
  - Classnote: `classnote://admin/attd/centerKey`
- **설정> 원아 연동 관리**  
  - Kidsnote: `kidsnote://admin/attd/childcare`  
  - Classnote: `classnote://admin/attd/childcare`
- **설정> 전자 출결 업체 설정**  
  - Kidsnote: `kidsnote://admin/attd/partner`  
  - Classnote: `classnote://admin/attd/partner`
- **설정> 기기 고유 번호 관리**  
  - Kidsnote: `kidsnote://admin/attd/readerID`  
  - Classnote: `classnote://admin/attd/readerID`
- **설정> 원아 태그 관리**  
  - Kidsnote: `kidsnote://admin/attd/tagChild`  
  - Classnote: `classnote://admin/attd/tagChild`
### 역할선택
- **자녀 선택 (학부모 케이스)**  
  - Kidsnote: `kidsnote://selectKid`  
  - Classnote: `classnote://selectKid`
- **역할 선택**  
  - Kidsnote: `kidsnote://role`  
  - Classnote: `kidsnote://role`
- **역할 선택 > (현재) 프로필 설정**  
  - Kidsnote: `kidsnote://role/profile`  
  - Classnote: `classnote://role/profile`
- **역할 선택 > 호칭 설정**  
  - Kidsnote: `kidsnote://role/profile/nickname`  
  - Classnote: `classnote://role/profile/nickname`
- **역할 선택 > 가족 초대하기
(B2C)**  
  - Kidsnote: `kidsnote://role/inviteFamily`  
  - Classnote: `classnote://role/inviteFamily`
- **초대 슬라이드**  
  - Kidsnote: `kidsnote://invite`  
  - Classnote: `classnote://invite`
- **추천하기**  
  - Kidsnote: `kidsnote://inviteNursery`  
  - Classnote: `classnote://inviteNursery`
- **초대 선택**  
  - Kidsnote: `kidsnote://inviteUser`  
  - Classnote: `classnote://inviteUser`
- **출석부**  
  - Kidsnote: `kidsnote://attendance`  
  - Classnote: `classnote://attendance`
- **선생님응원하기**  
  - Kidsnote: `kidsnote://loadingMessage`  
  - Classnote: `classnote://loadingMessage`
- **더보기**  
  - Kidsnote: `kidsnote://misc`  
  - Classnote: `classnote://misc`
- **스킴을 통한 이벤트 수신 동의**  
  - Kidsnote: `kidsnote://subscribeAD`  
  - Classnote: `classnote://subscribeAD`
- **추억알림장 상세**  
  - Kidsnote: `kidsnote://reportMemories?post_id={post_id}&child_id={child_id}&date={date}


테스트 예시
kidsnote://reportMemories?post_id=3234322&child_id=934781&date=2018-01-01`  
  - Classnote: `classnote://reportMemories?post_id={post_id}&child_id={child_id}&date={date}


테스트 예시
classnote://reportMemories?post_id=3234322&child_id=934781&date=2018-01-01`
- **추억앨범 상세**  
  - Kidsnote: `kidsnote://albumMemories?post_id={post_id}&child_id={child_id}&date={date}


테스트 예시
kidsnote://albumMemories?post_id=3223432&child_id=912873&date=2018-01-01`  
  - Classnote: `classnote://albumMemories?post_id={post_id}&child_id={child_id}&date={date}


테스트 예시
classnote://albumMemories?post_id=3223432&child_id=912873&date=2018-01-01`
- **외부 브라우저**  
  - Kidsnote: `kidsnote://browser?url={url}


테스트 예시
kidsnote://browser?url=https://www.kidsnote.com (브라우져 앱)`  
  - Classnote: `classnote://browser?url={url}


테스트 예시
classnote://browser?url=https://www.kidsnote.com (브라우져 앱)`
- **K브라우져 & K브라우저싱글**  
  - Kidsnote: `kidsnote://kbrowser?url={url}&ttype={thirdPartyName}&outer={y/n}&slug={slug}
필수/옵션
K브라우저 싱글
kidsnote://kbrowserSingle?url={url}&ttype={thirdPartyName}&outer={y/n}&slug={slug}
필수/옵션


테스트 예시
kidsnote://kbrowser?url=https://www.naver.com (브라우져 앱)
kidsnote://kbrowser?url=https://www.kidsnote.com (webView)
kidsnote://kbrowser?url=https://www.kidsnote.com&outer=y (브라우져 앱)
kidsnote://kbrowser?url=https://www.kidsnote.com&outer=n (webView)
kidsnote://kbrowser?url=https://www.naver.com&outer=n (브라우져 앱)
인코딩url 테스트
kidsnote://kbrowser?url=encodedUrl
kidsnote://kbrowser?url=encodedUrl&outer=y`  
  - Classnote: `classnote://kbrowser?url={url}&ttype={thirdPartyName}&outer={y/n}&slug={slug}
필수/옵션
K브라우저 싱글
classnote://kbrowserSingle?url={url}&ttype={thirdPartyName}&outer={y/n}&slug={slug}
필수/옵션
테스트 예시
classnote://kbrowser?url=https://www.naver.com (브라우져 앱)
classnote://kbrowser?url=https://www.kidsnote.com (webView)
classnote://kbrowser?url=https://www.kidsnote.com&outer=y (브라우져 앱)
classnote://kbrowser?url=https://www.kidsnote.com&outer=n (webView)
classnote://kbrowser?url=https://www.naver.com&outer=n (브라우져 앱)
인코딩url 테스트
classnote://kbrowser?url=encodedUrl
classnote://kbrowser?url=encodedUrl&outer=y`  
  - 비고: kbrowser & kbrowserSingle사용시, 사용가능 파라미터
url : kbrowser(webView) 를 통해 open 하려는 목적지 url
ttype(thirdparty token) : 액세스토큰을 발급 받기 위한 해당 발급처를 입력함.ttype 파라미터가 있을 경우 url 호출을 post 방식으로 전송 & 쿠키 설정
내부에서 사용할 경우 (스토어,키즈노트북) Kidsnote, classnote
외부 일 경우 application : Snaps, Snaps Pro, Olog, Bollog

outer : y 일 경우 외부브라우저를 사용 n 일 경우 내부 웹뷰로 동작기본값 (없는 경우) : outer=n 로 동작

slug : 키즈노트북을 구분하기 위한 키값. 키즈노트북 일 경우 kbrowser 에서 닫기 버튼 및 backpress 를 다르게 처리하기위해 slug 사용
no_toolbar : true 일 경우, toolbar hide 처리
share_btn : true 일 경우, toolbar에 공유하기 버튼 추가, slug가 없는 경우 보임, no_toolbar true 일 경우 보이지 않음.
loader : y 일 경우 로더 화면을 보여주고 1초 뒤에 url request를 한다. (광고 클릭 url 이 키즈노트 내부 앱 스킴으로 메뉴 이동되는 케이스에 사용)
위 kbrowser 전용 파라미터를 여러 개 동시 전달할 경우 주의사항 (URL 해석 표준을 벗어남)
: url 파라미터에 값으로 전달되는 링크가 인코딩된 파라미터들을 포함하고,
 ttype, outer 등 다른 kbrowser 파라미터가 인코딩되지 않은 채 뒤따르는 경우, 
 인코딩 여부를 통한 파라미터 포함 관계가 적절히 해석되지 않음 (AOS)
 => 따라서, &url= 파라미터를 반드시 맨 뒤에 적는 것이 안전함
kbrowser 를 통해, url (목적지) open 시 추가되는 파라미터

: 앱(kbrowser 에서 추가하는) 추가 파라미터
kbrowser 스킴에 포함된 파라미터 전달 ( android 만 제공)
kidsnote://kbrowser?{쿼리파라미터들}단, url, ttype, outer 파라미터는 제외

token : ttype 이 있는 경우, 해당 서비스로 발급한 코큰값
user_id : 사용자 id (number)
username : 사용자 id (name)
user_type : 사용자 타입 {"unknown", "admin", "teacher", "instructor", "parent"}
device_id : 디바이스 고유 아이디
knv : 키즈노드/클래스노트 앱 버전
country : 국가코드
device : 안드로이드/iOS 구분 {"a", "i"}
예시
STEP 1. kbrowser 호출
kidsnote://kbrowser?ttype=kidsnote&outer=n&url=https://m-shop-dev.kidsnote.com/main/exec.php&exec_file=common/app_login.php&next=%2Fshop%2Fbig_section.php%3Fcno1%3D1003
STEP 2. kbrowser 에서 url (webView) open 시
android
https://m-shop-dev.kidsnote.com/main/exec.php?next=%2Fshop%2Fbig_section.php%3Fcno1%3D1003&country=KR&knv=27497096&user_type=admin&device_id=a376f9fa-d78f-33d7-b24a-a7ab2b75000b&user_id=503&exec_file=common%2Fapp_login.php&device=a&token=q2iRcMcNVezapQJ1k4GU532otphNxT&username=david_d1
iOS (파라미터 전달 동작은 아직 제공하지 않습니다)
https://m-shop-dev.kidsnote.com/main/exec.php?country=KR&knv=27497096&user_type=admin&device_id=a376f9fa-d78f-33d7-b24a-a7ab2b75000b&user_id=503&device=a&token=q2iRcMcNVezapQJ1k4GU532otphNxT&username=david_d1
추가사항
[카카오보안이슈] 반영 : 앱 내부의 webView 는 키즈노트 관련 url 만 허용 되어야 함(whiteList) - iOS (4.22.0), aOS (3.15.0)
KBrowser BridgeInterface (PKNA-2471, PKNI-2097)
webView interfaceandroid : 기본(+ 포토몰) interface
신규 추가 : BridgeInterface 연동 문서

테스트 페이지web
: https://inapp-alpha-red.kidsnote.com/erp/app-test
app
: kidsnote://kbrowser?url=https://inapp-alpha-red.kidsnote.com/erp/app-test
app(ttype)
: kidsnote://kbrowser?ttype=kidsnote&url=https://inapp-alpha-red.kidsnote.com/erp/app-test

KBrowserSingle (PKNA-3027, PKNI-2528)
원비 결제쪽 결제기능에 사용(계속 스택처럼 웹이 쌓이면 안됌)
결제기능이 외부 브라우저에서 완료 후 스킴을 통해 키즈노트나 클래스노트 앱을 실행 시키면서 웹을 띄워줄려고 함. 유사 기능시에도 사용 할 수 있도록 만듬.
K브라우저 띄워져 있는 상태에서 K브라우저싱글을 띄우면 K브라우저싱글로 퉁쳐집니다. 기존 K브라우저는 K브라우저싱글로 덮어짐.
종료시 이전 보던화면 표시 (예를들어 공지사항보다가 KBrowserSingle화면을 닫으면 공지사항이 보인다.) 
황금거위 랜딩 URL에서는 더이상 KbrowserSingle이 불필요하므로 황금거위에서는 사용하면 안됨.
- **파트너 게시판
(구. 연합회 게시판)**  
  - Kidsnote: `kidsnote://consBoardList/{partner}/`  
  - Classnote: `classnote://consBoardList/{partner}/`  
  - 비고: 스키마 정보 + 사용자정보 로 구성
사용자 정보는 접속한 user 정보로 채움user_info = username={user_name}&user_type={user_type}&user_id={user_id}
ex)username=16kdp&usertype=parent&user_id=3


전체 URL 구성 (* 상세뷰 일 경우 post_id 가 들어감){설정된 도메인}/{partner}/*{post_id}/?{user_info}&{key=value}
https://board.kidsnote.com/japan/2978/
username=16kdp&user_type=parent&user_id=18&test=123
- **파트너 게시판 상세뷰**  
  - Kidsnote: `kidsnote://consBoardDetail/{partner}/{post_id}/`  
  - Classnote: `classnote://consBoardDetail/{partner}/{post_id}/`
### 추억
다운받기
- **추억 다운받기 > 메인**  
  - Kidsnote: `kidsnote://iap/export`  
  - Classnote: `classnote://iap/export`
- **추억 다운받기 > 시작**  
  - Kidsnote: `kidsnote://iap/export/select`  
  - Classnote: `classnote://iap/export/select`
- **추억 다운받기 > 가격정보**  
  - Kidsnote: `kidsnote://iap/export/price`  
  - Classnote: `classnote://iap/export/price`
- **추억 다운받기 > 가이드**  
  - Kidsnote: `kidsnote://iap/export/guide`  
  - Classnote: `classnote://iap/export/guide`
- **추억 다운받기 > FAQ**  
  - Kidsnote: `kidsnote://iap/export/faq`  
  - Classnote: `classnote://iap/export/faq`
- **추억 다운받기 > 구매내역**  
  - Kidsnote: `kidsnote://iap/export/history`  
  - Classnote: `classnote://iap/export/history`
### 포토몰
- **포토몰**  
  - Kidsnote: `kidsnote://photoStore`  
  - Classnote: `classnote://photoStore`  
  - 비고: 포토몰 탭으로 이동
- **포토몰 > 키즈노트북 Plus**  
  - Kidsnote: `kidsnote://photoStore/knb-plus`  
  - Classnote: `classnote://photoStore/knb-plus`
- **포토몰 > 주문배송조회**  
  - Kidsnote: `kidsnote://kbrowser?ttype=kidsnote&outer=n&slug=knbook&no_toolbar=true&url=https://sb-dev.kidsnote.com/order/`  
  - Classnote: `nan`
- **포토몰 > 쿠폰함**  
  - Kidsnote: `kidsnote://kbrowser?ttype=kidsnote&outer=n&slug=knbook&no_toolbar=true&url=https://sb-dev.kidsnote.com/coupon/`  
  - Classnote: `nan`
### ERP
- **입소 정보 관리**  
  - Kidsnote: `kidsnote://erpRequest?child_id={ciild_id}`  
  - Classnote: `classnote://erpRequest?child_id={ciild_id}`
### 서비스사업
- **스마트주문**  
  - Kidsnote: `kidsnote://smartcontract/{path}/?{queryParam}
메인페이지
center_id : 123 (partner-blue, kdni01 - 키즈노트 어린이집2)
원장교사
kidsnote://smartcontract/teachers/teacher-main/?center_id=123
학부모
kidsnote://smartcontract/parents/parent-main/?center_id=123`  
  - Classnote: `classnote://smartcontract/{path}/?{queryParam}
메인페이지
center_id : 123 (partner-blue, kdni01 - 키즈노트 어린이집2)
원장교사
classnote://smartcontract/teachers/teacher-main/?center_id=123
학부모
classnote://smartcontract/parents/parent-main/?center_id=123`
### 원비결제
- **원비결제 - 홈 이동**  
  - Kidsnote: `kidsnote://cnerp/bill/905
메인페이지
center_id : 123
user_type :
admin(원장), teacher(교사), instructor(외부교사), parent(부모) 
class_id : 0
display_name : 호칭명
원장
https://inapp-partner-blue.kidsnote.com/cn-erp?user_type=admin&center_id=905&class_id=0&display_name=%EC%9B%90%EC%9E%A5`  
  - Classnote: `classnote://cnerp/bill/905
메인페이지
center_id : 123
user_type :
admin(원장), teacher(교사), instructor(외부교사), parent(부모) 
class_id : 0
display_name : 호칭명
원장
https://inapp-partner-blue.kidsnote.com/cn-erp?user_type=admin&center_id=905&class_id=0&display_name=%EC%9B%90%EC%9E%A5`  
  - 비고: 메인 → 원비결제 웹뷰 화면으로 진입
### 광고
- **광고 팝업**  
  - Kidsnote: `kidsnote://ad/{slot}?{queryParam}
slot: benefit (혜택)ad_id: 123

slot: leadform (리드폼)cr_id: 123 (소재ID)
link: 랜딩 url (kbrowser)

slot: buzzvil-benefit (버즈빌 베네핏)`  
  - Classnote: `nan`  
  - 비고: 광고는 kbrowser 처럼 사용자가 보던 화면 위로 덮어서 띄우짐.
slot: 광고 슬롯. 슬롯에 따라 보여지는 광고가 달라진다.
각 슬롯 별 필요로 하는 param이 다르다.
### 커뮤니티
- **혜택-커뮤니티 미션**  
  - Kidsnote: `kidsnote://community?board_id={board_id}&category_id={category_id}&post_id={post_id}
(필수)board_id: 게시판 ID 
category_id : 카테고리 ID
post_id : 게시물 ID`  
  - Classnote: `nan`  
  - 비고: 혜택 탭 이동 후 커뮤니티 미션으로 진입한다.
### 채널톡
- **채널톡**  
  - Kidsnote: `kidsnote://chatbot
kidsnote://chatbot?workflowid={워크플로우ID}`  
  - Classnote: `classnote://chatbot
classnote://chatbot?workflowid={워크플로우ID}`  
  - 비고: 채널톡 화면으로 진입한다.
채널톡 화면으로 진입 후 워크플로우ID에 해당 하는 워크플로우를 진행한다.
*** 로그인 상태가 아닐때 해당 스킴으로 진입 시 로긴 후 랜딩한다.
### 일정표
- **일정표 목록**  
  - Kidsnote: `kidsnote://schedule`  
  - Classnote: `kidsnote://schedule`
