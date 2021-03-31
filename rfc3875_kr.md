# INFORMATIONAL
### Network Working Group
D. Robinson
### Request for Comments: 3875
K. Coar
### Category: Informational
The Apache Software Foundation
### October 2004



# The Common Gateway Interface (CGI) Version 1.1

### Status of this Memo

이 메모는 인터넷 커뮤니티를 위한 정보를 제공합니다. 이 메모는 어떠한 종류의 인터넷 표준도 정의하지 않습니다. 이 메모의 배포에는 제한이 없습니다.

### 저작권 알림

Copyright (C) The Internet Society (2004).

### IESG Note

이 문서는 어떤 수준의 인터넷 표준에도 해당되지 않습니다. IETF는 어떤 목적으로든 본 문서의 적합성에 대한 지식을 부인하며, 특히 보안, 정체 제어 또는 배치된 프로토콜과의 부적절한 상호 작용과 같은 것에 대해 IETF 검토를 하지 않았다는 점에 주목합니다. RFC 편집자가 재량에 따라 이 문서를 등록하도록 선택했습니다. 이 문서의 독자는 구현 및 배치에 대한 가치를 평가할 때 주의를 기울여야 합니다.

### 요약

CGI(Common Gateway Interface)는 플랫폼 독립적인 방식으로 정보 서버 아래에서 외부 프로그램, 소프트웨어 또는 게이트웨이를 실행하기 위한 간단한 인터페이스입니다. 현재 지원되는 정보 서버는 HTTP 서버입니다.

인터페이스는 1993년부터 WWW(World-Wide Web)에서 사용되고 있습니다. 이 규격은 미국 국립 슈퍼컴퓨팅 응용 프로그램 센터에서 개발 및 문서화된 'CGI/1.1' 인터페이스의 '현재 관행' 매개변수를 정의합니다. 이 문서는 또한 UNIX(R) 및 기타 유사한 시스템에서 CGI/1.1 인터페이스 사용을 정의합니다.

# 목차

```
1.  Introduction
    1.1. Purpose
    1.2. Requirements
    1.3. Specifications
    1.4. Terminology

2.  Notational Conventions and Generic Grammar
    2.1. Augmented BNF
    2.2. Basic Rules
    2.3. URL Encoding

3.  Invoking the Script
    3.1. Server Responsibilities
    3.2. Script Selection
    3.3. The Script-URI
    3.4. Execution

4.  The CGI Request
    4.1. Request Meta-Variables
        4.1.1.  AUTH_TYPE
        4.1.2.  CONTENT_LENGTH
        4.1.3.  CONTENT_TYPE
        4.1.4.  GATEWAY_INTERFACE
        4.1.5.  PATH_INFO
        4.1.6.  PATH_TRANSLATED
        4.1.7.  QUERY_STRING
        4.1.8.  REMOTE_ADDR
        4.1.9.  REMOTE_HOST
        4.1.10. REMOTE_IDENT 
        4.1.11. REMOTE_USER
        4.1.12. REQUEST_METHOD 
        4.1.13. SCRIPT_NAME
        4.1.14. SERVER_NAME
        4.1.15. SERVER_PORT
        4.1.16. SERVER_PROTOCOL
        4.1.17. SERVER_SOFTWARE
        4.1.18. Protocol-Specific Meta-Variables
    4.2. Request Message-Body 
    4.3. Request Methods  
        4.3.1.  GET
        4.3.2.  POST 
        4.3.3.  HEAD 
        4.3.4.  Protocol-Specific Methods
    4.4. The Script Command Line

5.  NPH Scripts 
    5.1. Identification 
    5.2. NPH Response 

6.  CGI Response
    6.1. Response Handling
    6.2. Response Types 
        6.2.1.  Document Response
        6.2.2.  Local Redirect Response
        6.2.3.  Client Redirect Response 
        6.2.4.  Client Redirect Response with Document
    6.3. Response Header Fields 
        6.3.1.  Content-Type 
        6.3.2.  Location 
        6.3.3.  Status 
        6.3.4.  Protocol-Specific Header Fields
        6.3.5.  Extension Header Fields
    6.4. Response Message-Body

7.  System Specifications 
    7.1. AmigaDOS 
    7.2. UNIX 
    7.3. EBCDIC/POSIX 

8.  Implementation
    8.1. Recommendations for Servers
    8.2. Recommendations for Scripts

9.  Security Considerations 
    9.1. Safe Methods 
    9.2. Header Fields Containing Sensitive Information
    9.3. Data Privacy 
    9.4. Information Security Model 
    9.5. Script Interference with the Server
    9.6. Data Length and Buffering Considerations
    9.7. Stateless Processing 
    9.8. Relative Paths 
    9.9. Non-parsed Header Output 

10. Acknowledgements

11. References
    11.1. Normative References
    11.2. Informative References

12. Authors' Addresses

13. Full Copyright Statement
```

# 1.  소개

## 1.1.  목적

CGI(Common Gateway Interface)[22]를 사용하면 HTTP[1],[4] 서버 및 CGI 스크립트가 클라이언트 요청에 대한 응답에 대한 책임을 공유할 수 있습니다. 클라이언트 요청은 URI(Uniform Resource Identifier)[11], 요청 방법 및 전송 프로토콜에 의해 제공된 요청에 대한 다양한 보조 정보로 구성됩니다.

CGI는 클라이언트의 요청을 설명하는 메타변수로 알려진 추상 매개 변수를 정의합니다. 특정 프로그래머 인터페이스와 함께 스크립트와 HTTP 서버 사이의 플랫폼 독립 인터페이스를 지정합니다.

서버는 클라이언트 요청과 관련된 연결, 데이터 전송, 전송 및 네트워크 문제를 관리하는 반면 CGI 스크립트는 데이터 액세스 및 문서 처리와 같은 애플리케이션 문제를 처리합니다.

## 1.2.  요구사항

이 문서의 '반드시 해야하는(MUST)', '절대로 해서는 안 되는(MUST NOT)', '요구되는(REQUIRED)', '해야하는(SHALL)', '해서는 안 되는(SHALL NOT)', '해야 하는(SHOULD)', '해서는 안 되는(SHOULD NOT)', '할 수도 있는(MAY)', '선택적인(OPTIONAL)' 키워드는 BCP 14, RFC 2119의 묘사대로 해석됩니다.

구현하는 프로토콜에 대해 하나 또는 그 이상의 '반드시 해야하는 /절대로해서는 안 되는' 요건을 충족하지 못하면 구현은 따르지 않습니다. 기능에 대한 모든 '반드시 해야하는'과 '해야하는' 요건을 충족하는 구현은 '무조건 준수'라고 하며, 기능에 대한 '반드시 해야하는'은 모두 충족하지만 '해야하는' 요건을 모두 충족하지는 않는 구현은 '조건 준수'라고 합니다.

> 역주: RFC 2119에 따르면 MUST, SHALL, REQUIRED는 강제성이 있습니다. 이것들은 부사 '반드시/절대로'를 붙였습니다. SHOULD, RECOMMENDED는 지켜야 하는 것들이지만 타당한 이유가 있을 경우 무시할 수 있으며 '해야 한다/해서는 안 된다'로 번역했습니다. MAY는 "~할 수도 있습니다" 또는 "~일 수도 있습니다"로 번역했습니다. 이 문서에 REQUIRED, SHALL, OPTIONAL은 나오지 않았습니다.


## 1.3.  규격

CGI의 모든 기능과 특징이 이 규격의 주요 부분에 정의되어 있는 것은 아닙니다. 지정되지 않은 기능을 설명하는 데 다음 구문이 사용됩니다:

### '시스템정의' 

이 기능은 시스템마다 다를 수 있지만 동일한 시스템을 사용하는 다른 구현의 경우 반드시 동일해야 합니다. 시스템은 일반적으로 운영 체제의 클래스를 식별합니다. 일부 시스템은 이 문서의 섹션 7에 정의되어 있습니다. 새로운 시스템은 이 문서를 수정하지 않고 새로운 규격에 의해 정의될 수 있습니다.

### '구현정의'

기능의 동작은 구현마다 다를 수 있습니다. 특정 구현은 반드시 동작을 문서화해야 합니다.

## 1.4.  용어

이 규격은 HTTP/1.1 규격에 정의된 많은 용어를 씁니다 [4]. 그러나 여기에 쓰인 다음의 용어들은 HTTP/1.1의 용어의 정의나 일반적인 의미와 호환되지 않을 수도 있습니다.

### '메타변수'

서버에서 스크립트로 정보를 전달하는 명명된 매개변수입니다. 꼭 운영체제의 환경변수인 것은 아니지만 가장 일반적인 구현이기는 합니다.

### '스크립트'

서버가 이 인터페이스에 따라 호출하는 소프트웨어입니다. 독립 실행형 프로그램일 필요는 없지만 동적으로 로드되거나 공유된 라이브러리일 수도 있고 서버의 하위 루틴일 수도 있습니다. 이는 '스크립트'라는 용어가 자주 이해되기 때문에 런타임에 해석되는 일련의 문장일 수 있지만, 이는 요구 사항이 아니며 이 규격의 맥락에서 용어에는 더 넓은 정의가 명시되어 있습니다.

### '서버'

클라이언트의 요청을 처리하기 위해 스크립트를 호출하는 응용프로그램입니다.



# 2.  Notational Conventions and Generic Grammar

## 2.1.  Augmented BNF

이 문서에 실린 모든 메커니즘은 RFC 822에서 사용된 것과 비슷한 방식의 산문체와 argumented 배커스 나우어(BNF)방식으로 설명되어 있습니다[13]. 따로 명시되어있지 않으면 요소는 대소문자를 구분합니다. argumented BNF는 다음과 같은 구조를 포함합니다.

### 이름 = 정의

규칙의 이름과 정의는 등호 문자('=')로 구분됩니다. 공백은 연속된 줄로 들여쓰기 되어있는 정의에만 의미가 있습니다.

## "리터럴"

큰따옴표로 둘러싼 문자열이되, 꺾쇠(<와 >)로 둘러싼 것을 제외합니다.

### 규칙1 | 규칙2

대체 가능한 규칙은 수직막대('|')로 나뉩니다.

### (규칙1 규칙2 규칙3)

괄호에 갇힌 요소들은 하나의 요소로 취급됩니다.

### *규칙

별표(```'*'```)가 앞에 오는 규칙은 0회 또는 그 이상 나타날 수 있습니다. 완전한 형식은 ```n*m 규칙```이고 규칙의 최소 n회에서 최대 m회 나타나는 것을 표시합니다. n과 m은 각각 기본값이 0과 무한대인 선택적인 십진수 값입니다.

### [규칙]

