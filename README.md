# AWS Config RDK Samples

아래는 AWS 블로그 “AWS Config와 Systems Manager를 활용하여 인터넷에 연결된 로드 밸런서 생성 탐지 및 제거하기” 내용을 참고하여 작성된 Custom Config Rule 코드입니다.
https://aws.amazon.com/ko/blogs/tech/internet-facing-load-balancer-detection/

**프로덕션에 적용하기 전에 각 규칙을 주의 깊게 검토하고 개발/테스트 환경에서 테스트해 주세요.**

## 코드 배포하기
RDK 도구를 활용하여 간단한 명령어로 배포할 수 있습니다. 또는 사용자가 직접 수동 Deploy 할 수 있습니다.

### RDK 설치 및 준비
RDK를 설치합니다.

```bash
pip install rdk
export AWS_DEFAULT_REGION=ap-northeast-2
rdk init
```

### RDK로 Deploy
현재 디렉토리에서, `alb-restrict-single-az` rule을 deploy하는 예시입니다.
```
rdk deploy alb-restrict-single-az
```

### 수동 Deploy
이 리포지토리의 샘플 함수를 사용하여 AWS 리소스의 구성 설정을 평가하는 Config 규칙을 만들 수 있습니다. 먼저 AWS Lambda를 사용하여 샘플 코드를 기반으로 하는 함수를 생성합니다. 그런 다음 AWS Config를 사용하여 함수와 연결된 규칙을 생성합니다. 규칙의 트리거가 발생하면 AWS Config는 함수를 호출하여 AWS 리소스를 평가합니다.

다음 단계를 완료하여 AWS Config에 규칙을 추가하십시오. 자세한 단계는 *AWS 구성 개발자 안내서*의 [사용자 지정 Lambda 규칙 (일반 예제)](http://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_develop-rules_nodejs.html) 을 참조하십시오.

1. AWS Lambda 콘솔로 이동합니다.
	- AWS 관리 콘솔에 로그인하여 [AWS Lambda 콘솔](https://console.aws.amazon.com/lambda/) 을 엽니다.
	- 해당 지역이 AWS Config 규칙을 지원하는 지역으로 설정되어 있는지 확인하십시오.
	- 지원되는 지역 목록은 [AWS 구성 리전 및 엔드포인트](http://docs.aws.amazon.com/general/latest/gr/rande.html#awsconfig_region) 를 참조하십시오.
2. Lambda 함수를 생성합니다.
	- 선택한 코드 입력 유형에 필요한 방법을 사용하여 코드를 입력합니다. 
	- Python 또는 Node.js 함수를 추가하는 경우 사용하려는 샘플의 코드를 복사하여 붙여넣을 수 있습니다.Java 함수를 추가하는 경우 Java 클래스가 포함된 JAR 파일을 제공해야 합니다.JAR 파일 구축 지침은 [Java를 사용한 AWS 구성 규칙 생성] (을) 을 참조하십시오./자바/하우투.md).
	- 함수에 할당하는 역할에 대해**AWS Config Rules 권한** 옵션을 선택합니다.여기에는 Lambda 함수에 평가를 “입력”할 수 있는 권한을 허용하는 AWS 관리형 정책인 *AWSConfigRulesExecutionRole*이 포함됩니다.
	- **Handler**의 경우 파이썬 또는 Node.js 함수를 추가하는 경우 기본값을 유지하십시오.Java 함수를 추가하는 경우 사용하려는 Java 함수에 대한 핸들러 값을 지정하십시오.핸들러 값은 [AWS 구성 규칙 (Java)] (을) 참조하십시오./자바/규칙_자바.md).
3. 함수를 생성한 후에는 함수의 ARN을 기록해 두십시오. 
4. [AWS 구성 콘솔](https://console.aws.amazon.com/config/) 을 엽니다. 
    - 사용자 지정 규칙에 대한 AWS Lambda 함수를 생성한 리전과 동일한 리전으로 리전이 설정되어 있는지 확인하십시오. 
5. AWS Config 콘솔을 사용하여 사용자 지정 규칙을 추가합니다. 
	- **AWS Lambda 함수 ARN**의 경우 생성한 함수의 ARN을 지정합니다.
	- **트리거 유형**의 경우 이 저장소의 *트리거 샘플*을 사용하는 경우 **구성 변경**을 선택하십시오.이 저장소의 *주기적* 샘플을 사용하는 경우 **주기적**을 선택하십시오.
	- 규칙 매개 변수의 경우 필요한 매개 변수를 지정합니다.
	- 각 함수의 트리거 유형 및 필수 파라미터는 [AWS Config Rules] (을) 참조하십시오./Rules.md) (파이썬 및 Node.js 함수용) 또는 [AWS 구성 규칙 (자바)] (./자바/규칙_자바.md).
    - **참고**: AWS Config 콘솔로 사용자 지정 규칙을 생성하면 Lambda를 호출하기 위한 적절한 권한이 자동으로 생성됩니다.AWS CLI로 사용자 지정 규칙을 생성하는 경우, `aws lambda add-permission` 명령을 사용하여 Lambda 함수를 호출할 수 있는 권한을 AWS Config에 부여해야 합니다.

규칙을 생성하면 해당 규칙이 **규칙** 페이지에 표시되고 AWS Config는 해당 Lambda 함수를 호출합니다.몇 분 후에 평가 결과 요약이 표시됩니다.