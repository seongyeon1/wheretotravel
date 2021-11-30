# 문화관광 데이터 분석
뉴노멀 관광 트렌드에 적합한 지역 도출

# 기획 배경
![슬라이드3](https://user-images.githubusercontent.com/83098550/143670305-56f6f9f7-0df1-4160-9165-48dab6b073ce.PNG)
![슬라이드4](https://user-images.githubusercontent.com/83098550/143670306-bc34fc7f-f6e4-48a5-b054-73597c3505ee.PNG)
![슬라이드5](https://user-images.githubusercontent.com/83098550/143670307-89f59133-dbca-4c36-8167-3be969e6a73c.PNG)
![슬라이드6](https://user-images.githubusercontent.com/83098550/143670309-a34024b0-6108-402d-91ec-60a031d2a563.PNG)


## 코로나 이후 달라진 관광 트렌드 키워드: 생활관광, 친환경, 언택트, 안전여행
![슬라이드7](https://user-images.githubusercontent.com/83098550/143670311-e6eeb0bd-b724-406b-b3dc-5058c5456ea0.PNG)

# 데이터 소개 및 분석
## 뉴노멀 관광 키워드에 적합한 여행지 선정 기준
![슬라이드8](https://user-images.githubusercontent.com/83098550/143670312-0bfecbb9-b33d-4550-a58d-8b30d4f1b78a.PNG)

## 매출이 적은 지역 선정
- 사용 데이터 : 신한카드 내국인 데이터
- 코로나 이전(2020년)대비 코로나 이후 업종 소분류의 ‘교통‘의 취급액 자료를 바탕으로 관광산업 정책을 우선순위로 마련할 지역을 선정하고자 하였다.
- 코로나의 영향이 없었더라면 매출이 크게 감소하지 않았을 것이라 생각하고 코로나를 기점으로 매출의 변화가 큰 지역을 선정해보기 위해 데이터를 분석하였다.

## 교통이 불편한 지역 선정 
- 취급액 변화만을 기준으로 지역을 선정하게 되면 코로나 이전의 매출에 대한 반영이 되지 못해 정책이 효과적으로 빠르게 효과를 볼 수 있을지 의문이 들었다.
- 코로나 이전의 취급액은 너무 낮지는 않은 지역을 선정하고자 다시 데이터를 분석하였다.

![image](https://user-images.githubusercontent.com/83098550/143678597-b9e299a9-87cc-4bd6-9ad6-5213e511a02d.png)

이상치 보정
![image](https://user-images.githubusercontent.com/83098550/143678616-0a0d8eb1-41b8-4c47-8d05-29f08d2172b4.png)

* 취급액과 이용건수 자료를 파악해보니 이상치가 많이 존재함을 알 수 있었다. 
* 단순 변화로 점수를 매기게 되면 문제가 발생할 수 있을 것 같아 로그를 씌워 첨도를 보정하였다.

문제점
![image](https://user-images.githubusercontent.com/83098550/143678710-efb53a4c-879d-4be2-ad20-f0a4c298d987.png)
* 취급액과 이용건수에서 이상치가 존재해 점수가 너무 차이가 나게 되어버리는 문제가 발생한다.
* RobustScaler, MinMaxScaler

이상치 보정
![image](https://user-images.githubusercontent.com/83098550/143678775-4348c754-2444-4869-944b-2daf4a8e0330.png)

## 문화시설이 많은 지역 선정
- Tour api에서 제공하는 자연, 레포츠 ,인문 역사에 대한 문화시설 크롤링 후 데이터로 사용
``` PYTHON
  URL = ‘https://api.visitkorea.or.kr/search/commonList.do’
  관광지명 = []
  지역 = []
  cnt = 0
```

홈페이지 열기
``` PYTHON
  driver = webdriver.chrome(executable_path=' ./chromedriver')
  driver.get(url=uRL)
  driver.implicitly_wait(time_to_wait=3)
```  
  
쇼핑, 음식점 카테고리 제거하기
``` PYTHON  
  driver.find_element_by_xpath('//*[@id="typeid_38"]').click()
  time.sleep(1)
  driver.find_element_by_xpath('//*[@id="typeid_39"]').click()
  driver.implicitly_wait(time_to_wait=3)
  time.sleep(2)
```  
  
자연 먼저 확인하기
``` PYTHON 
  driver.find_element_by_xpath(‘//*(@id="selectDiv"]/table/tbody/tr[2]/td/div[1]/span(1)/select(1)/option[2]').click()
  driver.implicitly_wait(time_to_wait=3)
  time. sleep(2)
```  
  
메뉴 50개씩 보기

``` PYTHON
  driver.find_element_by_xpath('//*[@id="numofPage"]/option[5]*).click()
  driver.implicitly_wait(time_to_wait=1)
  driver.find_element_by_xpath(‘'//*[@id="numOfPageview"]*).click()
  driver.implicitly_wait(time_to_wait=5)
  
  while True:
    # 페이지 넘어가기 알고리즘
    try:
      if cnt >= 1:
        driver.find_element_by_xpath('//*[@id="content"]/div(6é)/span/a[{}]'.format(cnt)).click()
        driver.implicitly_wait(time_to_wait=3)
        time.sleep(2)
      cnt += 1
    except:
      break
    if cnt == 10:
    driver.find_element_by_xpath('//*[@id="content"]/div[6é]/a[3]').click()
    driver.implicitly_wait (time_to_wait=3)
    time.sleep(2)
    cnt = 0
```

해당 페이지 소스 받아오기

``` PYTHON
  html = driver.page_source
  soup = BeautifulSoup(html, ‘html.parser')
  attraction = soup.select('#listForm > ul > li > a > dl > dt')
  location = soup.select('#listForm > ul > li > a > dl > dd > strong’)
```  
  
1. 비대면

![image](https://user-images.githubusercontent.com/83098550/143993737-41c93755-4a98-4082-9b9d-a5809eb5a73c.png)

2. 언텍트

![image](https://user-images.githubusercontent.com/83098550/143993756-36d3c73a-17ff-460d-aa0f-293f6c7b3496.png)

# 정책 제안 및 결론
## 최종 지역 선정
![image](https://user-images.githubusercontent.com/83098550/143996722-7fb577e7-0a2f-4a90-a5ef-d633d0c71311.png)
![image](https://user-images.githubusercontent.com/83098550/143996823-4ef00381-52cd-462c-8ffd-6b5cf4885189.png)