대괄호('['과 ']')에 갇힌 요소는 선택적이며 '*1 규칙'과 같습니다.

### N 규칙

규칙 앞에 오는 십진수는 정확히 규칙에 N 만큼의 occurrence가 있음을 뜻합니다. 'N*N 규칙'과 같습니다.

## 2.2.  기본 규칙

이 규격은 문자 단위로 정의된 BNF 유사 문법을 사용합니다. 프로토콜이 허용하는 바이트를 정의하는 많은 규격과 달리, 여기 문법의 각 리터럴은 그것이 나타내는 문자와 일치합니다. 이러한 문자가 시스템 내에서 비트 및 바이트 단위로 표시되는 방법은 시스템정의 또는 특정 컨텍스트에서 지정됩니다. 단일 예외는 아래에 정의된 규칙 '옥텟'입니다.

다음 규칙은 이 규격에서 기본 구문 분석 방법을 설명하는 데 사용됩니다.

```
alpha         = lowalpha | hialpha
lowalpha      = "a" | "b" | "c" | "d" | "e" | "f" | "g" | "h" |
                "i" | "j" | "k" | "l" | "m" | "n" | "o" | "p" |
                "q" | "r" | "s" | "t" | "u" | "v" | "w" | "x" |
                "y" | "z"
hialpha       = "A" | "B" | "C" | "D" | "E" | "F" | "G" | "H" |
                "I" | "J" | "K" | "L" | "M" | "N" | "O" | "P" |
                "Q" | "R" | "S" | "T" | "U" | "V" | "W" | "X" |
                "Y" | "Z"
digit         = "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" |
                "8" | "9"
alphanum      = alpha | digit
OCTET         = <any 8-bit byte>
CHAR          = alpha | digit | separator | "!" | "#" | "$" |
                "%" | "&" | "'" | "*" | "+" | "-" | "." | "`" |
                "^" | "_" | "{" | "|" | "}" | "~" | CTL
CTL           = <any control character>
SP            = <space character>
HT            = <horizontal tab character>
NL            = <newline>
LWSP          = SP | HT | NL
separator     = "(" | ")" | "<" | ">" | "@" | "," | ";" | ":" |
                "\" | <"> | "/" | "[" | "]" | "?" | "=" | "{" |
                "}" | SP | HT
token         = 1*<any CHAR except CTLs or separators>
quoted-string = <"> *qdtext <">
qdtext        = <any CHAR except <"> and CTLs but including LWSP>
TEXT          = <any printable character>
```

개행은 단일 제어 문자가 아니라 연속된 문자일 수 있습니다. 어떤 시스템은 텍스트를 <'CTL'을 제외하고 'LWSP'를 포함하는 CHAR 집합>보다 큰 문자의 집합으로 정의할 수도 있습니다.

## 2.3.  URL 인코딩

여기서 사용되는 일부 변수와 구성은 'URL 인코딩'으로 설명됩니다. 이 인코딩은 RFC 2396의 섹션 2에 설명되어 있습니다[2]. URL 인코딩 문자열에서 이스케이프 시퀀스는 백분율 문자("%")와 두 개의 16진수 자릿수로 구성됩니다. 여기서 두 개의 16진수 자릿수는 옥텟을 형성합니다. 이스케이프 시퀀스는 미국 ASCII[9] 코드 문자 집합(있는 경우) 내에 옥텟이 코드로 있는 그래픽 문자를 나타냅니다. 현재 URI 구문 내에 ASCII가 아닌 문자 집합을 식별할 수 있는 조항이 없습니다. ASCII 코드는 이 문제를 나타내므로 CGI는 임시적으로 이 문제를 처리합니다.

일부 안전하지 않은(예약된) 문자는 인코딩 시 다른 의미를 가질 수 있습니다. 안전하지 않은 문자의 정의는 상황에 따라 다릅니다. 권한 있는 처리는 RFC 2732[7]에 의해 업데이트된 RFC 2396[2]의 섹션 2를 참조하십시오. 이러한 예약된 문자는 일반적으로 필드 구분 기호와 같이 문자 문자열에 구문 구조를 제공하는 데 사용됩니다. 모든 경우 문자열은 먼저 존재하는 예약된 문자와 관련하여 처리되고, 그 다음 %" 이스케이프 시퀀스를 문자 값으로 대체하여 URL 디코딩이 가능합니다.

문자 문자열을 인코딩하려면 모든 예약된 문자 및 금지된 문자가 해당하는 "%" 이스케이프 시퀀스로 대체됩니다. 그런 다음 URI를 조립하는 데 문자열을 사용할 수 있습니다. 예약된 문자는 상황에 따라 다르지만 항상 다음 집합에서 그립니다.

```
reserved = ";" | "/" | "?" | ":" | "@" | "&" | "=" | "+" | "$" |
           "," | "[" | "]"
```

RFC 2732 [7]에 의해 마지막 두 문자가 추가되었습니다. 특정 컨텍스트에서 이러한 문자의 하위 집합은 예약됩니다. 문자열이 해당 컨텍스트에서 URL로 인코딩될 때 이 세트의 다른 문자를 절대로 인코딩해서는 안 됩니다. URI 구문을 설명하는 데 사용되는 다른 기본 규칙은 다음과 같습니다.

```
hex        = digit | "A" | "B" | "C" | "D" | "E" | "F" | "a" | "b"
             | "c" | "d" | "e" | "f"
escaped    = "%" hex hex
unreserved = alpha | digit | mark
mark       = "-" | "_" | "." | "!" | "~" | "*" | "'" | "(" | ")"
```

# 3.  스크립트 호출하기

## 3.1.  서버의 책임

서버는 응용프로그램 게이트웨이 역할을 합니다. 클라이언트로부터 요청을 받고, 요청을 처리할 CGI 스크립트를 선택하고, 클라이언트 요청을 CGI 요청으로 변환하고, 스크립트를 실행하고 CGI 응답을 클라이언트의 응답으로 변환합니다. 클라이언트 요청을 처리할 때 프로토콜 또는 전송 수준 인증 및 보안을 구현할 책임이 있습니다. 또한 서버는 미디어 유형 변환 또는 프로토콜 감소와 같은 추가 서비스를 제공하기 위해 요청 또는 응답을 수정하여 '불투명' 방식으로 작동할 수 있습니다.

서버는 이 규격에 필요한 클라이언트 요청 데이터에 대해 변환 및 프로토콜 변환을 반드시 수행해야 합니다. 또한 CGI 스크립트가 이 규격을 준수하지 않더라도 서버는 관련 네트워크 프로토콜을 준수할 수 있는 클라이언트에 대한 책임을 유지합니다.

서버가 요청에 인증을 적용하는 경우 정의된 액세스 제어를 모두 통과하지 않는 한 절대로 스크립트를 실행해서는 안 됩니다.

## 3.2.  스크립트 선택

서버는 클라이언트가 제공하는 일반 양식 URI를 기준으로 실행할 CGI를 결정합니다. 이 URI에는 구성 요소가 "/"로 구분된 계층 경로가 포함됩니다. 특정 요청에 대해, 서버는 이 경로의 전체나 앞 부분을 개별 스크립트로 식별하고, 경로 계층의 특정한 지점에 스크립트를 배치합니다. 경로의 나머지 부분은, 있다면, 스크립트로 해석될 리소스나 하위 리소스 식별자입니다.

경로의 이 분할에 대한 정보는 아래 설명된 메타변수의 스크립트에서 사용할 수 있습니다. 비계층적 URI 스킴에 대한 지원은 이 규격의 범위를 벗어납니다.

## 3.3.  스크립트-URI

클라이언트 요청 URI에서 스크립트를 선택하는 매핑(mapping)은 서버별로 구현과 구성에 따라 정의됩니다. 서버는 스크립트가 서로 다른 URI 경로계층 몇 개의 집합으로 인식되도록 할 수도 있으므로, 메타 변수를 처리하고 생성하는동안 URI를 다른 멤버와 대체하는 것이 허용됩니다. 서버는

1. 특정 클라이언트의 요청에서 URI를 유지할 수도 있습니다. 또는
2. 각 스크립트에 대해 가능한 값의 집합에서 표준 URI를 선택할 수도 있습니다. 또는 
3. 그 집합에서 다른 URI 선택을 구현할 수도 있습니다.

이렇게 생성된 메타변수에서 URI, 즉 'Script-URI'를 구성할 수 있습니다. 클라이언트가 이 URI로 대신해서 액세스했다면, 스크립트가 SCRIPT_NAME, PATH_INFO 및 QUERY_STRING 메타변수에 대해 동일한 값으로 실행되었을 것이라는 속성을 반드시 가져야 합니다. 스크립트-URI는 RFC 2396[2]의 섹션 3에 정의된 일반 URI 구조를 가지며, 객체 매개변수(object parameter)와 조각 식별자(fragment identifier)는 허용되지 않습니다. Script-URI의 다양한 구성 요소는 일부 메타변수에 의해 정의됩니다(아래 참조).

```
script-URI = <scheme> "://" <server-name> ":" <server-port>
             <script-path> <extra-path> "?" <query-string>
```

```<scheme>```은 SERVER_PROTOCOL에서 발견되고, ```<server-name>```, ```<server-port>```와 ```<query-string>```은 각 메타변수의 값입니다. SCRIPT_NAME과 PATH_INFO 값(';', '=', '?' 예약어를 포함하면서 인코딩 된 URL)은 ```<script-path>```와 ```<extra-path>```를 제공합니다.

PATH_INFO 메타변수에 대한 자세한 내용은 섹션 4.1.5를 참조하십시오.

스킴과 프로토콜은 응용프로그램 프로토콜 외에도 액세스 방법을 식별하기 때문에 동일하지 않습니다. 예를들어 전송계층보안(Transport Layer Security, TLS)[14]을 사용해서 엑세스 하는 리소스는 HTTP프로토콜을 사용하면서 https 스킴이 포함된 요청URI를 가질 수 있습니다[19]. CGI/1.1은 스크립트를 재구성하기 위한 일반적인 수단을 제공하지 않으므로, 정의된 스크립트-URI는 사용된 기본 프로토콜을 포함합니다. 하지만 스크립트는 스킴 별 메타변수를 사용하여 URI 스킴을 더 잘 추론할 수도 있습니다.

또한 이 정의는 URI에서 적절한 구성요소를 수정함으로서, URI가 스크립트를 경로정보(path-info)나 질의문자열(query-string)이 될 수 있는 어떠한 값으로 호출하도록 구성되는 것을 허용한다는 것을 알아두세요.

## 3.4.  실행

스크립트는 시스템정의 방식으로 호출됩니다. 달리 지정하지 않으면 스크립트가 포함된 파일이 실행 가능한 프로그램으로 호출됩니다. 서버는 섹션 4에 설명된 대로 CGI 요청을 준비합니다. 이 요청은 요청 메타변수(request meta-variable)(실행 시 스크립트에서 즉시 사용 가능)와 요청 메시지 데이터(request message data)로 구성됩니다. 요청 데이터가 스크립트에서 즉시 사용될 수 있어야만 하는 것은 아니며, 클라이언트에서 서버로 이 모든 데이터가 수신되기 전에 스크립트가 실행될 수 있습니다. 섹션 5 및 6에 설명된 대로 스크립트의 응답이 서버로 반환됩니다.

오류 상태가 발생할 경우 서버는 경고 없이 언제든지 스크립트 실행을 중단하거나 종료할 수 있습니다. 예를 들어 서버와 클라이언트 간에 전송 오류가 발생할 경우에 이러한 문제가 발생할 수 있으므로, 스크립트가 비정상적인 종료를 처리할 수 있도록 준비해야 합니다.

# 4.  CGI 요청

요청에 대한 정보는 두 소스에서 가져오는데, 하나는 요청 메타변수(request meta-variable)이고 하나는 관련된 메시지 본문(associated message-body)입니다.

## 4.1.  요청 메타변수(request meta-variable)

메타변수는 서버에서 스크립트로 전달된 요청에 관한 데이터를 담고 있고, 시스템정의 방식에 따라 스크립트가 접근합니다. 메타변수는 대/소문자를 구분하지 않는 이름으로 식별되며, 대/소문자를 구분하는 두 개의 변수가 있을 수 없습니다. 여기에서는 대문자와 밑줄("_") 정규표현을 사용하여 나타납니다. 특정 시스템은 다른 표현을 정의할 수 있습니다.

```
meta-variable-name = "AUTH_TYPE" | "CONTENT_LENGTH" |
                     "CONTENT_TYPE" | "GATEWAY_INTERFACE" |
                     "PATH_INFO" | "PATH_TRANSLATED" |
                     "QUERY_STRING" | "REMOTE_ADDR" |
                     "REMOTE_HOST" | "REMOTE_IDENT" |
                     "REMOTE_USER" | "REQUEST_METHOD" |
                     "SCRIPT_NAME" | "SERVER_NAME" |
                     "SERVER_PORT" | "SERVER_PROTOCOL" |
                     "SERVER_SOFTWARE" | scheme |
                     protocol-var-name | extension-var-name
protocol-var-name  = ( protocol | scheme ) "_" var-name
scheme             = alpha *( alpha | digit | "+" | "-" | "." )
var-name           = token
extension-var-name = token
```

프로토콜이나 스킴 이름으로 시작거나, 스킴 이름과 같은 메타변수(예: HTTP_ACCEPT)도 정의됩니다. 이러한 변수의 수와 의미는 이 규격으로부터 독립적으로 변경될 수 있습니다. (섹션 4.1.18도 참조.)

서버는 추가 구현정의 확장 메타변수를 설정할 수도 있고, 그 변수명에는 접두어 "X_"를 붙여야 합니다.

이 규격은 길이가 0(NULL)인 값과 찾을 수 없는(missing) 값을 구분하지 않습니다. 예를 들어 스크립트는 두 요청 "http://host/script"와 "http://host/script?"를 구분할 수 없어서 두 경우 모두 QUARY_STRING 메타변수가 NULL이 됩니다.

```
meta-variable-value = "" | 1*<TEXT, CHAR or tokens of value>
```

어떤 선택적인 메타변수는 값이 NULL이면 생략될 수 있습니다(설정되지 않은 상태로). 메타변수 값은 달리 명시되지 않은 경우를 제외하고 반드시 대소문자를 구분하는 것으로 간주해야 합니다. 메타변수의 문자 표현은 시스템정의이므로 서버는 값을 해당 표현으로 반드시 변환해야 합니다.

### 4.1.1.  AUTH_TYPE

AUTH_TYPE 변수는 서버를 통해 사용자를 인증하는 데 사용되는 메커니즘을 식별합니다. 클라이언트 프로토콜 또는 서버 구현에 의해 정의된 대/소문자를 구분하지 않는 값을 포함합니다.

HTTP의 경우, 클라이언트가 외부로부터의 접근에 필요한 인증을 요청한다면, 서버는 이 변수의 값을 반드시 Authorization 헤더 필드 요청의 'auth-scheme' 토큰에서 설정해야 합니다.

```
AUTH_TYPE      = "" | auth-scheme
auth-scheme    = "Basic" | "Digest" | extension-auth
extension-auth = token
```

HTTP 액세스 인증 체계는 RFC 2617 [5]에 설명되어 있습니다.

### 4.1.2.  CONTENT_LENGTH

CONTENT_LENGTH 변수는 요청에 첨부된 메시지 본문의 크기(있는 경우)를 십진수로 포함합니다. 데이터가 연결되어 있지 않으면 NULL(또는 설정되지 않음)을 선택합니다.

```
CONTENT_LENGTH = "" | 1*digit
```

반드시 요청이 메시지 본문 엔터티와 함께 있는 경우에만 서버가 이 메타변수를 설정해야 합니다. CONTENT_LENGTH 값은 반드시 서버가 transfer-codings 또는 content-codings를 제거한 후에 메시지 본문의 길이에 반영해야 합니다.

### 4.1.3.  CONTENT_TYPE

요청에 메시지 본문이 포함된 경우 CONTENT_TYPE 변수는 메시지 본문의 인터넷 미디어 유형 [6]으로 설정됩니다.

```
CONTENT_TYPE = "" | media-type
media-type   = type "/" subtype *( ";" parameter )
type         = token
subtype      = token
parameter    = attribute "=" value
attribute    = token
value        = token | quoted-string
```

유형, 하위 유형 및 매개변수 속성 이름은 대소문자를 구분하지 않습니다. 매개변수 값은 대소문자를 구분할수도 있습니다. HTTP에서 미디어의 유형 및 용도는 HTTP/1.1 규격 [4]의 섹션 3.7에 설명되어 있습니다.

이 변수에 대한 기본값은 없습니다. 이 변수 값이 설정되지 않은 경우에만 스크립트가 수신된 데이터에서 미디어 유형을 검사할 수 있습니다. 유형을 알 수 없는채로 남아있다면, 스클비트는 응용프로그램/옥텟스트림의 유형을 가정해서 고를 수도 있고, 요청을 오류로서 거부할 수도 있습니다.(섹션 6.3.3 에 설명된 대로).

각 미디어 유형은 선택적 매개 변수와 필수 매개 변수 집합을 정의합니다. 여기에는 메시지 본문에 대해 코드화된 문자 집합을 정의하는, 대/소문자를 구분하지 않는 값이 포함된 문자 집합 매개변수가 포함될 수 있습니다. 문자 집합 매개변수가 생략된 경우, 기본값은 다음 규칙 중 가장 먼저 적용되는 규칙에 따라 파생되어야 합니다.

1. 일부 미디어 유형에는 시스템정의 기본 문자 집합이 있을 수 있습니다.
2. 텍스트 미디어 유형의 기본값은 ISO-8859-1 [4]입니다.
3. 미디어 유형 규격에 정의된 기본값입니다.
4. 기본값이 US-ASCII입니다.

HTTP Content-Type 필드가 클라이언트 요청 헤더에 있는 경우, 서버는 반드시 이 메타변수를 설정해야 합니다. 서버가 연결된 엔티티가 있는 요청을 수신하지만 내용 유형 헤더 필드가 없는 경우, 정확한 콘텐츠 유형을 확인하려고 할 수 있습니다. 그렇지 않으면 이 메타변수를 생략해야 합니다.

### 4.1.4.  GATEWAY_INTERFACE

GATEWAY_INTERFACE 변수는 반드시 서버가 스크립트와 통신하기 위해 사용되는 CGI의 방언으로 성정되어야 합니다. 문법:

```
GATEWAY_INTERFACE = "CGI" "/" 1*digit "." 1*digit
```

메이저와 마이너는 따로 다뤄지고, 따라서 단일 숫자보다 높을 수 있습니다. 따라서 CGI/2.4는 CGI/2.13보다 낮은 버전이고 CGI/12.3보다도 낮습니다. 선행하는 0은 스크립트에 의해 반드시 무시되어야 하고 절대로 서버에서 생성되지 않아야 합니다.

이 문서는 CGI/1.1 버전을 정의합니다.

### 4.1.5.  PATH_INFO

PATH_INFO 변수는 CGI 스크립트로 해석될 경로를 지정합니다. PATH_INFO 변수는 CGI 스트립트에 의해 반환될 리소스나 하위 리소스를 식별하며, 스크립트 자신을 식별하는 부분 다음에 오는 URI경로 계층 부분으로부터 유도됩니다. URI 경로와 달리, PATH_INFO는 URL로 인코딩된 것이 아니고, 경로 세그먼트 매개변수를 포함할 수 없습니다. "/"의 PATH_INFO는 단일한 void 경로 세그먼트를 나타냅니다.

```
PATH_INFO = "" | ( "/" path )
path      = lsegment *( "/" lsegment )
lsegment  = *lchar
lchar     = <any TEXT or CTL except "/">
```

이 값은 대/소문자를 구분하는 것으로 간주되며, 서버는 경로의 대/소문자를 반드시 요청 URI에 나타난대로 보존해야 합니다. 서버는 PATH_INFO에 대해 허용하는 값에 대해 제한과 제한을 가할 수도 있으며, 거부 가능한 값이 있는 경우 오류와 함께 요청을 거부할 수도 있습니다. 여기에는 "/"가 인코딩되어 PATH_INFO로 디코딩 될 요청이 포함될 수 있으며, 스크립트로 가는 정보의 손실이 나타날 수도 있습니다. 비슷하게, 경로에서 미국 ASCII가 아닌 문자에 대한 처리는 시스템정의입니다.

URL로 인코딩된 PATH_INFO 문자열은 extra-path의 SCRIPT_NAME 부분을 따르는 SCRIPT_URI(섹션 3.3 참조)의 구성요소를 형성합니다

### 4.1.6.  PATH_TRANSLATED

PATH_TRANSLATED 변수는 PATH_INFO 값을 가져와서 자체적인 로컬 URI로 구문 분석하고, 서버의 문서 리파지토리 구조로 매핑에 적합한 '가상에서 물리'로 번환을 수행합니다. 결과에서 허용되는 문자 집합은 시스템정의입니다.

```
PATH_TRANSLATED = *<any character>
```

요청에 의해 접근하게 될 파일 위치입니다.

```
<scheme> "://" <server-name> ":" <server-port> <extra-path>
```

<스킴>이 있는 곳은 클라이언트 요청 원본의 스킴이고 <extra-path>는 ";", "=", "?" 예약어로 URL로 인코딩된 버전의 PATH_INFO 입니다. 예를들어, 다음과 같은 요청은:

```
http://somehost.com/cgi-bin/somescript/this%2eis%2epath%3binfo
```

PATH_INFO 결과 값이

```
/this.is.the.path;info
```

내부 URI는 스킴, 서버위치, URL로 인코딩된 PATH_INFO로부터 구성됩니다.

```
http://somehost.com/this.is.the.path%3binfo
```

그러면 서버의 문서 리포지토리에 있는 위치(아마도 이와 같은 파일 시스템 경로)로 변환됩니다:

```
/usr/local/www/htdocs/this.is.the.path;info
```

PATH_TRANSLATED 값은 변환의 결과입니다.

이 값은 올바른 리포지토리 위치에 매핑되는지 여부에 관계없이 이러한 방식으로 파생됩니다. 서버는 기본 리포지토리가 대소문자를 구분하지 않는 이름을 지원하지 않는 한 추가 경로 세그먼트의 대/소문자를 반드시 유지해야 합니다. 리포지토리가 문서 이름에 대해 대/소문자를 구분하거나 대/소문자를 구분하지 않는 경우, 서버는 변환을 통해 원본 세그먼트의 대/소문자를 보존할 필요가 없습니다.

서버가 PATH_TRANSLATED를 도출하기 위해 사용하는 변환 알고리즘은 구현정의이며, 이 변수를 사용하는 CGI 스크립트는 제한된 이식성을 겪을 수 있습니다.

요청 URI에 경로 정보 구성 요소가 포함되어 있는 경우 서버는 이 메타변수를 설정해야 합니다. PATH_INFO가 NULL이면 PATH_TRANSLATED 변수를 반드시 NULL(또는 설정되지 않음)로 설정해야 합니다.

### 4.1.7.  QUERY_STRING

QUARY_STRING 변수에는 URL로 인코딩된 검색 또는 매개 변수 문자열이 포함되어 있습니다. 이 문자열은 CGI 스크립트로부터 반환될 문서에 영향을 미치거나 제련하기 위해 스크립트에 정보를 제공합니다.

검색 문자열의 URL 구문은 RFC 2396 [2]의 섹션 3에 설명되어 있습니다. QUARY_STRING 값은 대소문자를 구분합니다.

```
QUERY_STRING = query-string
query-string = *uric
uric         = reserved | unreserved | escaped
```

질의문자열을 파싱하고 디코딩할 때 파싱의 세부사항, 예약된 문자, 미국 ASCII가 아닌 문자에 대한 지원은 컨텍스트에 따라 달라집니다. 예를 들어, HTML 문서[18]의 양식 제출은 application/x-www-form-urlencoded 인코딩을 사용하는데, 여기서 문자 "+", "&", "="는 예약문자이고, ISO 8859-1 인코딩은 미국 ASCII가 아닌 문자에 사용될 수 있습니다.

QUARY_STRING 값은 Script-URI의 질의문자열 부분을 제공합니다(섹션 3.3 참조).

서버는 이 변수를 반드시 설정해야 합니다. 스크립트-URI에 쿼리 구성 요소가 포함되어 있지 않으면 QUARY_STRING을 반드시 빈 문자열("")로 정의해야 합니다.

### 4.1.8.  REMOTE_ADDR

REMOTE_ADDR 변수는 반드시 서버로 요청을 보내는 클라이언트의 네트워크 주소로 설정되어야 합니다.

```
REMOTE_ADDR  = hostnumber
hostnumber   = ipv4-address | ipv6-address
ipv4-address = 1*3digit "." 1*3digit "." 1*3digit "." 1*3digit
ipv6-address = hexpart [ ":" ipv4-address ]
hexpart      = hexseq | ( [ hexseq ] "::" [ hexseq ] )
hexseq       = 1*4hex *( ":" 1*4hex )
```

IPv6 형식 주소는 RFC 3513 [15]에 설명되어 있습니다.

### 4.1.9.  REMOTE_HOST

REMOTE_HOST 변수는, 서버로 요청을 보내는 클라이언트의 완전한 도메인 이름을 담는 것이 가능할 때는 그렇게 하고, 아닐 경우에는 NULL입니다. 완전한 도메인 이름은 RFC 1034의 섹션 3.5[17]와 RFC 1123의 섹션 2.1[12]에서 설명하는 형식을 취합니다. 도메인 이름은 대소문자를 구분하지 않습니다.

```
REMOTE_HOST   = "" | hostname | hostnumber
hostname      = *( domainlabel "." ) toplabel [ "." ]
domainlabel   = alphanum [ *alphahypdigit alphanum ]
toplabel      = alpha [ *alphahypdigit alphanum ]
alphahypdigit = alphanum | "-"
```

서버는 이 변수를 설정해야 합니다. 성능상의 이유나 기타 이유로 호스트 이름을 사용할 수 없는 등의 경우, 서버가 REMOTE_ADDR 값을 대체할 수도 있습니다.

### 4.1.10.  REMOTE_IDENT

REMOTE_IDENT 변수는 RFC 1413[20]에 따른 원격 대리자로 연결 요청에 대해 보고된 식별 정보를 제공하기 위해 쓰일 수도 있습니다. 서버는 이 기능을 지원하지 않거나, 효율성 때문에 데이터를 요청하지 않거나, 사용 가능한 ID 데이터를 반환하지 않는 것을 선택할 수 있습니다.

```
REMOTE_IDENT = *TEXT
```

반환된 데이터는 인증 목적으로 사용될 수 있지만, 신뢰 수준이 최소로 유지되어야 합니다.

### 4.1.11.  REMOTE_USER

REMOTE_USER 변수는 클라이언트로부터 사용자 인증의 일부로서 지원되는 사용자 식별 문자열을 제공합니다.

```
REMOTE_USER = *TEXT
```

클라이언트 요청이 HTTP Authentication [5]를 필요로 하는 경우(예: AUTH_TYPE 메타변수가 "Basic" 또는 "Digest"로 설정됨) REMOTE_USER 메타변수의 값은 반드시 제공된 사용자 ID로 설정해야 합니다.

### 4.1.12.  REQUEST_METHOD

RESSURE_METHOD 메타변수는 섹션 4.3에서 설명한 대로, 요청을 처리하기 위해 스크립트에서 사용해야 하는 메서드로 반드시 설정되어야 합니다.

```
REQUEST_METHOD   = method
method           = "GET" | "POST" | "HEAD" | extension-method
extension-method = "PUT" | "DELETE" | token
```

이 메서드는 대소문자를 구분합니다. HTTP 메서드는 HTTP/1.0 규격의 섹션 5.1.1[1]과 HTTP/1.1 규격의 섹션 5.1.1[4]에 설명되어 있습니다.

### 4.1.13.  SCRIPT_NAME

SCRIPT_NAME 변수는 반드시 (스크립트의 출력이 아니라) CGI 스크립트를 식별할 수 있는 URI경로(인코딩된 URL이 아님)로 설정되어야 합니다. 문법은 PATH_INFO와 동일합니다(섹션 4.1.5).

```
SCRIPT_NAME = "" | ( "/" path )
```

앞에 붙은 "/"는 경로의 일부가 아닙니다. 경로가 NULL이라면 선택사항입니다. 그러나 그런 경우에도 변수는 반드시 설정되어야 합니다.

SCRIPT_NAME 문자열은 구현정의 방식으로 파생된 스크립트-URI의 경로 구성 요소의 일부 선행 부분을 형성합니다. SCRIPT_NAME 값에 PATH_INFO 세그먼트(섹션 4.1.5 참조)가 포함되어 있지 않습니다.

### 4.1.14.  SERVER_NAME

SERVER_NAME 변수는 반드시 클라이언트의 요청이 유도된 서버 호스트의 이름으로 설정되어야 합니다. 대소문자를 구별하는 호스트명이나 네트워크 주소입니다. Script-URI의 호스트 부분을 형성합니다.

```
SERVER_NAME = server-name
server-name = hostname | ipv4-address | ( "[" ipv6-address "]" )
```

배치된 서버는 이 변수에 대해 가능한 값을 한 개 이상 가질 수 있는데, 그건 HTTP 가상 호스트가 동일한 IP 주소를 공유하는 곳입니다. 이 경우 서버는 요청의 Host 헤더 필드의 내용을 사용하여 올바른 가상 호스트를 선택합니다.

### 4.1.15.  SERVER_PORT

SERVER_PORT 변수는 반드시 클라이언트로부터 이 요청을 수신한 TCP/IP 포트 번호로 설정되어야 합니다. 이 값은 Script-URI의 포트 부분에서 사용됩니다.

```
SERVER_PORT = server-port
server-port = 1*digit
```

이 변수는 반드시 설정되어야 하며, 심지어 포트가 스킴에 따른 기본 포트이고 URI에서 생략될 수 있을 때에도 그러합니다.

### 4.1.16.  SERVER_PROTOCOL

SERVER_PROTOCOL 변수는 반드시 이 CGI 요청에 사용된 어플리케이션 계층 프로토콜의 이름과 버전으로 설정되어야 합니다. 이것은 서버가 클라이언트와의 통신에 사용한 프로토콜 버전과 다를 수도 있습니다.

```
SERVER_PROTOCOL   = HTTP-Version | "INCLUDED" | extension-version
HTTP-Version      = "HTTP" "/" 1*digit "." 1*digit
extension-version = protocol [ "/" 1*digit "." 1*digit ]
protocol          = token
```

여기서 '프로토콜'은 서버와 스크립트 사이에 전달되는 일부 정보('프로토콜별' 기능)의 구문을 정의합니다. 대/소문자를 구분하지 않으며 일반적으로 대문자로 나타납니다. 프로토콜은 클라이언트가 서버와 통신하기 위해 사용하는 전체 액세스 메커니즘을 정의하는 스크립트 URI의 스킴 부분과 같지는 않습니다. 예를 들어 "HTTP" 프로토콜로 스크립트에 도달하는 요청이 "https" 스킴을 사용했을 수 있습니다.

SERVER_PROTOCOL로 사용될 수도 있는 잘 알려진 값은 "INCLUDE"이고, 그건 클라이언트 요청의 직접적인 대상이라기 보단 현재 문서가 복합 문서의 일부라는 표시입니다. 스크립트는 이것을 HTTP/1.0 요청으로 다뤄야 합니다.

### 4.1.17.  SERVER_SOFTWARE

SERVER_SOFTWARE 메타변수는 반드시 CGI요청을 만들어내고 게이트웨이를 실행하는 서버 소프트웨어의 이름과 버전으로 설정되어야 합니다. 클라이언트에 보고된 서버 설명과 같아야 합니다. (있다면)

```
SERVER_SOFTWARE = 1*( product | comment )
product         = token [ "/" product-version ]
product-version = token
comment         = "(" *( ctext | comment ) ")"
ctext           = <any TEXT excluding "(" and ")">
```

### 4.1.18.  프로토콜에 특정된 메타변수

서버는 요청으로 온 프로토콜과 스킴에 특정된 메타변수를 설정해야 합니다. 프로토콜에 특정된 변수의 해석은 SERVER_PROTOCOL의 프로토콜 버전에 따라 다릅니다. 서버는 스킴과 프로토콜이 동일하지 않은 경우에 메타변수를 NULL값이 아닌 스킴이름으로 설정할 수도 있습니다. 그런 변수의 존재는 어떤 스킴이 요청에 사용되었는지를 스크립트로 나타냅니다.

이름이 "HTTP_"로 시작하는 메타변수에는 사용되는 프로토콜이 HTTP인 경우 클라이언트 요청 헤더 필드에서 읽은 값이 포함됩니다. HTTP 헤더 필드 이름은 대문자로 변환되고 ```-```가 ```_```로 대체되며 메타변수 이름을 지정하기 위해 "HTTP_"가 미리 작성됩니다. 헤더 데이터는 클라이언트가 보낸 대로 표시하거나 의미론을 변경하지 않는 방식으로 다시 작성할 수 있습니다. 동일한 필드 이름의 헤더 필드가 여러 개 수신되면 서버는 반드시 동일한 의미론을 가진 단일 값으로 다시 작성해야 합니다. 마찬가지로, 여러 줄에 걸쳐 있는 헤더 필드는 반드시 한 줄로 병합해야 합니다. 서버는 필요한 경우 CGI 메타변수에 적합하도록 데이터(예: 문자 집합)의 표현을 반드시 변경해야 합니다.

서버가 수신하는 모든 헤더 필드에 대해 메타변수를 생성할 필요는 없습니다. 특히 '인증'과 같은 인증 정보가 포함된 헤더 필드나 '콘텐츠 길이' 및 '콘텐츠 유형'과 같은 다른 변수의 스크립트에서 사용할 수 있는 헤더 필드를 제거해야 합니다. 서버는 '연결'과 같은 클라이언트 측 통신 문제에만 관련된 헤더 필드를 제거할 수 있습니다.

## 4.2.  요청 메시지 본문(Request message-body)

요청 데이터는 시스템정의 방법에 따라 스크립트에 의해 접근됩니다. 달리 정의된 것이 아니면 이것은 표준입력 파일디스크립터나 파일 핸들을 읽어들임으로서 이뤄집니다.

```
Request-Data   = [ request-body ] [ extension-data ]
request-body   = <CONTENT_LENGTH>OCTET
extension-data = *OCTET
```

CONTENT_LENGTH가 NULL이 아니면 요청 본문과 요청이 제공됩니다. 서버는 스크립트가 읽을 수 있는 바이트를 반드시 최소한으로 해야 합니다. CONTENT_LENGTH 바이트를 읽은 후 서버가 end-of-file 상태 시그널을 보내거나 확장 데이터를 제공할 수 있습니다. 따라서 더 많은 데이터를 사용할 수 있더라도 스크립트는 절대로 CONTENT_LENGTH 바이트 이상을 읽으려고 시도해서는 안 됩니다. 그러나 데이터를 읽을 의무는 없습니다.

NPH(파싱 않는 헤더) 스크립트(섹션 5)의 경우, 서버는 스크립트에 제공된 데이터가 클라이언트가 제공한 데이터와 정확히 일치하고 서버가 변경하지 않았는지 확인해야 합니다.

요청 본문에서는 전송 코드가 지원되지 않으므로 서버는 반드시 메시지 본문에서 이러한 코드를 모두 제거하고 CONTENT_LENGTH를 다시 계산해야 합니다. 이 작업이 가능하지 않으면(예를들어 버퍼링 요구사항이 커서) 서버는 클라이언트 요청을 거부해야 합니다. 또한 메시지 본문에서 내용 코드를 제거할 수도 있습니다.

## 4.3.  요청 메서드(Request Methods)

REQUEST_METHOD 메타변수에서 제공되는 Request Method는 응답을 생성하는 과정에서 스크립트에 의해 적용될 프로세싱 메서드를 식별합니다. 스크립트 작성자는 특정 응용프로그램에 가장 적합한 방법을 구현하도록 선택할 수 있습니다. 스크립트가 지원하지 않는 메서드를 포함한 요청을 수신한다면 오류 처리와 함께 거절해야 합니다 (섹션 6.3.3 참조).

### 4.3.1. GET

GET 메서드는 스크립트가 메타변수 값을 기반으로 문서를 생성해야 한다는 것을 나타냅니다. 관례상 GET 메서드는 '안전'하고 '멱등'하고 문서를 생성하는 것 이상의 의미를 가지지 않아야 합니다.

GET 메서드의 의미는 프로토콜 별 메타변수에 따라 수정될 수도 있고 개정될 수도 있습니다.

### 4.3.2. POST

POST 메서드는, 메타변수 값에 추가적으로, 요청 메시지 본문에 있는 데이터에 기반해서 문서를 처리하고 작성하는 것을 수행하는 스크립트를 요청하는데에 사용됩니다. 흔한 용도는, 스크립트에 의한 데이터베이스의 변경과 같은, 영구적인 적용을 하는 프로세싱을 초기화 하려는 HTML 제출양식입니다

스크립트는 반드시 메시지 본문을 읽기 전에 CONTENT_LENGTH 변수를 확인해야 하고, 처리하기 전에 CONTENT_TYPE값을 확인해야 합니다.

### 4.3.3. HEAD

HEAD 메서드는 스크립트가 응답 메시지 본문을 제공하지 한고 응답 헤더 영역을 반환하기에 충분한 처리를 하도록 요청합니다. HEAD 요청에 대해 스크립트는 절대로 응답 메시지 본문을 제공해서는 안 됩니다. 그렇게 한다면, 스크립트로부터 응답을 읽어들일 때 서버는 메시지 본문을 반드시 버려야 합니다. 

### 4.3.4. 프로토콜에 특정된 메서드(Protocol-Specific Methods)

스크립트는 HTTP/1.1 PUT과 DELETE처럼 어떤 프로토콜에 특정된 메서드를 구현할 수도 있습니다. 그렇게 할 때는 SERVER_PROTOCOL을 확인해야 합니다.

서버는 몇몇 메서드가 스크립트에 적절하지 않거나 허가되지 않았다고 결정할 수도 있고, 메서드 자체를 조정하거나 클라이언트로 오류를 반환할 수도 있습니다.

## 4.4.  스크립트 명령행(The Script Command Line)

몇몇 시스템은 CGI 스크립트에 문자열 배열을 제공하는 메서드를 지원합니다. 이것은 'indexed' HTTP 쿼리에만 사용되며, 그것은 인코딩 안 된 '=' 문자를 포함하지 않는 URI 질의문자열이 있는 GET 또는 HEAD 요청에 의해 식별됩니다. 그러한 요청에 대해, 서버는 query-string을 search-string으로 간주하고 이를 단어로 파싱해야 하고, 다음 규칙을 사용합니다.

```
search-string = search-word *( "+" search-word )
search-word   = 1*schar
schar         = unreserved | escaped | xreserved
xreserved     = ";" | "/" | "?" | ":" | "@" | "&" | "=" | "," |
                "$"
```
파싱 후에 각 search-word는 디코딩된 URL이 되고, 시스템정의 방법에 따라 선택적으로 인코딩 되고 명령행 리스트 인자에 추가됩니다.

서버가 인자 리스트의 한 부분도 생성하지 못하면, 서버는 절대로 어떠한 명령행 정보도 만들어내서는 안 됩니다. 예를 들어 인자의 수가 운영체제나 서버의 제한보다 많거나 단어가 인자로 표현되지 않을 수도 있습니다.

스크립트는 QURERY-STRING 값이 인코딩 안 된 "=" 문자를 포함하는지 확인해야 하고, 그렇다면 명령행 인자를 쓰지 않아야 합니다.

# 5.  NPH 스크립트

## 5.1. 식별

서버는 NPH (Non-Parsed Header, 파싱 안 된 헤더) 스크립트를 지원할 수도 있습니다. 이 스크립트는 서버가 응답 처리에 대한 모든 책임을 전달하는 스크립트입니다.

이 규격은 출력 데이터만으로 NPH 스크립트를 식별할 수 있는 메커니즘을 제공하지 않습니다. 관례로, 그러므로 어떤 특정 스크립트는 한 가지 타입(NPH 또는 CGI)만 제공하고, 그런 이유로 스크립트 자체는 NPH 스크립트로 설명됩니다. NPH가 있는 서버는 NPH 스크립트를 식별할 구현정의 메커니즘을 반드시 제공해야 합니다(아마도 스크립트의 이름이나 위치에 기반한).

## 5.2. NPH 응답

스크립트가 서버 또는 클라이언트로 데이터를 다시 보내려면 시스템정의 방법이 반드시 있어야 합니다. 스크립트는 항상 일부 데이터를 반환해야 합니다. 달리 정의되지 않는 한 이는 기존 CGI 스크립트와 동일합니다.

현재 NPH 스크립트는 HTTP 클라이언트 요청에 대해서만 정의됩니다. (HTTP) NPH 스크립트는 HTTP 규격 [1], [4]의 섹션 6에서 현재 설명된 전체 HTTP 응답 메시지를 반드시 반환해야 합니다. 이 스크립트는 반드시 SERVER_PROTOCOL 변수를 사용하여 응답에 적합한 형식을 결정해야 합니다. 특정 프로토콜 규격을 따를지도 모를 요청에서의 일반 메타변수나 프로토콜 특유의 메타변수 또한 반드시 고려해야 합니다.

서버는 반드시 스크립트 출력이 수정되지 않은 채로 클라이언트로 전송되도록 보장해야 합니다. 이 작업을 수행하려면 스크립트가 헤더 필드에 올바른 문자 집합(미국-ASPI [9] 및 ISO 8859-1 [10](HTTP의 경우)을 사용해야 합니다. 서버는 내부 버퍼링을 최소화하고 전송이 보이지 않는 버퍼링으로 스크립트 출력이 클라이언트로 직접 전송되도록 보장해야 합니다.

구현에서 달리 정의하지 않는 한 스크립트는 클라이언트가 동일한 연결을 통해 추가 요청을 보낼 수 있음을 절대로 응답에 표시해서는 안 됩니다.

# 6.  CGI 응답

## 6.1.  응답 처리

스크립트는 항상 반드시 비어있지 않은 응답을 제공해야 하고, 그러므로 이 데이터를 서버 돌려보내는 시스템정의 메서드가 있습니다. 정의되어있지 않으면 표춘출력 파일디스크립터를 통해 수행합니다.

이 스크립트는 요청을 처리하고 응답을 준비할 때 REQUEST_METHOD 변수를 반드시 확인해야 합니다.

서버는 스크립트로부터 데이터를 수신해야만 하는 제한시간을 구현할 수도 있습니다. 서버 구현에서 이러한 제한시간을 정의하고 제한시간 내에 스크립트로부터 데이터를 수신하지 않으면 서버가 스크립트 프로세스를 종료할 수도 있습니다.

## 6.2.  응답 유형(Response Types)

응답은 메시지 헤더와 메시지 본문으로 구성되며, 빈 줄로 구분됩니다. 메시지 헤더에 하나 이상의 헤더 필드가 있습니다. 본문은 NULL일 수 있습니다.

```
generic-response = 1*header-field NL [ response-body ]
```

스크립트는 문서 응답, 로컬 리디렉션 응답 또는 클라이언트(선택사항으로 문서) 리디렉션 응답 중 하나를 반드시 반환해야 합니다. 아래 응답 정의에서 응답의 헤더 필드 순서는 유의하지 않습니다(BNF에 나타나더라도). 헤더 필드는 섹션 6.3에 정의되어 있습니다.

```
CGI-Response = document-response | local-redir-response | client-redir-response | client-redirdoc-response
```

### 6.2.1. 문서 응답(Document Response)

문서응답에서, CGI 스크립트는 문서를 사용자에게 반환할 수 있고, 응답의 성공 상태를 가리키는 선택적인 오류 코드를 함께 보낼 수 있습니다.

```
document-response = Content-Type [ Status ] *other-field NL
                    response-body
```

스크립트는 Content-Type 헤더 필드를 반드시 반환해야 합니다. Status 헤더 필드는 선택사항이고, 생략된다면 status 200 'OK'로 간주됩니다. 서버는 반드시 클라이언트로의 응답이 응답 프로토콜 버전을 확실히 준수하게끔 스크립트의 출력을 적절하게 수정해야 합니다.

### 6.2.2. 로컬 리디렉션 응답(Client Redirect Response)

CGI 스크립트는 URI경로와 질의문자열('local-pathquery')을 로컬 리소스로 Location 헤더 필드에 반환할 수 있습니다. 이것은 서버에 특정한 경로를 사용하는 요청을 다시 처리해야 한다고 지시합니다.

```
local-redir-response = local-Location NL
```

스크립트는 절대로 다른 헤더 필드나 메시지 본문을 반환해서는 안 되고, 서버는 반드시 응답이 URL을 포함한 요청에 대한 응답으로 생성되었다는 응답을 만들어내야 합니다.

```
scheme "://" server-name ":" server-port local-pathquery
```

### 6.2.3. 클라이언트 리디렉션 응답(Client Redirect Response)

CGI 스크립트는 특정 URI를 사용하는 요청을 다시 처리해야 한다는 것을 클라이언트에 알리기 위해, Location 헤더 필드에 URI 절대경로를 반환할 수 있습니다.

```
client-redir-response = client-Location *extension-field NL
```

스크립트는 서버에서 정의한 CGI 확장 영역을 제외하고는 다른 헤더필드를 절대로 제공하면 안 됩니다. HTTP 클라이언트 요청에 대해, 서버는 반드시 302 'Found' HTTP 응답 메시지를 생성해야 합니다. 

### 6.2.4. 문서로 클라이언트 리디렉션 응답(Client Redirect Response with Document)

CGI스크립트는 클라이언트에게 특정 URI를 사용하는 요청을 다시 처리해야 한다는 것을 나타내기 위해 Location 헤더 필드에 문서를 함께 첨부하여 URI 절대 경로를 반환할 수 있습니다.

```
client-redirdoc-response = client-Location Status Content-Type
                           *other-field NL response-body
```

Status 헤더 필드는 반드시 제공되어야 하고 반드시 302 'Found' 상태값을 포함해야 하고, 또는 확장 코드(클라이언트 리다이렉션을 의미하는 또다른 유효한 상태 코드)를 포함할 수도 있습니다. 서버는 반드시 클라이언트로의 응답이 응답 프로토콜 버전을 준수함을 보증하기 위해 스크립트 출력에 적절한 수정사항을 만들어내야 합니다. 

## 6.3.  응답 헤더 필드(Response Header Fields)

응답 헤더 필드는 CGI 또는 서버에 의해 해석되는 확장 헤더필드이거나, 클라이언트에 반환되는 응답에 포함되는 프로토콜에서 정의하는 헤더 필드입니다. 적어도 CGI 필드 하나는 반드시 제공되어야 합니다. 각 CGI 필드는 절대로 응답 중에 한 번 이상 나타나서는 안 됩니다. 응답 헤더 필드는 문법을 가집니다:

```
header-field    = CGI-field | other-field
CGI-field       = Content-Type | Location | Status
other-field     = protocol-field | extension-field
protocol-field  = generic-field
extension-field = generic-field
generic-field   = field-name ":" [ field-value ] NL
field-name      = token
field-value     = *( field-content | LWSP )
field-content   = *( token | separator | quoted-string )
```

field-name은 대소문자를 구분하지 않습니다. NULL 필드 값은 보내지 않는 필드와 동등합니다. CGI 요청에서의 각 헤더 필드는 반드시 한 줄에 특정되어야 한다는 것을 알아두세요. CGI/1.1은 연속된 행을 지원하지 않습니다. 공백은 ":"과 필드 값 사이에서 허용되고(그러나 필드명과 ":"사이에서는 그렇지 않습니다.), 필드 값의 토큰 사이에서도 그러합니다.

### 6.3.1. Content-Type

Content-Type 응답 필드는 엔터티 본문의 인터넷 미디어 유형[6]을 설정합니다.

```
Content-Type = "Content-Type:" media-type NL
```

엔터티 본문이 반환된다면, 스크립트는 반드시 응답에 Content-Type 필드를 제공해야 합니다. 그렇게 하는 것에 실패한다면, 서버는 정확한 콘텐츠 유형을 알아내려고 하지 않아야 합니다. 값은 charset 매개변수 변경을 제외하고는 클라이언트로 보내져야 합니다.

그렇지 않고 시스템에 별도로 정의되어 있다면, 텍스트 미디어 유형에 대해 charset 기본값은, 프로토콜이 HTTP이면 ISO-8859-1이고 아니면 US-ASCII라고 클라이언트에 의해 간주됩니다. 따라서 스크립트는 charset 매개변수를 포함해야 합니다. 이 이슈를 논하기 위해 3.4.1 섹션(HTTP/1.1 규격)을 보세요

### 6.3.2. Location

Location 헤더 필드는 스크립트가 실제 문서가 아닌 문서에 대한 레퍼런스를 반환한다는 것을 서버에 명시하기 위해 사용됩니다(섹션 6.2.3과 6.2.4 참조). 절대 URI이고(선택적으로 조각 식별자)인데, 클라이언트가 참조된 문서나 로컬 URI 경로(선택적으로 질의문자열)를 가져올 것이라고 나타내고, 서버는 참조된 문서를 가져오고 응답으로서 그것을 반환한다는 것을 나타냅니다.

```
Location        = local-Location | client-Location
client-Location = "Location:" fragment-URI NL
local-Location  = "Location:" local-pathquery NL
fragment-URI    = absoluteURI [ "#" fragment ]
fragment        = *uric
local-pathquery = abs-path [ "?" query-string ]
abs-path        = "/" path-segments
path-segments   = segment *( "/" segment )
segment         = *pchar
pchar           = unreserved | escaped | extra
extra           = ":" | "@" | "&" | "=" | "+" | "$" | ","
```

절대 URI의 문법은 RFC 2396[2]과 RFC 2732[7]에서 지정된 이 문서로 통합됩니다. 유효한 절대 URI는 항상 ":"이 뒤따르는 스킴 이름으로 시작합니다. 스킴 이름은 문자로 시작하고 알파벳과 숫자, "+", "-", "."으로 이어집니다. 로컬 URI 경로와 쿼리는 반드시 절대경로여야 하고, 상대경로나 NULL이 아니어야 하며, 따라서 반드시 "/"로 시작해야 합니다.

요청에 연결된 메시지 본문(예: POST 요청)은 리디렉션 대상인 리소스에서 사용할 수 없을 수도 있습니다.

### 6.3.3. Status

Status 헤더 필드는 스크립트의 요청 처리 성공 수준을 나타내는 3자리 정수로 된 결과코드를 포함합니다.

```
Status         = "Status:" status-code SP reason-phrase NL
status-code    = "200" | "302" | "400" | "501" | extension-code
extension-code = 3digit
reason-phrase  = *TEXT
```

Status 코드 200 'OK'는 성공을 나타내고, 문서 응답에 대해 가정된 기본값 입니다. Status 코드 302 'Found'는 Location 헤더 필드 및 응답 메시지 본문과 함께 쓰입니다. Status 코드 400 'Bad Request'는 못찾는 CONTENT_TYPE처럼 모르는 요청 형식에 대해 쓰일 수도 있습니다. Status 코드 501 'Not Implemented'는 지원되지 않는 REQUEST_METHOD인 경우에 대해 반환될 수도 있습니다.

다른 유효한 상태 코드는 HTTP 규격 섹션 6.1.1[1], [4]과 IANA HTTP Status Code Registry [8]에 나열되어있고, 나열된 것들에 추가로 사용되거나 대체해서 사용될 수도 있습니다. 스크립트는 HTTP/1.1 상태코드를 사용하기 전에 SERVER_PROTOCOL을 확인해야 합니다. 스크립트는 스크립트가 지원하지 않는 HTTP/1.1요청에 대해 405 오류 'Method Not Allowed'로 거부할 수도 있습니다.

오류 상태코드를 반환하는 것은 스크립트 자체의 오류 상태를 의미하지는 않는다는 것을 알아두십시오. 예를들어 서버에 의해 오류처리기로서 호출된 스크립트는 서버의 오류 상태를 적절히 반환해야 합니다.

reason-pharse는 클라이언트로 반환될 오류에 대한 사람의 이해를 위한 설명입니다

### 6.3.4. 프로토콜별 해더 필드(Protocol-Specific Header Fields)

이 스크립트는 SERVER_PROTOCOL(SERVER_PROTOCOL(HTTP/1.0[1] 또는 HTTP/1.1[4]) 규격에 의해 정의된 응답 메시지와 관련된 다른 헤더 필드를 반환할 수도 있습니다. 서버는 반드시 헤더 데이터를 CGI헤더 구문에서 HTTP헤더 구문으로(이 둘이 다를 경우에) 번역해야 합니다. 예를 들어 CGI 스크립트에서 사용되는 개행에 대한 문자 시퀀스(유닉스의 US-ASCII LF같은 것)는 HTTP에서 쓰이는 것(US-ASCII CR 다음에 LF)과 다를 수도 있습니다.

스크립트는 클라이언트측 통신 이슈와 관련된 것과 서버의 클라이언트로 응답을 보내는 성능에 영향을 미칠 수 있는 헤더필드는 절대로 아무 것도 반환해서는 안 됩니다. 서버는 클라이언트에 의해 반환되는 그런 헤더필드는 제거할 수도 있습니다. 스크립트에 의해 반환되는 헤더필드와, 그렇지 않고 스스로 보내는 헤더필드 사이에 나는 충돌은 뭐든지 해결해야 합니다.

### 6.3.5. 확장 헤더 필드(Extension Header Fields)

구현정의 CGI 헤더 필드가 추가로 있을 수 있으며, 필드 이름은 "X-CGI-"로 시작해야 합니다. 서버는 스크립트에서 수신되는, "X- CGI-"로 시작하는 이름을 가지고 인식되지 않는 헤더 필드를 무시하거나 삭제할 수 있습니다.

## 6.4.  응답 메시지 본문(Response Message-Body)

응답 메시지 본문은 서버로부터 클라이언트에 반환될 첨부된 문서입니다. 서버는 반드시 스크립트가 end-of-file 상태로 메시지 본문의 끝이라는 신호를 보낼 때까지 스크립트에 의해 제공된 모든 데이터를 읽어야 합니다. 메시지 본문은 HEAD 요청, transfer-codings가 필요한 경우, content-codings 또는 charset 변환을 제외하고는 수정되지 않은 채로 클라이언트에 보내져야 합니다. 

```
response-body = *OCTET
```

# 7.  시스템 규격(System Specifications)

## 7.1.  AmigaDOS

### 메타변수

메타변수는 동일하게 명명된 환경변수로 스크립트에 전달됩니다. 이것들은 도스 라이브러리 루틴 GetVar()로 접근됩니다. 플래그 인자는 0이어야 합니다. 대소문자는 무시되지만 대소문자를 구분하는 시스템과의 호환을 위해 대문자를 권합니다.

### 현재 작업 디렉토리

스크립트에 대한 현재 작업 디렉토리는 스크립트를 담고있는 디렉토리로 설정됩니다.

### 문자 집합

US-ASCII 문자 집합[9]이 메타변수, 헤더필드와 값 정의에 사용됩니다. 개행(NL) 시퀀스는 LF입니다. 서버는 CR LF도 개행으로 허용해야 합니다.

## 7.2.  UNIX

유닉스 호환 운영체제에 대해, 다음 사항이 정의됩니다.

### 메타변수

메타변수는 동일하게 명명된 환경변수로 스크립트에 전달됩니다. 이것들은 C 라이브러리 루틴 getenv()나 environ 변수로 접근됩니다.

### 명령행

메인으로 보내지는 argc와 argv 인자로 접근됩니다. 이 단어들은 Bourne shell에서 역슬래시로 이스케이프되며, '활성화'되는 어떤 문자를 갖고 있습니다.

### 현재 작업 디렉토리

스크립트에 대한 현재 작업 디렉토리는 스크립트를 담고있는 디렉토리로 설정해야 합니다.

### 문자 집합

NUL을 제외한 US-ASCII 문자 집합[9]은 메타변수, 헤더필드, CHAR 값에 사용됩니다. TEXT 값은 ISO-8859-1을 사용합니다. PATH_TRANSLATED 값은 NUL을 제외한 NUL을 제외한 8-bit 바이트를 포함합니다. 개행 (NL) 시퀀스는 LF입니다. 서버는 개행으로 CR LF도 허용해야 합니다.

## 7.3.  EBCDIC/POSIX

EBCDIC 문자 집합을 사용하는 POSIX 호환 운영체제는 다음 정의를 따릅니다.

### Meta-Variables

메타변수는 동일하게 명명된 환경변수로 스크립트에 전달됩니다. 이것들은 C 라이브러리 루틴 getenv()로 접근됩니다.

### 명령행

메인으로 보내지는 argc와 argv 인자로 접근됩니다. 이 단어들은 Bourne shell에서 역슬래시로 이스케이프되며 '활성화'되는 어떤 문자를 갖고 있습니다.

### 현재 작업 디렉토리

스크립트에 대한 현재 작업 디렉토리는 스크립트를 담고있는 디렉토리로 설정되어야 합니다.

### 문자 집합

NUL을 제외한 IBM1047 문자 집합은 메타변수, 헤더 필드, 값, TEXT 문자열, PATH_TRANSLATED 값에 사용됩니다. 개행(NL) 시퀀스는 LF입니다. 개행 (NL) 시퀀스는 LF입니다. 서버는 개행으로 CR LF도 허용해야 합니다.

### 미디어 유형 문자집합 기본값

텍스트(와 다른 구현정의) 미디어 유형에 대한 문자 집합 기본값은 IBM1047입니다.

# 8.  구현

## 8.1.  서버 권장사항

서버와 CGI 스크립트가 URL 경로(각각 클라이언트 URL 및 PATH_INFO 데이터)의 처리에 일관적일 필요는 없지만, 서버 작성자는 일관성을 적용하고자 할 수 있습니다. 따라서 서버 구현은 다음과 같은 경우에 대한 서버 동작을 지정해야 합니다.

1. 허용되는 경로 세그먼트에 대한 제한, 특히 터미널 이외의 NULL 세그먼트가 허용되는지 여부를 정의합니다.
2. "."이나 ".." 경로 세그먼트에 대한 동작을 정의합니다. 즉, 그것들이 금지되었는지,  통상적인 경로 세그먼트로 취급되었는지, 상대 URL 규격에 맞춰서 해석되었는지 여부를 정의합니다.
3. 경로나 문자열 길이 제한, 서버가 파싱할 헤더필드의 용량 제한을 포함한 어러 구현의 제한을 정의합니다.

## 8.2.  스크립트 권장사항

스크립트가 PATH_INFO 데이터를 처리할 의도가 없다면, (PATH_INFO가 NULL이 아니라면) 요청을 404 Not Found로 거부해야 합니다.

폼의 결과물이 처리중이라면, CONTENT_TYPE이 "application/x-www-form-urlencoded" 또는 "multipart/form-data"인지 확인하십시오. CONTENT_TYPE이 비어있다면, 스크립트는 프로토콜이 지원하는 415 'Unsupported Media Type'오류로 요청을 거부할 수 있습니다.

PATH_INFO, PATH_TRANSLATED, SCRPIT_NAME을 파싱할 때는 스크립트는 빈 경로 세그먼트("//")과 특별한 경로 세그먼트(".", "..")에 유의해야 합니다. 그것들은 또한 운영체제 시스템 콜을 사용하기 전에 경로에서 제거되거나, 요청이 404 'Not Found'로 거부되어야 합니다.

헤더필드를 반환할 때, 스크립트는 CGI 헤더를 되도록 빠르게 보내야 하고, 그것들을 다른 HTTP 헤더 필드에 앞서 보내야 합니다. 서버의 메모리 요구량을 낮추는 데에 도움을 줄 수도 있습니다.

스크립트 작성자는 REMOTE_ADDR과 REMOTE_HOST 메타변수(섹션 4.1.8 및 4.1.9 참조)가 요청의 궁극적인 소스를 식별하지 않을 수도 있다는 것을 알아야 합니다. 그것들은 /서버로의 즉각적인 요청을 위해 클라이언트를 식별합니다. 클라이언트는 프록시, 게이트웨이, 기타 실제 클라이언트 소스를 대신하여 중개 역할을 하는 것일 수도 있습니다.

# 9.  보안관련 고려사항

## 9.1.  안전한 메서드(Safe Methods)

HTTP 규격 [1], [4]의 보안 고려사항에서 논의된 바와 같이, GET 및 HEAD 방법은 '안전'하고 '멱등'해야 한다는 규칙이 확립되었습니다(반복된 요청은 단일 요청과 동일한 효과를 가집니다). 자세한 내용은 RFC 2616 [4] 섹션 9.1을 참조하십시오.

## 9.2.  민감한 정보를 담고있는 헤더 필드(Header Fields Containing Sensitive Information)

일부 http 헤더 필드는 서버가 명시적으로 민감한 정보를 보내지 않아야 한다고 구성하지 않는 한, 민감한 정보를 나를 수도 있습니다. 예를 들어 서버가 스크립트를 기초적인 인증 스킴으로 스크립트를 보호한다면, 클라이언트는 Authorization 헤더 필드에 유저이름과 암호를 담아 보낼지도 모릅니다. 서버는 이 정보를 승인하므로, 안일하게 암호를 HTTP_AUTHORIZATION 메타변수를 통해 보내지 않아야 합니다. 이 점은 Proxy-Authorization 헤더필드와 HTTP_PROXY_AUTHORIZATION 메타변수에 해당하는 부분에도 적용됩니다.

## 9.3.  데이터 프라이버시(Data Privacy)

요청에서의 기밀 데이터는 POST 요청의 일부로서 메시지 본문에 있어야 하고, URI나 메시지 헤더에 있어서는 안 됩니다. 일부 시스템에서 메타변수를 스크립트로 보내는데에 사용되는 환경변수는 다른 스크립트나 사용자에게 보일지도 모릅니다. 게다가 현존하는 많은 서버, 프록시, 클라이언트는 제3자가 볼 수 있는 URI를 영구적으로 기록할 것입니다.

## 9.4.  정보 보안 모델(Information Security Model)

TLS를 사용하는 클라이언트 연결에 대해, 보안 모델은 클라이언트와 서버 사이에 적용하고, 클라이언트와 스크립트 사이에는 아닙니다. TLS 세션을 다루는 것은 서버의 책임이고, 따라서 클라이언트에 인증되는 것은 서버이지, CGI 스크립트가 아닙니다.

이 규격은 스크립트에 대해 스크립트를 호출한 서버를 인증하는 어떠한 메커니즘도 제공하지 않습니다. CGI요청과 응답 메시지에 대해 강제적 무결성은 없습니다.

## 9.5.  서버에 대한 스크립트 인터페이스(Script Interference with the Server)

가장 흔한 CGI 구현은 스크립트를 서버의 자식프로세스로, 서버 프로세스와 같은 유저와 그룹을 써서 호출하는 것입니다. 그러므로 스크립트는 서버 프로세스, 구성, 문서, 로그파일을 방해하지 않아야 합니다.

스크립트가 서버 소프트웨어와 연결된 함수 호출에 의해 실행되었다면(컴파일타임 또는 런타임 모두) 서버의 코어메모리를 보호하기 위한 예방조치나, 신뢰할 수 없는 코드는 실행할 수 없도록 보장하는 예방조치가 취해져야 합니다.

## 9.6.  데이터 길이와 버퍼링에 관한 고려사항(Data Length and Buffering Considerations)

이 규격은 스크립트에 표시되는 메시지 본문의 길이에 제한을 두지 않습니다. 스크립트는 사이즈가 어떻든 정적으로 할당된 버퍼가 전송된 것을 한 번에 담기에 충분하다고 가정해서는 안 됩니다. 오버플로를 신경쓰지 않고 고정된 길이의 버퍼를 사용하면 공격자가 운영체제의 스택-스매싱이나 스택오버플로 취약점을 악용하는 결과를 초래할 수 있습니다. 스크립트는 대량의 수신물을 디스크나 기타 버퍼링 미디어에 스풀할 수 있지만, 대량의 수신물이 급격하게 잇따르는 것은 서비스 상태 거부를 초래할 수도 있습니다. 메시지 본문의 CONTENT_LENGTH가 리소스 허용량보다 큰 경우, 스크립트는 프로토콜에 맞는 오류상태로 응답해야 합니다. 잠재적으로 적용가능한 상태코드는 503 'Service Unavailable' (HTTP/1.0과 HTTP/1.1), 413 'Request Entity Too Large'(HTTP/1.1), 414 'Request-URI Too Large' (HTTP/1.1)을 포함합니다.

스크립트로부터의 CGI 응답을 서버가 다루는 데에 비슷한 고려사항이 적용됩니다. 스크립트에 의해 반환되는 헤더나 메시지 본문의 길이에 아무 제한도 없습니다. 서버는 사이즈가 어떻든 정적으로 할당된 버퍼가 전체 응답을 담기에 충분할 것이라고 가정하지 않아야 합니다.

## 9.7.  Stateless Processing

웹의 상태 비저장 특성은 여러 요청이 하나의 개념적인 웹 트랜잭션을 구성하는 경우에도 각 스크립트 실행 및 리소스 검색을 다른 모든 스크립트와 독립적으로 만듭니다.  따라서 스크립트는 사용자-에이전트가 요청을 제출하는 컨텍스트에 대해 어떠한 가정도 하지 않아야 합니다.  특히 스크립트는 클라이언트에서 얻은 데이터를 검사해야 하고, 정보와 내용 모두에서 유효한지 확인한 후, 다른 애플리케이션이나 명령이나 운영 체제 서비스에 대한 입력과 같은 중요한 용도로 사용할 수 있도록 해야 합니다.  이러한 사용에는 시스템 호출 인수, 데이터베이스 쓰기, 동적으로 평가된 소스 코드, 청구 또는 기타 보안 프로세스에 대한 입력이 포함됩니다.  무효화가 사용자 오류, 논리 오류 또는 악의적인 액션의 결과인지 여부에 관계없이 애플리케이션을 잘못된 입력으로부터 보호하는 것이 중요합니다.

다중 요청 트랜잭션에 관련된 스크립트 작성자는 상태 정보의 유효성 검사에 특히 주의해야 합니다. 그렇지 않으면 안전하다고 간주될 수 있는 제출 부분의 위험 값 대체로 인해 바람직하지 않은 영향이 발생할 수 있습니다. 클라이언트에서 제어하지 않은 이전 트랜잭션 단계(예: 숨겨진 HTML 양식 요소, 쿠키, 임베디드 URL 등)의 데이터를 변경할 때 이 유형의 하위 버전이 발생합니다.

## 9.8.  상대경로(Relative Paths)

서버는 요청 URI의 ".." 경로 세그먼트에 주의해야 합니다. URI가 script-path와 extra-path로 분할되기 전에 요청 URI에서 이러한 문제를 제거하거나 해결해야 합니다. 또는 extra-path를 사용하여 PATH_TRANSLATED를 찾는 경우, 경로 해상도가 예상된 경로 계층 바깥도 번역된 경로를 제공하는 것을 피하도록 주의해야 합니다.

## 9.9.  파싱 안 된 헤더 출력(Non-parsed Header Output)

스크립트가 헤더 출력을 클라이언트가 자체 프로토콜로 처리하도록 파싱되지 않은 채로 반환한다면, 스크립트는 프로토콜에 관한 모든 보안 고려사항을 반드시 다뤄야 합니다.

# 10.  일러두기(Acknowledgements)

이 작업은 'www-talk' 메일링 목록에 대한 논의에서 발생한 기존 CGI 인터페이스를 기반으로 합니다.  특히, Rob McCool, John Franks, Ari Luoten, George Phillips 및 Tony Sanders는 이 인터페이스의 초기 버전을 정의하고 구현하는 노력에 대해 특별한 인정을 받을 자격이 있습니다. 

이 문서는 또한 Chris Adie, Dave Kristol 및 Mike Meyer, 또한 David Morris, Jeremy Madea, Patrick McManus, Adam Donahue, Ross Patterson 및 Harald Albestrand의 논평과 제안으로 인해 큰 혜택을 받았습니다.

# 11.  참조

## 11.1  규범 참조 문헌

[1] Berners-Lee, T., Fielding, R. and H. Frystyk, "Hypertext Transfer Protocol -- HTTP/1.0", RFC 1945, May 1996.

[2] Berners-Lee, T., Fielding, R. and L. Masinter, "Uniform Resource Identifiers (URI) : Generic Syntax", RFC 2396, August 1998.

[3] Bradner, S., "Key words for use in RFCs to Indicate Requirements Levels", BCP 14, RFC 2119, March 1997.

[4] Fielding, R., Gettys, J., Mogul, J., Frystyk, H., Masinter, L., Leach, P., and T. Berners-Lee, "Hypertext Transfer Protocol -- HTTP/1.1", RFC 2616, June 1999.

[5] Franks, J., Hallam-Baker, P., Hostetler, J., Lawrence, S., Leach, P., Luotonen, A., and L. Stewart, "HTTP Authentication: Basic and Digest Access Authentication", RFC 2617, June 1999.

[6] Freed, N. and N. Borenstein, "Multipurpose Internet Mail Extensions (MIME) Part Two: Media Types", RFC 2046, November 1996.

[7] Hinden, R., Carpenter, B., and L. Masinter, "Format for Literal IPv6 Addresses in URL's", RFC 2732, December 1999.

[8] "HTTP Status Code Registry", http://www.iana.org/assignments/http-status-codes, IANA.

[9] "Information Systems -- Coded Character Sets -- 7-bit American Standard Code for Information Interchange (7-Bit ASCII)", ANSI INCITS.4-1986 (R2002).

[10] "Information technology -- 8-bit single-byte coded graphic character sets -- Part 1: Latin alphabet No. 1", ISO/IEC 8859-1:1998.

## 11.2.  저명한 참고 문헌

[11] Berners-Lee, T., "Universal Resource Identifiers in WWW: A Unifying Syntax for the Expression of Names and Addresses of Objects on the Network as used in the World-Wide Web", RFC 1630, June 1994.

[12] Braden, R., Ed., "Requirements for Internet Hosts -- Application and Support", STD 3, RFC 1123, October 1989.

[13] Crocker, D., "Standard for the Format of ARPA Internet Text Messages", STD 11, RFC 822, August 1982.

[14] Dierks, T. and C. Allen, "The TLS Protocol Version 1.0", RFC 2246, January 1999.

[15] Hinden R. and S. Deering, "Internet Protocol Version 6 (IPv6) Addressing Architecture", RFC 3513, April 2003.

[16] Masinter, L., "Returning Values from Forms: multipart/form-data", RFC 2388, August 1998.

[17] Mockapetris, P., "Domain Names - Concepts and Facilities", STD 13, RFC 1034, November 1987.

[18] Raggett, D., Le Hors, A., and I. Jacobs, Eds., "HTML 4.01 Specification", W3C Recommendation December 1999, http://www.w3.org/TR/html401/.

[19] Rescola, E. "HTTP Over TLS", RFC 2818, May 2000.

[20] St. Johns, M., "Identification Protocol", RFC 1413, February 1993.

[21] IBM National Language Support Reference Manual Volume 2, SE09-8002-01, March 1990.

[22] "The Common Gateway Interface", http://hoohoo.ncsa.uiuc.edu/cgi/, NCSA, University of Illinois.

# 12.  저자의 주소

   David Robinson  
   The Apache Software Foundation  
   EMail: drtr@apache.org  
   Ken A. L. Coar  
   The Apache Software Foundation  
   EMail: coar@apache.org

# 13.  완전한 저작권 고지

Copyright (C) The Internet Society (2004). 본 문서는 BCP 78 및 www.rfc-editor.org에 포함된 권한, 라이센스 및 제한 사항을 준수하며, 이에 명시된 경우를 제외하고 작성자는 모든 권한을 보유합니다.

본 문서와 여기에 포함된 정보는 "있는 그대로" 제공되며, 기여자, 자신이 대표하거나 후원하는 조직, 인터넷 사회 및 관련 엔지니어링 태스크 포스는 본 문서에 포함된 정보의 사용에 대한 보증을 포함하되 이에 국한되지 않는 모든 명시적 또는 묵시적 보증을 거부합니다. 상품성 또는 특정 목적에 대한 적합성에 대한 권리 또는 묵시적 보증을 침해하지 않습니다.

### 지적 재산.

IETF는 이 문서에 기술된 기술의 구현 또는 사용과 관련된 것으로 주장될 수 있는 지적재산권이나 그 밖의 권리의 유효성과 범위에 관한 입장을 취하지 않으며, 그러한 권리에 따라 면허를 사용할 수 있거나 사용할 수 없는 정도도 아니다.이러한 권리를 식별하기 위한 노력을 기울입니다. ISOC 문서의 권리와 관련된 ISOC 절차에 대한 정보는 BCP 78 및 BCP 79에서 확인할 수 있습니다.

IETF 사무국에 대한 IPR 공시의 사본과 이용 가능한 라이선스의 보증 또는 구현자나 이 규격의 사용자가 그러한 독점적 권리를 사용하기 위한 일반 라이선스 또는 허가를 얻기 위한 시도의 결과는 IETF 온라인 IPR 저장소 http://www.ietf.org/ipr에서 얻을 수 있습니다.

IETF는 이 표준을 구현하는 데 필요한 기술을 포괄할 수 있는 저작권, 특허 또는 특허 출원 또는 기타 독점적 권리를 보유할 수 있는 모든 이해 당사자를 초대합니다. 이 정보를 IETF ietfipr@ietf.org로 보내주시기 바랍니다.

### 일러두기

RFC 편집기 기능에 대한 기금은 현재 인터넷 협회에 의해 제공됩니다.
